<properties
    pageTitle="Codes d’erreur SQL : erreur de connexion de base de données | Microsoft Azure"
    description="Découvrez les codes d’erreur SQL pour les applications clientes de base de données SQL, tels que les erreurs de connexion de base de données, des problèmes de copie de base de données et des erreurs générales. "
    keywords="code d’erreur SQL, sql access, erreur de connexion de base de données, les codes d’erreur sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Cet article répertorie les codes d’erreur SQL pour les applications clientes SQL de base de données, notamment des erreurs de connexion de base de données, erreurs transitoires (également appelés défaillances temporaires), les erreurs de gouvernance de ressources, problèmes de copie de base de données, pool élastique et autres erreurs. La plupart des catégories sont propres à la base de données SQL Azure et ne s’appliquent pas à Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erreurs de connexion de base de données, les erreurs transitoires et autres erreurs temporaires

Le tableau suivant décrit les codes d’erreur SQL pour les erreurs de perte de connexion et d’autres erreurs transitoires, que vous pouvez rencontrer lors de votre application tente d’accéder aux base de données SQL. Pour obtenir des didacticiels sur la connexion de base de données SQL Azure, consultez l’article [connexion à la base de données SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erreurs de connexion de base de données plus courants et erreurs transitoires

L’infrastructure Azure a la possibilité de reconfigurer dynamiquement serveurs cas de charges de travail dans le service de base de données SQL.  Ce comportement dynamique peut-être provoquer votre programme client perdre sa connexion à la base de données SQL. Ce type de condition d’erreur est appelé une *défaillance transitoire*.

Si votre programme client possède une logique, il peut tenter de rétablir une connexion après avoir donné le temps de pannes transitoires se corrige de lui-même.  Nous vous recommandons de retarder pendant 5 secondes avant votre première tentative. Nouvelle tentative après un délai plus court que risques 5 secondes le service en nuage est surchargé. Pour chaque tentatives suivantes le délai doit croissance exponentielle, avec un maximum de 60 secondes.

Erreurs transitoires manifestent généralement dans l’un des messages d’erreur suivants à partir de vos programmes clients :

- Base de données < db_name > serveur < Azure_instance > n’est pas disponible actuellement. Réessayez plus tard la connexion. Si le problème persiste, contactez le support client et leur donner l’ID de suivi de session de < ID de session >

- Base de données < db_name > serveur < Azure_instance > n’est pas disponible actuellement. Réessayez plus tard la connexion. Si le problème persiste, contactez le support client et leur donner l’ID de suivi de session de < ID de session >. (Microsoft SQL Server, erreur : 40613)

- Une connexion existante a été fermée par l’hôte distant.

- System.Data.Entity.Core.EntityCommandExecutionException : Une erreur s’est produite lors de l’exécution de la définition de commande. Consultez l’exception interne pour plus d’informations. ---> System.Data.SqlClient.SqlException : une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur. (fournisseur : fournisseur de Session, erreur : 19 - connexion physique n’est pas utilisable)

Pour obtenir des exemples de code de logique de recherche, voir :

- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) 
- [Actions à corriger les erreurs de connexion et erreurs transitoires dans la base de données SQL](sql-database-connectivity-issues.md)

Une discussion de la *période de blocage* pour les clients qui utilisent ADO.NET est disponible dans [Le regroupement de connexion SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Codes d’erreur de défaillance transitoires

Les erreurs suivantes sont transitoires et doivent être retentés dans logique de l’application 

| Code d’erreur | Gravité | Description |
| ---: | ---: | :--- |
| 4060 | 16 | Ne peut pas ouvrir de base de données « % & #x2a ; ls » demandée par la connexion. Échec de la connexion. |
|40197|17|Le service a rencontré une erreur de traitement de votre requête. Essayez à nouveau. Code d’erreur %d.<br/><br/>Vous recevrez cette erreur lorsque le service est arrêté en raison de logiciels ou mises à niveau matérielles, pannes matérielles ou d’autres problèmes de basculement. Le code d’erreur (%d) incorporé dans le message d’erreur 40197 fournit des informations supplémentaires sur le type de panne ou basculement qui se sont produites. Quelques exemples de l’erreur codes incorporés dans le message d’erreur 40197 sont 40020, 40143, 40166 et 40540.<br/><br/>Se reconnecter à votre serveur de base de données SQL sera automatiquement vous connecter à une copie intègre de votre base de données. Votre application doit intercepter journal des erreurs 40197, le code d’erreur incorporé (%d) dans le message de résoudre les problèmes et essayez de vous reconnecter à la base de données SQL jusqu'à ce que les ressources sont disponibles, votre connexion est rétablie.|
|40501|20|Le service est actuellement occupé (e). Relancer la demande après 10 secondes. ID de l’incident : %ls. Code : %d.<br/><br/>*Remarque :* Pour plus d’informations, voir :<br/>• [Les limites des ressources de base de données SQL azure](sql-database-resource-limits.md).
|40613|17|Base de données '% & #x2a ; ls' sur serveur « % & #x2a ; ls » n’est pas disponible actuellement. Réessayez plus tard la connexion. Si le problème persiste, contactez le support client et leur donner l’ID de suivi de session de « % & #x2a ; ls ».|
|49918|16|Impossible de traiter la demande. Pas assez de ressources pour traiter la demande.<br/><br/>Le service est actuellement occupé (e). Réessayez plus tard la demande. |
|49919|16|Processus ne peut pas créer ou mettre à jour de la demande. Trop grand nombre créer ou mettre à jour les opérations en cours pour l’abonnement » % ld ».<br/><br/>Le service est occupé traitement plusieurs créer ou mettre à jour les demandes de votre abonnement ou de serveur. Demandes actuellement bloqués d’optimisation des ressources. Requête [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pour les opérations en attente. Patientez jusqu'à ce que créer ou mettre à jour en attente demandes sont terminées ou supprimer un de vos demandes en attente et renouveler votre requête ultérieurement. |
|49920|16|Impossible de traiter la demande. Trop d’opérations en cours pour l’abonnement » % ld ».<br/><br/>Le service est occupé à traiter plusieurs requêtes pour cet abonnement. Demandes actuellement bloqués d’optimisation des ressources. Requête [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) état de l’opération. Attente jusqu'à ce que demandes en attente sont terminée ou supprimer un de vos demandes en attente et renouveler votre requête ultérieurement. |

## <a name="database-copy-errors"></a>Erreurs de copie de base de données

Les erreurs suivantes peuvent être rencontrées lors de la copie d’une base de données dans la base de données SQL Azure. Pour plus d’informations, voir [Copier une base de données SQL Azure](sql-database-copy.md).


|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|40635|16|Client avec adresse IP '% & #x2a ; ls' est temporairement désactivé.|
|40637|16|Créer copie de la base de données est désactivée.|
|40561|16|Copie de la base de données a échoué. Base de données source ou cible n’existe pas.|
|40562|16|Copie de la base de données a échoué. La base de données source a été supprimé.|
|40563|16|Copie de la base de données a échoué. La base de données cible a été supprimé.|
|40564|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible, puis réessayez.|
|40565|16|Copie de la base de données a échoué. Pas plus de 1 copie simultané de la base de données à partir de la même source est autorisée. Veuillez supprimer la base de données cible, puis réessayez plus tard.|
|40566|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible, puis réessayez.|
|40567|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible, puis réessayez.|
|40568|16|Copie de la base de données a échoué. Base de données source est indisponible. Veuillez supprimer la base de données cible, puis réessayez.|
|40569|16|Copie de la base de données a échoué. Base de données cible est indisponible. Veuillez supprimer la base de données cible, puis réessayez.|
|40570|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible, puis réessayez plus tard.|
|40571|16|Copie de la base de données a échoué en raison d’une erreur interne. Veuillez supprimer la base de données cible, puis réessayez plus tard.|

## <a name="resource-governance-errors"></a>Erreurs de gouvernance de ressources

Messages d’erreur suivants sont créés par un excès utilisation des ressources lorsque vous travaillez avec la base de données SQL Azure. Par exemple :

- Une transaction a été ouverte trop longtemps.
- Une transaction est maintenant trop verrous.
- Une application consomme trop de mémoire.
- Une application consomme trop `TempDb` espace.

Rubriques connexes :

* Des informations plus détaillées sont disponibles ici : [limites de ressources de base de données SQL Azure](sql-database-resource-limits.md)

|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|10928|20|Nº ressource : %d. La limite de %s pour la base de données est %d et a été atteinte. Pour plus d’informations, voir [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>L’ID de ressource indique la ressource qui a atteint la limite. Threads de travail, l’ID de ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>*Remarque :* Pour plus d’informations sur cette erreur et comment faire pour résoudre ce problème, voir :<br/>• [Les limites des ressources de base de données SQL azure](sql-database-resource-limits.md). |
|10929|20|Nº ressource : %d. La garantie minimale %s est %d, nombre maximal est %d et l’utilisation de la base de données actuelle est %d. Toutefois, le serveur est actuellement occupé et prend en charge les demandes plus grande que %d pour cette base de données. Pour plus d’informations, voir [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Dans le cas contraire, veuillez réessayer ultérieurement.<br/><br/>L’ID de ressource indique la ressource qui a atteint la limite. Threads de travail, l’ID de ressource = 1. Pour les sessions, l’ID de ressource = 2.<br/><br/>*Remarque :* Pour plus d’informations sur cette erreur et comment faire pour résoudre ce problème, voir :<br/>• [Les limites des ressources de base de données SQL azure](sql-database-resource-limits.md).|
|40544|20|La base de données a atteint sa taille du quota. Partition ou supprimer des données, supprimer les index ou consultez la documentation pour les solutions possibles.|
|40549|16|Session se termine parce que vous disposez d’une transaction longue. Essayez de raccourcir votre transaction.|
|40550|16|La session a été interrompue, car elle a acquis verrous trop. Essayez de lecture ou modification moins de lignes en une seule opération.|
|40551|16|La session a été interrompue en raison d’un excès `TEMPDB` l’utilisation. Essayez de modifier votre requête afin de réduire l’espace utilisé par table temporaire.<br/><br/>*Conseil :* Si vous utilisez des objets temporaires, économiser de l’espace dans le `TEMPDB` base de données en faisant glisser les objets temporaires lorsque vous n’avez plus besoin par la session.|
|40552|16|La session a été interrompue en raison de l’utilisation de l’espace de journal des transactions excessive. Essayez de modifier le nombre de lignes dans une transaction unique.<br/><br/>*Conseil :* Si vous effectuez insertions en bloc à l’aide de la `bcp.exe` utilitaire ou le `System.Data.SqlClient.SqlBulkCopy` de cours, essayez d’utiliser la `-b batchsize` ou `BatchSize` options permettant de limiter le nombre de lignes copiés sur le serveur dans chaque transaction. Si vous recréez un index portant le `ALTER INDEX` instruction, essayez d’utiliser la `REBUILD WITH ONLINE = ON` option.|
|40553|16|La session a été interrompue en raison de l’utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Conseil :* Réduire le nombre de `ORDER BY` et `GROUP BY` opérations dans votre code Transact-SQL réduit les besoins en mémoire de votre requête.|

## <a name="elastic-pool-errors"></a>Erreurs du pool élastique

Les erreurs suivantes sont liées à la création et l’utilisation des Pools ELASTIQUES.

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Le pool élastique a atteint sa limite de stockage. L’utilisation de stockage pour le pool élastique ne peut pas dépasser Mo (%d). | Limite d’espace pool élastique en Mo. | Vous tentez d’écrire des données dans une base de données lors de la limite de stockage du pool élastique a été atteinte. | Veuillez augmentez les DTUs du pool élastique si possible afin d’augmenter sa limite de stockage, le stockage utilisé par les bases de données individuelles au sein du pool élastique d’en réduire ou supprimer des bases de données à partir du pool élastique. |
| 10929 | EX_USER | La garantie minimale %s est %d, nombre maximal est %d et l’utilisation de la base de données actuelle est %d. Toutefois, le serveur est actuellement occupé et prend en charge les demandes plus grande que %d pour cette base de données. Voir [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) pour obtenir une assistance. Dans le cas contraire, veuillez réessayer ultérieurement. | Min DTU par base de données ; Max DTU par base de données | Le nombre total de travailleurs simultanées (requêtes) dans toutes les bases de données dans le pool élastique tenté de dépasser la limite du pool. | Veuillez vous pouvez augmenter les DTUs du pool élastique si possible afin d’augmenter la limite de son travail, ou supprimer des bases de données à partir du pool élastique. |
| 40844 | EX_USER | Base de données '%ls' serveur '%ls' est une base de données '%ls' édition d’un pool élastique et ne peut pas contenir une relation copie continue. | nom, l’édition de base de données, le nom du serveur de base de données | Émission d’une commande StartDatabaseCopy pour une base de données non premium dans un pool élastique. | À venir |
| 40857 | EX_USER | Pool élastique non trouvé pour le serveur : '%ls', nom du pool élastique : '%ls'. | nom du serveur ; nom du pool élastique | Pool élastique spécifié n’existe pas sur le serveur spécifié. | Veuillez fournir un nom de pool élastique valide. |
| 40858 | EX_USER | Pool élastique '%ls' existe déjà dans server : '%ls' | nom du pool élastique, le nom de serveur | Pool élastique spécifié existe déjà dans le serveur logique spécifié. | Fournir le nom du nouveau pool élastique. |
| 40859 | EX_USER | Pool élastique ne reconnaît pas le niveau de service '%ls'. | niveau de service pool élastique | Niveau de service spécifié n’est pas prise en charge de mise en service du pool élastique. | Fournir l’édition correcte ou laissez niveau de service vide pour utiliser le niveau de service par défaut. |
| 40860 | EX_USER | Combinaison d’objectif '%ls' '%ls' et le service pool élastique n’est pas valide. | nom du pool élastique ; nom objectif au niveau du service | Élastique objectif pool et service peut être spécifié ensemble uniquement si le service est défini en tant que « ElasticPool ». | Spécifiez la combinaison correcte du groupe élastique et objectif de service. |
| 40861 | EX_USER | L’édition de base de données ' %. *! ne peut pas être différente de celle du niveau de service pool élastique qui est « %.* «%.*ls». | Édition de base de données, niveau de service pool élastique | L’édition de base de données est différente de celle du niveau de service pool élastique. | Ne spécifiez pas une édition de base de données qui est différente de celle du niveau de service pool élastique.  Notez que l’édition de base de données n’a pas besoin d’être indiquée. |
| 40862 | EX_USER | Nom du pool élastique doit être spécifié si l’objectif de service pool élastique. | Aucun | Objectif de service pool élastique n’identifie pas un pool élastique. | Spécifiez le nom du pool élastique à l’aide de l’objectif de service pool élastique. |
| 40864 | EX_USER | Les DTUs pour le pool élastique doivent contenir au moins (%d) DTUs de niveau de service ' % * «%.*ls». | DTUs pour élastique pool ; niveau de service pool élastique. | Tentative de définir les DTUs pour le pool élastique au-dessous de la limite minimale. | Réessayez de définir les DTUs pour l’élastique du pool au moins la limite inférieure. |
| 40865 | EX_USER | Les DTUs pour le pool élastique ne peut pas dépasser (%d) DTUs de niveau de service ' % * «%.*ls». | DTUs pour élastique pool ; niveau de service pool élastique. | Tentative de définir les DTUs pour le pool élastique au-dessus de la limite maximale. | Veuillez réessayer affectant les DTUs pour le pool élastique pas dépasser la limite maximale. |
| 40867 | EX_USER | La DTU max par base de données doit être au minimum (%d) pour le niveau de service ' % * «%.*ls». | Max DTU par base de données ; niveau de service pool élastique | Tentative de définition du max DTU par base de données en dessous de la limite prise en charge. | Envisagez d’utiliser le niveau de service pool élastique qui prend en charge le paramètre souhaité. |
| 40868 | EX_USER | La DTU max par base de données ne peut pas dépasser (%d) pour le niveau de service ' % * «%.*ls». | Max DTU par base de données ; niveau de service pool élastique. | Tentative de définition du max DTU par base de données dépasse la limite de pris en charge. | Envisagez d’utiliser le niveau de service pool élastique qui prend en charge le paramètre souhaité. |
| 40870 | EX_USER | La min DTU par base de données ne peut pas dépasser (%d) pour le niveau de service ' % * «%.*ls». | Min DTU par base de données ; niveau de service pool élastique. | Tentative de définition du min DTU par base de données dépasse la limite de pris en charge. | Envisagez d’utiliser le niveau de service pool élastique qui prend en charge le paramètre souhaité. |
| 40873 | EX_USER | Le nombre de bases de données (%d) et min DTU par base de données (%d) ne peut pas dépasser les DTUs du pool élastique (%d). | Nombre de bases de données dans le pool élastique ; Min DTU par base de données ; DTUs du pool élastique. | Tentative de spécifier min DTU des bases de données dans le pool élastique qui dépasse les DTUs du pool élastique. | Vous pouvez augmenter les DTUs du pool élastique, ou réduire la min DTU par base de données ou réduire le nombre de bases de données dans le pool élastique. |
| 40877 | EX_USER | Impossible de supprimer un pool élastique, sauf si elles ne contiennent pas les bases de données. | Aucun | Le pool élastique contient une ou plusieurs bases de données et par conséquent ne peuvent pas être supprimé. | Supprimez les bases de données à partir du pool élastique afin de le supprimer. |
| 40881 | EX_USER | Le pool élastique ' % * «%.*ls» a atteint sa limite du nombre de base de données.  La limite du nombre de base de données pour le pool élastique ne peut pas dépasser (%d) pour un pool élastique avec (%d) DTUs. | Nom du pool élastique ; limite du nombre de base de données du pool élastique ; e DTUs à la liste de ressources partagées. | Essayez de créer ou ajouter de base de données au pool élastique lorsque la limite du nombre de base de données du pool élastique a été atteinte. | Veuillez vous pouvez augmenter les DTUs du pool élastique si possible afin d’augmenter sa limite de base de données, ou supprimer des bases de données à partir du pool élastique. |
| 40889 | EX_USER | La limite de stockage pour le pool élastique ou DTUs ' % * «%.*ls» ne peut pas être réduite dans la mesure où qui ne souhaitez pas fournir suffisamment d’espace pour les bases de données. | Nom du pool élastique. | Essayez de diminuer la limite de stockage du pool élastique en dessous de son utilisation du stockage. | Veuillez envisagez de réduire l’utilisation du stockage des bases de données dans le pool élastique ou supprimer des bases de données à partir du pool afin de réduire sa DTUs ou la limite de stockage. |
| 40891 | EX_USER | La min DTU par base de données (%d) ne peut pas dépasser la max DTU par base de données (%d). | Min DTU par base de données ; DTU max par base de données. | Tentative de définition du min DTU par base de données supérieure à la max DTU par base de données. | Vérifiez que la min DTU par bases de données ne dépasse pas la max DTU par base de données. |
| À DÉFINIR | EX_USER | La taille de stockage pour une base de données individuelle dans un pool élastique ne peut pas dépasser la taille maximale autorisée par « % * «%.*ls» pool élastique niveau du service. | niveau de service pool élastique | La taille maximale de la base de données dépasse la taille maximale autorisée par le niveau de service pool élastique. | Définissez la taille maximale de la base de données dans les limites de la taille maximale autorisée par le niveau de service pool élastique. |

Rubriques connexes :

* [Créer un pool de base de données élastique (c#)](sql-database-elastic-pool-create-csharp.md) 
* [Gérer un pool de base de données élastique (c#)](sql-database-elastic-pool-manage-csharp.md). 
* [Créer un pool de base de données élastique (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Moniteur et gérer un pool de base de données élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erreurs générales

Messages d’erreur suivants ne correspondent pas à n’importe quelle catégorie précédente.

|Code d’erreur|Gravité|Description|
|---:|---:|:---|
|15006|16|<AdministratorLogin>n’est pas un nom valide, car elle contient des caractères non valides.|
|18452|14|Échec de la connexion. La connexion est à partir d’un domaine non approuvé et ne peuvent pas être utilisée avec Windows authentication.% & #x2a ; ls (connexions Windows ne sont pas pris en charge dans cette version de SQL Server.)|
|18456|14|Échec de la connexion pour l’utilisateur ' % #x2a;ls'.%. & #x2a ; % ls. & #x2a ; ls (Échec de la connexion de l’utilisateur « % & #x2a ; ls ». Le changement de mot de passe a échoué. Modification du mot de passe lors de la connexion n'est pas pris en charge dans cette version de SQL Server.)|
|18470|14|Échec de la connexion pour l’utilisateur '% & #x2a ; ls'. Raison : Le compte est disabled.% & #x2a ; ls|
|40014|16|Plusieurs bases de données ne peuvent pas être utilisés dans la même transaction.|
|40054|16|Tables sans un index cluster ne sont pas prises en charge dans cette version de SQL Server. Créer un index cluster et essayez à nouveau.|
|40133|15|Cette opération n’est pas pris en charge dans cette version de SQL Server.|
|40506|16|Identificateur de sécurité spécifié n’est pas valide pour cette version de SQL Server.|
|40507|16|« % & #x2a ; «%.*ls» ne peut pas être appelé avec des paramètres dans cette version de SQL Server.|
|40508|16|Instruction USE n’est pas prise en charge pour basculer entre les bases de données. Utiliser une connexion pour vous connecter à une autre base de données.|
|40510|16|Instruction '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server|
|40511|16|Fonction intégrée « % & #x2a ; ls » n’est pas pris en charge dans cette version de SQL Server.|
|40512|16|Fonctionnalités déconseillées '%ls' ne sont pas pris en charge dans cette version de SQL Server.|
|40513|16|Serveur variable '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40514|16|'%ls' n’est pas pris en charge dans cette version de SQL Server.|
|40515|16|Référence au nom de la base de données et/ou serveur en '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40516|16|Objets temporaires globaux ne sont pas prises en charge dans cette version de SQL Server.|
|40517|16|Option de mot clé ou un relevé '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40518|16|Commande DBCC '% & #x2a ; ls' n’est pas pris en charge dans cette version de SQL Server.|
|40520|16|Classe sécurisables '% S_MSG' est pas pris en charge dans cette version de SQL Server.|
|40521|16|Classe sécurisables '% S_MSG' est pas pris en charge dans l’étendue du serveur dans cette version de SQL Server.|
|40522|16|Base de données principale '% & #x2a ; ls' type n’est pas pris en charge dans cette version de SQL Server.|
|40523|16|Création de '% & #x2a ; ls' implicite d’utilisateur n’est pas prise en charge dans cette version de SQL Server. Créer explicitement l’utilisateur avant de l’utiliser.|
|40524|16|Type de données « % & #x2a ; ls » n’est pas pris en charge dans cette version de SQL Server.|
|40525|16|AVEC '%.ls' n’est pas pris en charge dans cette version de SQL Server.|
|40526|16|« % & #x2a ; fournisseur «%.*ls» ne pas pris en charge dans cette version de SQL Server.|
|40527|16|Serveurs liés ne sont pas prises en charge dans cette version de SQL Server.|
|40528|16|Les utilisateurs ne peuvent pas être mappées à des chèques, clés asymétriques ou connexions Windows dans cette version de SQL Server.|
|40529|16|Fonction intégrée « % & #x2a ; ls » dans l’emprunt d’identité contexte n’est pas pris en charge dans cette version de SQL Server.|
|40532|11|Impossible d’ouvrir le serveur « % & #x2a ; ls » demandée par la connexion. Échec de la connexion.|
|40553|16|La session a été interrompue en raison de l’utilisation excessive de la mémoire. Essayez de modifier votre requête pour traiter moins de lignes.<br/><br/>*Remarque :* Réduire le nombre de `ORDER BY` et `GROUP BY` opérations dans votre code Transact-SQL permet de réduire les besoins en mémoire de votre requête.|
|40604|16|Impossible de CREATE/ALTER pas la base de données, car il dépasse le quota du serveur.|
|40606|16|Attacher des bases de données n’est pas pris en charge dans cette version de SQL Server.|
|40607|16|Connexions Windows ne sont pas prises en charge dans cette version de SQL Server.|
|40611|16|Serveurs peuvent avoir au maximum 128 règles de pare-feu définies.|
|40614|16|Adresse IP de début de la règle de pare-feu ne peut pas dépasser adresse IP de fin.|
|40615|16|Impossible d’ouvrir le serveur '{0}' demandée par la connexion. Client avec l’adresse IP '{1}' n’est pas autorisé à accéder au serveur.  Pour activer l’accès, utiliser le portail de base de données SQL ou exécuter sp_set_firewall_rule sur la base de données principale pour créer une règle de pare-feu pour cette adresse IP ou la plage d’adresses.  Il peut prendre jusqu'à cinq minutes pour que cette modification prenne effet.|
|40617|16|Le nom de la règle pare-feu qui commence par <rule name> est trop long. Longueur maximale est 128.|
|40618|16|Nom de la règle de pare-feu ne peut pas être vide.|
|40620|16|Échec de la connexion de l’utilisateur « % & #x2a ; ls ». Le changement de mot de passe a échoué. Modification du mot de passe pendant la connexion n’est pas pris en charge dans cette version de SQL Server.|
|40627|20|Opération sur le serveur '{0}' et base de données '{1}' est en cours. Patientez quelques minutes avant de réessayer.|
|40630|16|Échec de validation de mot de passe. Le mot de passe ne répond pas aux exigences de la stratégie, car il est trop court.|
|40631|16|Le mot de passe que vous avez spécifié est trop long. Le mot de passe doit contenir pas dépasser 128 caractères.|
|40632|16|Échec de validation de mot de passe. Le mot de passe ne répond pas aux exigences de la stratégie, car il n’est pas assez complexe.|
|40636|16|Impossible d’utiliser un nom de base de données réservé '% & #x2a ; ls' dans cette opération.|
|40638|16|Id de l’abonnement non valides < id d’abonnement >. Abonnement n’existe pas.|
|40639|16|Demande n’est pas conforme au schéma : <schema error>.|
|40640|20|Le serveur a rencontré une exception inattendue.|
|40641|16|L’emplacement spécifié n’est pas valide.|
|40642|17|Le serveur est actuellement occupé. Veuillez réessayer ultérieurement.|
|40643|16|La valeur de l’en-tête x-ms-version spécifié n’est pas valide.|
|40644|14|Impossible d’autoriser l’accès à l’abonnement spécifié.|
|40645|16|Nom du serveur <servername> ne peut pas être vide ou null. Il peut uniquement être composé de lettres minuscules « a »-« z », les chiffres 0 à 9 et le trait d’union. Trait d’union ne peut-être pas animer et traçage dans le nom.|
|40646|16|ID de l’abonnement ne peut pas être vide.|
|40647|16|Abonnement < id de l’abonnement n’a pas de nom du serveur de serveur.|
|40648|17|Trop de requêtes ont été effectuées. Réessayez plus tard.|
|40649|16|Type de contenu non valide est spécifié. Application/xml uniquement est pris en charge.|
|40650|16|Abonnement < id d’abonnement > n’existe pas ou n’est pas prêt pour l’opération.|
|40651|16|Échec de création de serveur, car l’abonnement < id d’abonnement > est désactivé.|
|40652|16|Impossible de déplacer ou créer serveur. Abonnement < id d’abonnement > dépassent quota de serveur.|
|40671|17|Échec de la communication entre la passerelle et le service de gestion. Réessayez plus tard.|
|40852|16|Ne peut pas ouvrir de base de données ' %. *! sur le serveur ' %.* «%.*ls» demandée par la connexion. Accès à la base de données est autorisé uniquement à l’aide d’une chaîne de connexion sécurisée. Pour accéder à cette base de données, modifier vos chaînes de connexion contenant « sécurisé » sur le serveur de nom de domaine complet -.database.windows 'nom de serveur'.net doit être modifié pour .database 'nom de serveur'. `secure`. Windows.NET.|
|45168|16|Le système SQL Azure se trouve sous charge et place une limite supérieure sur simultané DB opérations pour un seul serveur (par exemple, créer de base de données). Le serveur spécifié dans le message d’erreur a dépassé le nombre maximal de connexions simultanées. Réessayez plus tard.|
|45169|16|Le système SQL azure se trouve sous charge et place une limite supérieure sur le nombre d’opérations simultanées au serveur pour un abonnement unique (par exemple, créer un serveur). L’abonnement spécifié dans le message d’erreur a dépassé le nombre maximal de connexions simultanées, et la demande a été refusée. Réessayez plus tard.|

## <a name="related-links"></a>Liens connexes

- [Instructions et Limitations de général de la base de données SQL Azure](sql-database-general-limitations.md)
- [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md)
