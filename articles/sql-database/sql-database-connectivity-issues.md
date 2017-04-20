<properties
    pageTitle="Corriger une erreur de connexion SQL, une erreur temporaire | Microsoft Azure"
    description="Découvrez comment résoudre les problèmes, diagnostiquer et d’éviter une erreur de connexion SQL ou transitoires dans la base de données SQL Azure. "
    keywords="connexion SQL, chaîne de connexion, problèmes de connexion, erreur temporaire, erreur de connexion"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Résoudre les problèmes, diagnostiquer et d’éviter les erreurs de connexion SQL et erreurs transitoires pour la base de données SQL

Cet article décrit comment éviter, résoudre les problèmes, diagnostiquer et réduire les erreurs de connexion et des erreurs transitoires votre application cliente rencontre lorsqu’il interagit avec la base de données SQL Azure. Découvrez comment configurer une logique de nouvelle tentative, générer la chaîne de connexion et ajuster les autres paramètres de connexion.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Transitoires erreurs (erreurs transitoires)

Une erreur temporaire - également, transitoire défaillance - comporte une cause sous-jacente qui résoudra bientôt lui-même. Un occasionnellement des erreurs transitoires se produit lorsque le système Azure rapidement entraîne un décalage ressources matérielles pour optimiser l’équilibrage de charge diverses charges de travail. Plupart de ces événements reconfiguration souvent complet en moins de 60 secondes. Pendant cet intervalle de temps reconfiguration, vous pouvez avoir des problèmes de connectivité à la base de données SQL Azure. Applications qui se connectent à la base de données SQL Azure doivent être générées pour attendre ces erreurs transitoires, traitez-les en mettant en œuvre une logique de nouvelle tentative dans leur code au lieu de leur exposition aux utilisateurs en fonction des erreurs d’application.

Si votre programme client utilise ADO.NET, votre programme est informé l’erreur transitoire par la lever d’une **SqlException**. La propriété **Number** peut être comparée à la liste des erreurs transitoires près du haut de la rubrique : [codes d’erreur SQL pour les applications clientes de base de données SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connexion et commande

Vous essayez de vous reconnecter SQL ou établir à nouveau, selon les éléments suivants :

* **Une erreur temporaire se produit au cours d’une tentative de connexion**: la connexion doit être retentée après retarder pendant quelques secondes.

* **Une erreur temporaire se produit au cours d’une commande de la requête SQL**: la commande ne doit pas être retentée immédiatement. En revanche, après un délai, établir la connexion doit être récemment. Puis la commande peut être retentée.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Logique d’erreurs transitoires de nouvelle tentative


Les programmes clients qui parfois rencontrer une erreur temporaire sont plus performantes grâce aux lorsqu’ils contiennent une logique de nouvelle tentative.


Lorsque votre programme communique avec la base de données SQL Azure via un logiciels intermédiaires fête 3e, inquire avec le fournisseur si logiciels intermédiaires contient une logique de nouvelle tentative d’erreurs transitoires.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principes de nouvelles tentatives


- Une tentative d’ouverture d’une connexion doit être retentée si l’erreur est temporaire.


- Une instruction SQL SELECT échoue avec une erreur transitoire ne doit pas être relancée directement.
 - À la place, établir une nouvelle connexion, puis recommencez la sélectionner.


- Lorsqu’une instruction SQL UPDATE échoue avec une erreur temporaire, une nouvelle connexion convient avant nouvelle tentative de la mise à jour.
 - La logique de nouvelle tentative doit s’assurer que la transaction de base de données entière terminée, soit que la transaction entière est annulée.


#### <a name="other-considerations-for-retry"></a>Autres considérations relatives à de nouvelles tentatives


- Un programme de commandes qui est démarré automatiquement après les heures de travail et qui se termine avant matin, peut-elle très patients avec long les intervalles de temps entre chaque sa tentative.


- Un programme de l’interface utilisateur doit tenir compte de la tendance d’abandonner après une attente trop longue.
 - Toutefois, la solution ne doit pas être tentative après quelques secondes, étant donné que cette stratégie peut engorger le système avec des requêtes.


#### <a name="interval-increase-between-retries"></a>Augmentation de l’intervalle entre les tentatives



Nous vous recommandons de retarder pendant 5 secondes avant votre première tentative. Nouvelle tentative après un délai plus court que risques 5 secondes le service en nuage est surchargé. Pour chaque tentatives suivantes délai doit croissance exponentielle, avec un maximum de 60 secondes.

Une discussion de la *période de blocage* pour les clients qui utilisent ADO.NET est disponible dans [Le regroupement de connexion SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Vous souhaiterez également définir le nombre maximal de nouvelles tentatives avant que le programme s’arrête automatiquement.


#### <a name="code-samples-with-retry-logic"></a>Exemples de code avec une logique de nouvelle tentative


Exemples de code avec une logique de nouvelle tentative, dans une variété de langages de programmation, sont disponibles dans la page :

- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Tester votre logique des nouvelles tentatives


Pour tester votre logique des nouvelles tentatives, vous devez simuler ou génèrent une erreur que vous pouvez corriger pendant que votre programme est toujours en cours d’exécution.


##### <a name="test-by-disconnecting-from-the-network"></a>Tester en déconnectant à partir du réseau


La première que vous pouvez tester votre logique des nouvelles tentatives consiste pour vous déconnecter de l’ordinateur client à partir du réseau pendant l’exécution du programme. L’erreur seront :

- **SqlException.Number** = 11001
- Message : « aucun hôte n’est connu »


Dans le cadre de la première tentative, votre programme peut corriger la faute d’orthographe et puis tentez de vous connecter.


Pour effectuer cette opération pratique, vous déconnectez votre ordinateur à partir du réseau avant de démarrer votre programme. Puis votre programme reconnaît un paramètre d’exécution qui provoque le programme :

1. Ajouter temporairement 11001 à sa liste des messages d’erreur à considérer comme transitoires.
2. Essayez de sa première connexion comme d’habitude.
3. Une fois que l’erreur est détectée, supprimez 11001 dans la liste.
4. Afficher un message indiquant l’utilisateur à Branchez l’ordinateur sur le réseau.
 - Suspendre davantage l’exécution à l’aide de la méthode **Console.ReadLine** ou une boîte de dialogue avec un bouton OK. L’utilisateur appuie sur la touche ENTRÉE après que l’ordinateur connecté au réseau.
5. Réessayer de se connecter, digne succès.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testez en faute d’orthographe dans le nom de la base de données lors de la connexion


Votre programme peut avez intentionnellement mal orthographié le nom d’utilisateur avant la première tentative de connexion. L’erreur seront :

- **SqlException.Number** = 18456
- Message : « échouée de la connexion pour l’utilisateur 'WRONG_MyUserName'. »


Dans le cadre de la première tentative, votre programme peut corriger la faute d’orthographe et puis tentez de vous connecter.


Pour effectuer cette opération pratique, votre programme pu reconnaître un paramètre d’exécution qui provoque le programme :

1. Ajouter temporairement 18456 à sa liste des messages d’erreur à considérer comme transitoires.
2. Ajoutez intentionnellement 'WRONG_' au nom d’utilisateur.
3. Une fois que l’erreur est détectée, supprimez 18456 dans la liste.
4. Supprimez 'WRONG_' du nom d’utilisateur.
5. Réessayer de se connecter, digne succès.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Paramètres de SqlConnection .NET de nouvelle tentative de connexion


Si votre programme client se connecte pour la base de données SQL Azure à l’aide du .NET Framework classe **System.Data.SqlClient.SqlConnection**, vous devez utiliser .NET 4.6.1 ou version ultérieure afin que vous pouvez tirer parti de son fonctionnalité de nouvelle tentative de connexion. Détails de la fonctionnalité sont [ici](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quand vous créez la [chaîne de connexion](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pour votre objet **SqlConnection** , vous devez coordonner les valeurs entre les paramètres suivants :

- ConnectRetryCount &nbsp; &nbsp; *(valeur par défaut est 1. Plage est égal à 0 et 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(valeur par défaut est 1 seconde. La plage est 1 à 60)*
- Délai de connexion &nbsp; &nbsp; *(valeur par défaut est 15 secondes. Est comprise entre 0 et 2147483647)*


Plus précisément, vos valeurs choisies Vérifiez la true l’égalité suivants :

- Délai de connexion = ConnectRetryCount * ConnectionRetryInterval

Par exemple, si le nombre = 3 et intervalle = 10 secondes, un délai d’expiration seules les 29 secondes pas assez donnent le système suffisamment de temps à son réessayer 3e et dernière connexion pour : 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connexion et commande


Les paramètres **ConnectRetryCount** et **ConnectRetryInterval** permettent de votre objet **SqlConnection** recommencez l’opération de se connecter sans indiquant ou déranger votre programme, tel que rendre le contrôle à votre programme. Les tentatives peuvent se produire dans les situations suivantes :

- appel de la méthode mySqlConnection.Open
- appel de la méthode mySqlConnection.Execute

Il existe plusieurs méthodes. Si une erreur temporaire se produit alors que votre *requête* est en cours d’exécution, votre objet **SqlConnection** ne fait pas nouvelle tentative l’opération de connexion, et il certainement ne pas de nouvelle tentative votre requête. Toutefois, **SqlConnection** vérifie très rapidement la connexion avant d’envoyer votre requête pour l’exécution. Si la vérification rapide détecte un problème de connexion, **SqlConnection** tentatives l’opération de connexion. Si la nouvelle tentative a réussi, votre requête est envoyé pour l’exécution.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount doit être combiné avec logique de nouvelle tentative d’application ?

Supposons que votre application possède une personnalisé robuste logique. Il peut reprendre l’opération de connexion 4 heures. Si vous ajoutez **ConnectRetryInterval** et **ConnectRetryCount** = 3 à la chaîne de connexion, vous augmente le nombre de tentatives à 4 * 3 = 12 tentatives. Vous ne pourriez pas l’utiliser cet un grand nombre de tentatives.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Connexions à la base de données SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Connexion : Chaîne de connexion


La chaîne de connexion nécessaire pour vous connecter à la base de données SQL Azure est légèrement différente de la chaîne de connexion à Microsoft SQL Server. Vous pouvez copier la chaîne de connexion pour votre base de données à partir du [Portail Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connexion : Adresse IP


Vous devez configurer le serveur de base de données SQL pour accepter les communications à partir de l’adresse IP de l’ordinateur qui héberge votre programme client. Pour cela, en modifiant les paramètres du pare-feu via le [Portail Azure](https://portal.azure.com/).


Si vous oubliez configurer l’adresse IP, votre programme échouera avec un message d’erreur pratique indiquant l’adresse IP nécessaire.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Pour plus d’informations, voir : [Comment : configurer les paramètres de pare-feu sur base de données SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connexion : Ports


Vous devez généralement uniquement pour vous assurer que le port 1433 est ouvert pour les communications sortantes, sur l’ordinateur hébergeant programme client.


Par exemple, si votre programme client est hébergé sur un ordinateur Windows, le pare-feu Windows sur l’hôte de vous permet d’ouvrir le port 1433 :


1. Ouvrez le panneau de configuration
2. &gt;Tous les éléments du Panneau de contrôle
3. &gt;Pare-feu Windows
4. &gt;Paramètres avancés
5. &gt;Règles de trafic sortant
6. &gt;Actions
7. &gt;Nouvelle règle


Si votre programme client est hébergé sur une Azure machine virtuelle (), vous devez lire :<br/>[Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Pour les informations générales sur cofiguration de ports et l’adresse IP, voir : [pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Connexion : ADO.NET 4.6.1


Si votre programme utilise les classes ADO.NET comme **System.Data.SqlClient.SqlConnection** pour vous connecter à la base de données SQL Azure, nous vous conseillons d’utiliser .NET Framework version 4.6.1 ou une version ultérieure.


ADO.NET 4.6.1 :

- Pour la base de données SQL Azure, il est fiabilité améliorée lorsque vous ouvrez une connexion en utilisant la méthode **SqlConnection.Open** . La méthode **Open** intègre désormais une meilleures mécanismes de réessayer effort en réponse aux défaillances temporaires, pour certaines erreurs dans le délai de connexion.
- Prend en charge le regroupement de connexion. Cela inclut une vérification efficace que l’objet de connexion vous offre votre programme fonctionne.



Lorsque vous utilisez un objet de connexion à partir d’un pool de connexion, nous vous recommandons de que votre programme fermer temporairement la connexion lorsque vous l’utilisez pas immédiatement. Rouvrir une connexion n’est pas la façon de créer une nouvelle connexion est coûteux.


Si vous utilisez ADO.NET 4.0 ou version antérieure, nous vous recommandons de mettre à niveau vers la dernière ADO.NET.

- À partir de novembre 2015, vous pouvez [Télécharger ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostics

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostics : Tester si utilitaires peuvent se connecter


Si votre programme ne parvient pas à se connecter à la base de données SQL Azure, une option de diagnostic consiste à essayer de vous connecter avec un programme utilitaire. Dans l’idéal l’utilitaire serait se connecter à l’aide de la même bibliothèque que votre programme utilise.


Sur n’importe quel ordinateur Windows, vous pouvez essayer ces utilitaires :

- SQL Server Management Studio (ssms.exe), qui se connecte à l’aide d’ADO.NET.
- SQLCMD.exe, qui se connecte à l’aide de [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Une fois connecté, vérifier si une requête SQL SELECT courte fonctionne.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostics : Vérifier les ports ouverts


Supposons que vous soupçonnez que tentatives de connexion échoue en raison de problèmes de port. Sur votre ordinateur, vous pouvez exécuter un utilitaire de rapports sur les configurations de ports.


Sous Linux utilitaires suivants peuvent être utiles :

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Modifiez la valeur exemple est votre adresse IP).


Sous Windows la [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilitaire peut-être être utile. Voici une exécution exemple qui interrogées la situation port sur un serveur de base de données SQL Azure, et qui a été exécuté sur un ordinateur portable :


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostic : Journaux vos erreurs


Un problème intermittent est parfois mieux diagnostiquer par détection d’un modèle général sur jours ou semaines.


Votre client peut aider à un diagnostic par la journalisation de toutes les erreurs qu’il rencontre. Vous serez peut-être en mesure de faire correspondre les entrées du journal avec les données d’erreur de base de données SQL Azure se connecte lui-même en interne.


Entreprise bibliothèque 6 (EntLib60) offre classes .NET gérées pour aider avec l’enregistrement :

- [5 - aussi aisée qu’entrant désactiver un journal : à l’aide du bloc d’Application de journalisation](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostics : Examinez les journaux système pour les erreurs


Voici quelques instructions Transact-SQL SELECT cette requête les journaux d’erreur et d’autres informations.


| Requête du journal | Description |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La vue [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) propose des informations sur les événements individuels, y compris celles qui peuvent entraîner des erreurs transitoires ou défaillances.<br/><br/>Préférence vous pouvez faire correspondre les valeurs **start_time** ou **end_time** des informations sur lorsque votre programme client rencontré des problèmes.<br/><br/>**Conseil :** Vous devez vous connecter à la base de données **principale** pour effectuer cette opération. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La vue [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) propose agrégés comptages des types d’événements, pour des diagnostics supplémentaires.<br/><br/>**Conseil :** Vous devez vous connecter à la base de données **principale** pour effectuer cette opération. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostics : Rechercher les événements liés au problème dans le journal de base de données SQL


Vous pouvez rechercher des entrées sur les événements de problème dans le journal de base de données SQL Azure. Essayez de l’instruction Transact-SQL SELECT suivante dans la base de données **principale** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Quelques lignes renvoyées à partir de sys.fn_xe_telemetry_blob_target_read_file


L’option suivant est quoi peut ressembler une ligne renvoyée. Les valeurs null affichées ne sont pas souvent null dans d’autres lignes.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Bibliothèque d’entreprise 6


Entreprise bibliothèque 6 (EntLib60) est une structure de classes .NET qui vous permet de mettre en œuvre des clients robustes des services en nuage, y compris le service de base de données SQL Azure. Vous pouvez rechercher des rubriques dédiés à chaque zone dans laquelle EntLib60 peut vous aider en visitant première :

- [Bibliothèque Enterprise 6 – avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Logique des nouvelles tentatives de gestion des erreurs transitoires est une zone dans laquelle EntLib60 peut vous aider :

- [4 - perseverance, code Secret de tous les luttes : à l’aide du bloc d’Application de gestion des erreurs transitoires](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Le code source pour EntLib60 est disponible pour le public [Télécharger](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft n’envisage pas à effectuer d’autres mises à jour de la fonctionnalité ou mises à jour de la maintenance pour EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes EntLib60 les erreurs transitoires et réessayer


Les classes EntLib60 suivantes sont particulièrement utiles pour la logique de nouvelle tentative. Tous ces sont en cours, ou autres sous l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*Dans l’espace de noms* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- Classe **RetryPolicy**
 - Méthode **ExecuteAction**


- Classe **ExponentialBackoff**


- Classe **SqlDatabaseTransientErrorDetectionStrategy**


- Classe **ReliableSqlConnection**
 - Méthode **ExecuteCommand**


Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Classe **AlwaysTransientErrorDetectionStrategy**

- Classe **NeverTransientErrorDetectionStrategy**


Voici des liens vers des informations sur EntLib60 :

- Gratuit [téléchargement du carnet de : Guide du développeur pour Microsoft Enterprise bibliothèque, 2e édition](http://www.microsoft.com/download/details.aspx?id=41145)

- Meilleures pratiques : [Réessayer instructions générales fournies pour](../best-practices-retry-general.md) contient une présentation détaillée excellente de logique de recherche.

- Téléchargement NuGet [d’Enterprise Library - bloc d’application de gestion de pannes transitoires 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60 : Le bloc de journalisation


- Le bloc de journalisation est une solution hautement flexible et configurable qui vous permet de :
 - Créer et stocker les messages de journal dans un large éventail d’emplacements.
 - Classer et filtrer les messages.
 - Recueillir les informations contextuelles exigences en termes de journalisation utiles pour le débogage et le traçage, ainsi que pour l’audit et général.


- Le bloc de journalisation résume la fonctionnalité de journalisation à partir de la destination du journal afin que le code de l’application est cohérent, quel que soit l’emplacement et le type de la banque de journalisation cible.


Pour plus d’informations, consultez : [5 - comme facile comme entrant désactiver un journal : à l’aide du bloc d’Application journalisation](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Code source la méthode EntLib60 IsTransient


Ensuite, à partir de la classe **SqlDatabaseTransientErrorDetectionStrategy** , est le code source c# pour la méthode **IsTransient** . Le code source clarifie les erreurs ont été considéré comme étant transitoires et digne de nouvelle tentative, à partir d’avril 2013.

Nombreuses lignes **//comment** ont été supprimés à partir de cette copie pour mettre en évidence la lisibilité.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les autres problèmes de connexion de base de données SQL Azure courants, consultez [résoudre des problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [SQL Server regroupement de connexions (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Nouvel essai* est qu'une Apache 2.0 universelle bibliothèque nouvelle tentative, écrite dans **Python**, pour simplifier la tâche de l’ajout de comportement des nouvelles tentatives à sortes d’une licence.](https://pypi.python.org/pypi/retrying)
