<properties 
    pageTitle="Configurer la Active Geo-réplication de base de données SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Configurer la Active Geo-réplication de base de données SQL Azure à l’aide de PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurer la Geo-réplication de base de données SQL Azure avec PowerShell

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous explique comment configurer Active Geo-réplication de base de données SQL avec PowerShell.

Pour déclencher le basculement à l’aide de PowerShell, voir [lancer un basculement planifié ou de base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Réplication Geo active (lisibles secondaires) est désormais disponible pour toutes les bases de données de tous les niveaux de service. Dans avril 2017 le type secondaire illisible sera être déclassé et bases de données non lisible existantes passeront automatiquement vers secondaires lisibles.



Pour configurer la réplication Geo Active à l’aide de PowerShell, vous devez les éléments suivants :

- Un abonnement Azure. 
- Une base de données SQL Azure - la base de données principale que vous souhaitez répliquer.
- Azure PowerShell 1.0 ou version ultérieure. Vous pouvez télécharger et installer les modules PowerShell Azure en suivant [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d’identification, puis sélectionnez votre abonnement

Vous devez tout d’abord établir un accès à votre compte Azure donc démarrer PowerShell, puis exécutez l’applet de commande suivante. Dans l’écran de connexion, entrez les mêmes messagerie et votre mot de passe que vous utilisez pour vous connecter au portail Azure.


    Login-AzureRmAccount

Après la connexion avec succès, vous verrez certaines informations sur l’écran qui inclut l’Id vous connecté à l’aide et les abonnements Azure que vous avez accès.


### <a name="select-your-azure-subscription"></a>Sélectionnez votre abonnement Azure

Pour sélectionner l’abonnement, vous devez votre abonnement identifiant. Vous pouvez copier l’Id de l’abonnement à partir des informations affichées à partir de l’étape précédente, ou si vous disposez de plusieurs abonnements et que vous souhaitez obtenir plus d’informations vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copiez les informations de votre choix abonnement du jeu de résultats. L’applet de commande suivante utilise l’Id de l’abonnement pour définir l’abonnement actif :

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté correctement **Sélectionnez AzureRmSubscription** vous revenez à la session PowerShell invite.


## <a name="add-secondary-database"></a>Ajouter une base de données secondaire


Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat Geo réplication.  
  
Pour activer un secondaire, vous devez être le propriétaire de l’abonnement ou copropriétaire. 

Vous pouvez utiliser l’applet de commande **New-AzureRmSqlDatabaseSecondary** pour ajouter une base de données secondaire sur un serveur de partenaire pour une base de données locale sur le serveur auquel vous êtes connecté (base de données principale). 

Cette applet de commande remplace **AzureSqlDatabaseCopy de démarrer** avec le paramètre **– IsContinuous** .  Elle est accompagnée d’un objet **AzureRmSqlDatabaseSecondary** qui peut être utilisé par d’autres applets de commande pour clairement identifier un lien de réplication spécifique. Cette applet de commande renverra lorsque la base de données secondaire est créé et entièrement mises en culture. Selon la taille de la base de données peut prendre de quelques minutes à quelques heures.

La base de données répliquée sur le serveur secondaire a le même nom que la base de données sur le serveur principal et créera par défaut, le même niveau de service. La base de données secondaire peut être lisible ou non lisible et peut être une seule base de données ou une base de données élastique. Pour plus d’informations, voir [Nouveau AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).
Une fois l’image secondaire est créée et mises en culture, données commenceront réplication à partir de la base de données principale vers la nouvelle base de données secondaire. Les étapes suivantes décrivent comment effectuer cette tâche à l’aide de PowerShell pour créer secondaires non lisible et compréhensible, avec une seule base de données ou une base de données élastique.

Si la base de données partenaire existe déjà (par exemple - résulte termine une relation Geo réplication précédente), la commande échouera.



### <a name="add-a-non-readable-secondary-single-database"></a>Ajouter un secondaire non lisible (base de données unique)

La commande suivante crée une copie secondaire illisible de base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Ajouter lisible secondaire (base de données unique)

La commande suivante crée une copie lisible secondaire de base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Ajouter secondaire non lisible (base de données élastique)

La commande suivante crée secondaire illisible de base de données « mydb » dans le pool de base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Ajouter secondaire lisible (base de données élastique)

La commande suivante crée secondaire lisible de base de données « mydb » dans le pool de base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

Utiliser l’applet de commande **Supprimer AzureRmSqlDatabaseSecondary** définitivement terminer le partenariat de réplication entre une base de données secondaire et principal. À l’issue de la relation, la base de données secondaire devient une base de données en lecture / écriture. Si la connectivité à une base de données secondaire a provoqué la commande a réussi, mais le moniteur secondaire devient en lecture / écriture après restauration de connectivité. Pour plus d’informations, voir [Supprimer AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).

Cette applet de commande remplace arrêter AzureSqlDatabaseCopy pour la réplication. 

Cette suppression équivaut à un arrêt forcé qui supprime le lien de réplication et laisse le moniteur secondaire ancien comme une base de données autonome qui n’est pas entièrement répliquée antérieures à fin. Toutes les données de lien seront nettoyées sur l’ancien principal et secondaire, ancienne si ou lorsqu’ils sont disponibles. Cette applet de commande renverra lorsque le lien de réplication est supprimé. 


Pour supprimer secondaire, les utilisateurs doivent avoir accès en écriture aux bases de données principales et secondaires en fonction de RBAC. Voir contrôle d’accès basé sur un rôle pour plus d’informations.

L’exemple suivant supprime le lien de réplication de base de données nommée « mydb » au serveur » srv2 » de la ressource groupe « rg2 ». 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>État et la configuration de la réplication de geo moniteur

Tâches de surveillance incluent analyse du fonctionnement de la réplication de données et de contrôle de la configuration de la réplication geo.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) peut servir à récupérer les informations sur les liens de réplication vers l’avant visibles dans la vue de catalogue sys.geo_replication_links.

La commande suivante extrait l’état de la liaison de réplication entre la base de données principale « mydb » et le moniteur secondaire sur le serveur « srv2 » de la ressource groupe « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Geo-réplication Active, consultez - [Geo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)

