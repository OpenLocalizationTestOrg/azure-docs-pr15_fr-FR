<properties 
    pageTitle="Code tampon en anneau XEvent de base de données SQL | Microsoft Azure" 
    description="Fournit un exemple de code Transact-SQL qui a créé rapide et facile à l’aide de la cible du tampon en anneau, dans la base de données SQL Azure." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Anneau de code cible tampon étendus événements dans la base de données SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Vous souhaitez un exemple de code complet pour le meilleur moyen rapide des États et capture des données pour un événement étendu lors d’un test. La cible plus simple pour les données d’événement étendu est la [mémoire tampon d’anneau cible](http://msdn.microsoft.com/library/ff878182.aspx).


Cette rubrique présente un exemple de code Transact-SQL qui :


1. Crée une table avec des données pour montrer avec.

2. Crée une session pour un événement étendu existant, à savoir **sqlserver.sql_statement_starting**.
    - L’événement est limitée aux instructions SQL contenant une chaîne de mise à jour particulière : **instruction LIKE '% mise à jour tabEmployee %'**.
    - Choisit envoyer la sortie de l’événement à une cible de type tampon en anneau, à savoir **package0.ring_buffer**.

3. Démarre la session d’événements.

4. Problèmes de quelques instructions SQL UPDATE simples.

5. Émet un SQL SELECT pour récupérer des événements sortie à partir de la mémoire tampon simultanée.
    - **Sys.dm_xe_database_session_targets** et autres vues de gestion dynamiques (DMV) sont jointes.

6. Arrête la session d’événements.

7. Supprime la cible tampon en anneau, afin de libérer ses ressources.

8. Supprime la session d’événements et de la table démo.


## <a name="prerequisites"></a>Conditions préalables


- Un compte Azure et abonnement. Vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).


- Les bases de données que vous pouvez créer un tableau dans.
 - Vous pouvez également vous pouvez [créer une base de données de la démonstration **AdventureWorksLT** ](sql-database-get-started.md) en minutes.


- SQL Server Management Studio (ssms.exe), dans l’idéal sa dernière version mise à jour mensuelles. Vous pouvez télécharger la dernière ssms.exe à partir de :
 - Rubrique [Télécharger SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Un lien direct vers le téléchargement.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Exemple de code


Avec peu de modifications, l’exemple de code tampon en anneau suivant peut être exécuté sur base de données SQL Azure ou Microsoft SQL Server. La différence est la présence du nœud _basededonnées le nom des vues de gestion dynamiques (DMV), un utilisée dans la clause FROM à l’étape 5. Par exemple :

- Sys.dm_xe**_basededonnées**_session_targets
- Sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Contenu du tampon simultanée


Nous avons utilisé ssms.exe pour exécuter l’exemple de code.


Pour afficher les résultats, nous vous cliquez sur la cellule sous l' en-tête de colonne **target_data_XML**.

Puis dans le volet résultats nous avez cliqué sur la cellule sous l' en-tête de colonne **target_data_XML**. Cliquez sur créé un autre onglet fichier dans ssms.exe dans lequel le contenu de la cellule de résultat a été affiché, au format XML.


Le résultat est indiqué dans le bloc suivant. Il semble long, mais il est juste deux **<event>** éléments.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Libérer des ressources utilisées par votre tampon en anneau


Lorsque vous avez terminé avec votre tampon en anneau, vous pouvez supprimer et libérer ses ressources émettre un **ALTER** comme suit :


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


La définition de votre session événement est mis à jour, mais pas supprimée. Plus tard, vous pouvez ajouter une autre instance de la mémoire tampon simultanée à votre session événement :


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Plus d’informations


Le sujet principal pour les événements étendus sur base de données SQL Azure est la suivante :


- [Considérations relatives à l’événement étendues dans la base de données SQL](sql-database-xevent-db-diff-from-svr.md), et contraste certains aspects des événements étendus différentes entre la base de données SQL Azure par rapport à Microsoft SQL Server.


Autres rubriques d’exemples de code pour les événements étendus sont disponibles sur les liens suivants. Toutefois, vous devez vérifier régulièrement tout exemple pour voir si l’échantillon cible Microsoft SQL Server et celles de la base de données SQL Azure. Vous pouvez décider si des modifications mineures sont nécessaires pour exécuter l’exemple.


- Exemple de code de base de données SQL Azure : [code cible de fichier d’événements pour les événements étendus dans la base de données SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
