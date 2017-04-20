<properties
   pageTitle="Limites de capacité SQL Data Warehouse | Microsoft Azure"
   description="Valeurs maximales de connexions, les bases de données, les tables et les requêtes SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacité Data Warehouse SQL

Les tableaux suivants contiennent les valeurs maximales autorisées pour les divers composants du magasin de données SQL Azure.


## <a name="workload-management"></a>Gestion de la charge de travail

| Catégorie            | Description                                       | Valeur maximale            |
| :------------------ | :------------------------------------------------ | :----------------- |
| [Unités de magasin de données (DWU)][]| Max DWU pour un seul Data Warehouse SQL | 6000               |
| [Unités de magasin de données (DWU)][]| Max DWU pour un seul SQL server         | 6000 par défaut<br/><br/> Par défaut, chaque SQL server (par exemple, myserver.database.windows.net) a un Quota DTU de 45 000 permettant de devenir DWU 6000. Ce quota est simplement une limite de sécurité. Vous pouvez augmenter votre quota en [créant un tickets de support][] et en sélectionnant *Quota* comme type de requête.  Pour calculer votre DTU doit, multipliez le 7.5 par le total que DWU nécessaire. Vous pouvez afficher votre consommation DTU actuelle à partir de la carte de serveur SQL dans le portail. Compter les bases de données en pause et reprises vers le quota DTU. |
| Connexion de base de données | Sessions ouvertes simultanées                          | 1024<br/><br/>Nous prenons en charge un maximum de connexions actives 1024, chacun d'entre eux peut envoyer des demandes à une base de données SQL Data Warehouse en même temps. Notez qu’il existe des limites du nombre de requêtes pouvant être exécutées en réalité simultanément. En cas de dépassement de la limite de la concurrence, la demande passe en une file interne où il attend d’être traité.|
| Connexion de base de données | Mémoire maximale pour les instructions préparées            | 20 MO              |
| [Gestion de la charge de travail][] | Nombre maximal de requêtes simultané                    | 32<br/><br/> Par défaut, Data Warehouse SQL peuvent exécuter un maximum de 32 requêtes simultanées et files d’attente restant les requêtes.<br/><br/>Le niveau de concurrence peut-être diminuer lorsque des utilisateurs sont affectés à une classe supérieure de la ressource ou SQL Data Warehouse est configuré avec DWU faible. Certaines requêtes, comme les requêtes de vue, sont toujours autorisés à exécuter.|
| [Tempdb][]          | Taille maximale de Tempdb                                | 399 de Go par DW100. Par conséquent, en DWU1000 Tempdb est dimensionné à 3,99 to |


## <a name="database-objects"></a>Objets de base de données

| Catégorie          | Description                                  | Valeur maximale            |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de données          | Taille maximale                                     | 240 To compressés sur disque<br/><br/>Cet espace est indépendant de l’espace tempdb ou journal, et par conséquent cet espace dédié aux tables permanentes.  Compression columnstore groupé est estimée à 5 X.  Cette compression permet de la base de données atteigne environ 1 po lorsque toutes les tables sont columnstore groupé (le type de tableau par défaut).|
| Table             | Taille maximale                                     | 60 To compressé sur disque   |
| Table             | Tables par base de données                          | 2 milliards          |
| Table             | Colonnes par table                            | 1024 colonnes       |
| Table             | Octets par colonne                             | En fonction du [type de données][]de la colonne.  La limite est 8 000 pour les types de données car, 4 000 pour nvarchar ou 2 Go pour les types de données MAX.|
| Table             | Octets par ligne, taille définie                  | 8060 octets<br/><br/>Le nombre d’octets par ligne est calculé dans la même manière qu’il est pour SQL Server grâce à la compression de page. Comme SQL Server, SQL Data Warehouse prend en charge le stockage de dépassement de lignes qui permet de **colonnes de longueur variable** à être poussé hors ligne. Lorsque les lignes de longueurs sont déplacées hors ligne, uniquement 24 octets racine est stockée dans l’enregistrement principal. Pour plus d’informations, voir l’article MSDN [dépassement de lignes données supérieure à 8 Ko][] .|
| Table             | Partitions par table                    | 15 000<br/><br/>Pour optimiser les performances, nous vous recommandons de réduire le nombre de partitions vous avez besoin tout en prenant en charge des besoins de votre entreprise. Au fur et à mesure du nombre de partitions, la charge pour les opérations de langage DDL (Data Definition) et de langage de Manipulation de données (DML) se développe et provoque baisse des performances.|
| Table             | Caractères par valeur limite partition.| et 4 000 |
| Index             | Index non cluster par table.        | 999<br/><br/>S’applique aux tables rowstore uniquement.|
| Index             | Index groupés par table.            | 1<br><br/>S’applique aux tables rowstore et columnstore.|
| Index             | Taille de la clé d’index.                          | 900 octets.<br/><br/>S’applique aux index rowstore uniquement.<br/><br/>Index de colonnes avec une taille maximale de plus de 900 octets peuvent être créées si les données existantes dans les colonnes ne dépassent pas 900 octets lorsque l’index est créé. Toutefois, insérez plus tard ou les actions de mise à jour sur les colonnes occasionnant la taille totale supérieure à 900 octets échouera.|
| Index             | Colonnes clés par index.                   | 16<br/><br/>S’applique aux index rowstore uniquement. Index columnstore groupé incluent toutes les colonnes.|
| Statistiques        | Taille des valeurs combinées colonne.      | 900 octets.         |
| Statistiques        | Colonnes par objet statistiques.           | 32                 |
| Statistiques        | Statistiques créées dans des colonnes par table. | 30 000            |
| Procédures stockées | Niveaux d’imbrication maximum.               | 8                 |
| Affichage              | Colonnes par vue                         | 1 024             |


## <a name="loads"></a>Charges

| Catégorie          | Description                                  | Valeur maximale            |
| :---------------- | :------------------------------------------- | :----------------- |
| Chargement des Polybase    | Octets par ligne                                | 32 768<br/><br/>Charges Polybase sont limitées au chargement de lignes à la fois inférieures à 32 Ko et ne peut pas charger VARCHR(MAX), nvarchar (max) ou varbinary (max).  Même si cette limite existe aujourd'hui, il sera supprimé assez rapidement.<br/><br/>


## <a name="queries"></a>Requêtes

| Catégorie          | Description                                  | Valeur maximale            |
| :---------------- | :------------------------------------------- | :----------------- |
| Requête             | Requêtes en file d’attente dans les tables utilisateur.               | 1000               |
| Requête             | Demandes simultanées sur les vues système.          | 100                |
| Requête             | Requêtes en file d’attente sur les vues système               | 1000               |
| Requête             | Paramètres maximales                           | 2098               |
| Traitement par lots             | Taille maximale                                 | 65, 536 * 4096        |
| Résultats de la sélection    | Colonnes par ligne                              | 4096<br/><br/>Vous pouvez jamais avoir plus de 4096 colonnes par ligne dans le résultat de la sélection. Il n’existe aucune garantie que vous pouvez toujours avoir 4096. Si le plan de requête requiert une table temporaire, 1024 colonnes par table maximale peuvent s’appliquer.|
| SÉLECTIONNEZ            | Sous-requêtes imbriquées                            | 32<br/><br/>Vous pouvez jamais avoir plus de 32 sous-requêtes imbriquées dans une instruction SELECT. Il n’existe aucune garantie que vous pouvez toujours avoir 32. Par exemple, une jointure peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut également être limité par la quantité de mémoire disponible.|
| SÉLECTIONNEZ            | Colonnes par jointure                             | 1024 colonnes<br/><br/>Vous pouvez jamais avoir plus de 1024 colonnes de la jointure. Il n’existe aucune garantie que vous pouvez toujours avoir 1024. Si le plan de jointure requiert une table temporaire avec plus de colonnes que le résultat de la jointure, la limite de 1024 s’applique à la table temporaire. |
| SÉLECTIONNEZ            | Octets par colonnes GROUP BY.                  | 8060<br/><br/>Les colonnes dans la clause GROUP BY peuvent contenir un maximum de 8060 octets.|
| SÉLECTIONNEZ            | Octets par colonnes ORDER BY                   | 8060 octets.<br/><br/>Les colonnes dans la clause ORDER BY peuvent contenir un maximum de 8060 octets.|
| Identificateurs et constantes par instruction | Nombre de constantes et identificateurs référencées. | 65 535<br/><br/>SQL Data Warehouse limite le nombre d’identificateurs et de constantes qui peuvent être inclus dans une expression unique d’une requête. Cette limite est de 65 535. Le dépassement de ce nombre se traduit par erreur SQL Server 8632. Pour plus d’informations, voir [erreur interne : une limite des services d’expression a été atteinte][].|


## <a name="metadata"></a>Métadonnées

| Vue système                        | Nombre maximal de lignes |
| :--------------------------------- | :------------|
| Sys.dm_pdw_component_health_alerts | 10 000       |
| Sys.dm_pdw_dms_cores               | 100          |
| Sys.dm_pdw_dms_workers             | Nombre total de travailleurs DMS pour la plus récente 1000 SQL requêtes. |
| Sys.dm_pdw_errors                  | 10 000       |
| Sys.dm_pdw_exec_requests           | 10 000       |
| Sys.dm_pdw_exec_sessions           | 10 000       |
| Sys.dm_pdw_request_steps           | Nombre total d’étapes pour les requêtes SQL 1000 plus récentes qui sont stockées dans sys.dm_pdw_exec_requests. |
| Sys.dm_pdw_os_event_logs           | 10 000       |
| Sys.dm_pdw_sql_requests            | Les requêtes SQL plus récentes 1000 qui sont stockés dans sys.dm_pdw_exec_requests. |


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir [vue d’ensemble de référence Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->
[Unités de magasin de données (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Vue d’ensemble de référence Data Warehouse SQL]: ./sql-data-warehouse-overview-reference.md
[Gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[type de données]: ./sql-data-warehouse-tables-data-types.md
[Création d’une demande d’assistance]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Données au-delà de 8 Ko de dépassement de ligne]: https://msdn.microsoft.com/library/ms186981.aspx
[Erreur interne : une limite des services d’expression a été atteinte]: https://support.microsoft.com/kb/913050
