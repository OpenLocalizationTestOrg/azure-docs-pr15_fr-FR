< propriétés
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Mettre à niveau une application pour utiliser la dernière bibliothèque client élastique de base de données

Nouvelles versions de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md) sont disponibles via l’interface du Gestionnaire de NuGetPackage dans Visual Studio NuGetand. Mises à niveau contiennent correctifs et prise en charge des nouvelles fonctionnalités de la bibliothèque de client.

**Pour la version la plus récente :** Accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Reconstruire votre application avec la nouvelle bibliothèque, ainsi que modifier métadonnées de votre gestionnaire de carte éclater existante stockées dans vos bases de données SQL Azure pour prendre en charge les nouvelles fonctionnalités.

Effectuer ces étapes dans l’ordre garantit anciennes versions de la bibliothèque client n’est plus présentes dans votre environnement mise à jour des objets de métadonnées, qui signifie que les objets de métadonnées de l’ancienne version ne sont pas créées après mise à niveau.   

## <a name="upgrade-steps"></a>Étapes de mise à niveau

**1. mise à niveau de vos applications.** Dans Visual Studio, téléchargez et faire référence à la dernière version de bibliothèque client dans tous vos projets de développement qui utilisent la bibliothèque ; puis reconstruire et déployer. 

 * Dans votre solution Visual Studio, sélectionnez **Outils** --> **Gestionnaire de Package NuGet** -->  **Manage NuGet Packages pour la Solution**. 
 * (Visual Studio 2013) Dans le volet gauche, sélectionnez les **mises à jour**, puis le bouton de **mise à jour** dans le package **Bibliothèque cliente élastique échelle d’Azure SQL de base de données** qui apparaît dans la fenêtre.
 * (Visual Studio 2015) Définissez la zone de filtre pour **mettre à niveau disponibles**. Sélectionnez le package de mise à jour, puis cliquez sur le bouton **mettre à jour** .
    
 
 * Créez et déployez. 

**2. Mettez à niveau vos scripts.** Si vous utilisez des scripts **PowerShell** pour gérer milieu des fragments, [Téléchargez la nouvelle version de bibliothèque](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) et copiez-le dans le répertoire à partir duquel vous exécutez des scripts. 

**3. mise à niveau de votre service de fusion et fractionner.** Si vous utilisez l’outil de fusion et fractionnement élastique de base de données pour réorganiser les données sharded, [télécharger et déployer la dernière version de l’outil](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Pour le Service peut être trouvé une mise à niveau procédure détaillée [ici](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. mise à niveau de vos bases de données Gestionnaire de carte éclater**. Mettre à niveau les métadonnées de vos cartes partagé de prise en charge dans la base de données SQL Azure.  Il existe deux façons, vous pouvez y parvenir à l’aide de PowerShell ou c#. Les deux options sont affichées ci-dessous.

***Option 1 : Mettre à niveau les métadonnées à l’aide de PowerShell***

1. Téléchargez la dernière version utilitaire de ligne de commande pour NuGet [ici](http://nuget.org/nuget.exe) et enregistrer dans un dossier. 

2. Ouvrez une invite de commandes, à naviguer dans le même dossier et exécutez la commande :`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Recherchez le sous-dossier contenant la nouvelle version du client DLL que vous avez simplement téléchargé, par exemple :`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Télécharger le scriptlet mise à niveau du client élastique de base de données à partir du [Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), puis enregistrez-le dans le même dossier contenant la DLL.

5. À partir de ce dossier, exécutez « PowerShell.\upgrade.ps1 » à partir de l’invite de commandes et suivez les invites.
 
***Option 2 : Mettre à niveau les métadonnées à l’aide de c#***

Vous pouvez également créer une application Visual Studio qui ouvre votre ShardMapManager, parcourt tous les milieu des fragments et exécute la mise à niveau des métadonnées en appelant les méthodes [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) et [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) comme dans cet exemple : 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Ces techniques de métadonnées mises à jour peuvent être appliqués plusieurs fois sans endommager. Par exemple, si une version antérieure de client créé par inadvertance une éclater une fois que vous avez déjà mis à jour, vous pouvez exécuter à nouveau mise à niveau sur tous les milieu des fragments pour vous assurer que la dernière version de métadonnées est présente dans l’ensemble de votre infrastructure. 

**Remarque :**  Nouvelles versions de la bibliothèque cliente publiés à ce jour continuer à travailler avec les versions antérieures des métadonnées éclater carte gestionnaire sur la base de données SQL Azure et vice versa.   Toutefois, pour tirer parti de certaines des nouvelles fonctionnalités dans la dernière version du client, métadonnées devant être mis à niveau.   Notez que les mises à niveau de métadonnées n’affectent pas les données de l’utilisateur ou des données spécifiques à l’application, seuls les objets créés et utilisés par le Gestionnaire de carte partagé.  Et applications continuent à fonctionner à travers la séquence de mise à niveau décrite ci-dessus. 

## <a name="elastic-database-client-version-history"></a>Historique des versions de base de données élastique client 

Pour l’historique de version, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 