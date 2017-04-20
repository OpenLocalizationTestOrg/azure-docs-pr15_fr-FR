<properties
   pageTitle="Transactions distribuées entre les bases de données cloud"
   description="Vue d’ensemble des Transactions de base de données élastique avec la base de données SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transactions distribuées entre les bases de données cloud

Transactions de base de données élastique de base de données SQL Azure (DB SQL) vous autorise à exécuter les transactions qui s’étalent sur plusieurs bases de données dans la base de données SQL. Transactions de base de données élastique pour DB SQL sont disponibles pour les applications .NET à l’aide d’ADO.NET et intégrant l’expérience de programmation familier en utilisant les classes [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Pour obtenir la bibliothèque, voir [.NET Framework 4.6.1 (programme d’installation Web)](https://www.microsoft.com/download/details.aspx?id=49981).

En local, tel est le cas généralement requis exécutant Microsoft Distributed Transaction Coordinator (MSDTC). Dans la mesure où MSDTC n’est pas disponible pour l’application de plateforme en tant que Service dans Azure, la possibilité pour coordonner les transactions distribuées a maintenant été directement intégrée à base de données SQL. Applications peuvent se connecter à une base de données SQL pour lancer des transactions distribuées et une base de données s’en toute transparence la transaction distribuée, comme illustré dans l’illustration suivante. 

  ![Transactions distribuées avec la base de données SQL Azure à l’aide de transactions de base de données élastique ][1]

## <a name="common-scenarios"></a>Scénarios courants

Transactions de base de données élastique pour DB SQL permettent aux applications apporter des modifications atomiques aux données stockées dans plusieurs bases de données SQL différentes. L’aperçu se concentre sur l’expérience de développement côté client dans c# et .NET. Une expérience côté serveur à l’aide de T-SQL est planifiée pour une date ultérieure.  
Transactions de base de données élastique cible les scénarios suivants :

* Applications de bases de données multiples dans Azure : avec ce scénario, les données sont partitionnées verticalement sur plusieurs bases de données dans la base de données SQL, tels que les différents types de données se trouvent sur différentes bases de données. Certaines opérations requièrent des modifications apportées aux données qui sont conservées dans deux ou plusieurs bases de données. L’application utilise les transactions de base de données élastique pour coordonner les modifications entre bases de données et de garantir l’atomicité.

* Applications de base de données sharded dans Azure : avec ce scénario, la couche données utilise la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md) ou automatique ont pour diviser horizontalement les données entre plusieurs bases de données dans la base de données SQL. Un cas d’utilisation évidentes est nécessaire pour exécuter des modifications atomiques pour une application multi-cliente sharded lors de modifications s’étalent sur clients. Considérez par exemple un transfert d’un client vers une autre, les deux résidant sur différentes bases de données. Un second cas est permissions ont en fonction des besoins de capacité pour un client volumineux qui à son tour généralement implique que certaines opérations atomiques doit étirer sur plusieurs bases de données utilisées pour le même client. Un troisième cas est atomiques mises à jour pour référencer les données sont répliquées dans les bases de données. Opérations atomiques, traitées, le long de ces lignes peuvent désormais être coordonnées entre plusieurs bases de données à l’aide de l’aperçu.
Transactions de base de données élastique utilisent phases pour garantir la transmission des transactions entre bases de données. Il est adaptée aux transactions qui impliquent moins de 100 bases de données à la fois dans une transaction unique. Ces limites ne sont pas appliquées, mais une doit s’attendre performances et le taux de réussite pour les transactions de base de données élastique dysfonctionnements lorsque ces limites.


## <a name="installation-and-migration"></a>Installation et migration

Les fonctionnalités pour les transactions de base de données élastique dans la base de données SQL sont fournies par les mises à jour pour les bibliothèques .NET System.Data.dll et System.Transactions.dll. Les DLL garantissent que phases est utilisé si nécessaire pour garantir l’atomicité. Pour commencer à développer des applications à l’aide de transactions de base de données élastique, installez [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou une version ultérieure. Lors de l’exécution sur une version antérieure du .NET framework, transactions ne pourra pas promouvoir une transaction distribuée et une exception est élevée.

Après l’installation, vous pouvez utiliser la transaction distribuée API dans System.Transactions avec les connexions de base de données SQL. Si vous avez des applications MSDTC existantes à l’aide de ces API, il vous suffit reconstruire vos applications existantes pour .NET 4.6 après avoir installé la 4.6.1 Framework. Si vos projets ciblent .NET 4.6, ils utiliseront automatiquement les DLL mis à jour à partir de la nouvelle version de Framework et transaction distribuée QU'API appelle en combinaison avec les connexions de base de données SQL maintenant réussit.

N’oubliez pas que les transactions de base de données élastique ne nécessitent pas installer MSDTC. En revanche, les transactions de base de données élastique sont gérées directement par et dans la base de données SQL. Cela simplifie considérablement scénarios de cloud dans la mesure où un déploiement de MSDTC n’est pas nécessaire d’utiliser des transactions distribuées avec la base de données SQL. Section 4 explique plus en détail comment déployer des transactions de base de données élastique et requis .NET framework avec vos applications cloud pour Azure.

## <a name="development-experience"></a>Expérience de développement

### <a name="multi-database-applications"></a>Applications de bases de données multiples

Le code suivant utilise l’expérience de programmation familiarisé avec System.Transactions .NET. La classe TransactionScope établit une transaction ambiance dans .NET. (Une « transaction ambiance » est une valeur qui se trouve dans une conversation en cours.) Toutes les connexions ouvertes dans TransactionScope participent à la transaction. Si vous participez à des bases de données différentes, celle-ci est automatiquement élevée à une transaction distribuée. Le résultat de la transaction est contrôlé en définissant la zone à effectuer pour indiquer une validation.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Applications de base de données sharded
 
Transactions de base de données élastique pour DB SQL prennent également en charge coordonner des transactions distribuées où vous utilisez la méthode OpenConnectionForKey de la bibliothèque de client de base de données élastique pour ouvrir des connexions pour une mise à l’échelle des données niveau. Prendre en compte si vous avez besoin pour garantir la cohérence des modifications sur plusieurs valeurs clés ont différents. Connexions au milieu des fragments qui héberge les valeurs de clés ont différents sont demandées à l’aide de OpenConnectionForKey. En règle générale, les connexions peuvent être au milieu des fragments différents, tels que garantissant garanties transactions nécessite une transaction distribuée. Le code suivant illustre cette approche. Il suppose que vous utilisiez une variable appelée shardmap pour représenter une carte partagé à partir de la bibliothèque de client élastique de base de données :

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Installation .NET des Services en nuage Azure

Azure fournit plusieurs offres d’héberger des applications .NET. Une comparaison des différentes offres est disponible dans [Azure Application Service, les Services en nuage et comparaison de Machines virtuelles](../app-service-web/choose-web-site-cloud-service-vm.md). Si la système d’exploitation invité de l’offre est inférieure à .NET 4.6.1 requis pour les transactions élastiques, vous devez mettre à niveau la système d’exploitation invité vers 4.6.1. 

Pour les Services d’application Azure, mises à niveau vers la système d’exploitation invité ne sont actuellement pas pris en charge. Pour le Machines virtuelles Azure, connectez-vous simplement la machine virtuelle et exécutez le programme d’installation pour le dernier .NET framework. Pour les Services de Cloud Azure, vous devez inclure l’installation d’une nouvelle version .NET dans les tâches de démarrage de votre déploiement. Concepts et étapes sont présentées dans [.NET installer sur un rôle de Service Cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Notez que le programme d’installation pour .NET 4.6.1 peut-être nécessiter plus de stockage temporaire pendant le processus de démarrage sur les services en nuage Azure que le programme d’installation de .NET 4.6. Pour vous assurer que l’installation a réussi, vous devez augmenter le stockage temporaire de votre service cloud Azure dans votre fichier ServiceDefinition.csdef dans la section LocalResources et les paramètres d’environnement de votre tâche au démarrage, comme le montre l’exemple suivant :

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transactions sur plusieurs serveurs

Transactions de base de données élastique sont pris en charge sur les différents serveurs logiques de base de données SQL Azure. Lorsque des transactions dépassent des limites de serveur logique, les serveurs participants doivent d’abord renseigner dans une relation de communication commun. Une fois la relation de communication établie, les bases de données dans un des deux serveurs peuvent participer à des transactions élastiques les bases de données à partir de l’autre serveur. Avec les transactions s’étendant sur plus de deux serveurs logiques, une relation de communication doit être mis en place pour une paire de serveurs logiques.

Utiliser les applets de commande PowerShell pour gérer les relations entre les serveurs communication pour les transactions de base de données élastique :

* **Nouveau AzureRmSqlServerCommunicationLink**: cette applet de commande permet de créer une nouvelle relation de communication entre deux serveurs logiques dans la base de données SQL Azure. La relation est symétrique ce qui signifie que les deux serveurs peuvent générer des transactions avec l’autre serveur.
* **Get-AzureRmSqlServerCommunicationLink**: utilisez cette applet de commande pour récupérer les relations existantes de communication et leurs propriétés.
* **Supprimer AzureRmSqlServerCommunicationLink**: utilisez cette applet de commande pour supprimer une relation de communication existante. 


## <a name="monitoring-transaction-status"></a>Suivi de l’état de transaction

Utiliser des vues de gestion dynamique (DMV) dans la base de données SQL pour surveiller l’état et l’avancement de vos transactions de base de données élastique en cours. Tous les DMV liées à des transactions concernent les transactions distribuées dans la base de données SQL. Vous pouvez consulter la liste correspondante des DMV ici : [vues de gestion dynamique connexes Transaction et fonctions (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Ces DMV est particulièrement utiles :

* **sys.dm\_tran\_active\_transactions**: répertorie les transactions actives et leur statut. La colonne UOW (unité de travail) peut identifier les transactions enfants différents appartenant à la même transaction distribuée. Toutes les transactions dans la même transaction distribuée effectuer la même valeur UOW. Consultez la [documentation de vue](https://msdn.microsoft.com/library/ms174302.aspx) pour plus d’informations.
* **sys.dm\_tran\_base de données\_transactions**: fournit des informations supplémentaires sur les transactions, telles que le placement de la transaction dans le journal. Consultez la [documentation de vue](https://msdn.microsoft.com/library/ms186957.aspx) pour plus d’informations.
* **sys.dm\_tran\_verrous**: fournit des informations sur les verrous actuellement maintenus par les transactions en cours. Consultez la [documentation de vue](https://msdn.microsoft.com/library/ms190345.aspx) pour plus d’informations.

## <a name="limitations"></a>Limitations 

Les limitations suivantes s’appliquent actuellement pour les transactions de base de données élastique dans la base de données SQL :

* Uniquement les transactions via des bases de données dans la base de données SQL sont pris en charge. Autres [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) fournisseurs de ressources et de bases de données en dehors de la base de données SQL ne peut pas participer aux transactions de base de données élastique. Cela signifie que les transactions de base de données élastique ne peut pas étirer sur local SQL Server et les bases de données SQL Azure. Pour les transactions distribuées en local, continuez à utiliser MSDTC. 
* Seules les transactions client coordonné à partir d’une application .NET sont pris en charge. Prise en charge côté serveur pour T-SQL tels que commencer DISTRIBUTED TRANSACTION est planifiée, mais n’est pas encore disponible. 
* Seules les bases de données sur V12 de base de données SQL Azure sont prises en charge.
* Les transactions via des services WCF ne sont pas pris en charge. Par exemple, vous avez une méthode de service WCF qui s’exécute une transaction. L’appel au sein d’une étendue de transaction l’encadrement échouera comme un [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Ressources supplémentaires

Pas encore utilise fonctionnalités élastique de base de données pour vos applications Azure ? Consultez notre [Explorateur de documents](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Vous avez des questions, veuillez contacter nous sur le [forum de la base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et les requêtes de fonctionnalité, veuillez les ajouter à la [base de données SQL évaluations](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



