<properties
   pageTitle="Utiliser données plateforme Studio de Redgate pour charger les données dans Data Warehouse SQL | Microsoft Azure"
   description="Découvrez comment utiliser données plateforme Studio de Redgate pour scénarios entrepôt de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Charger les données avec Redgate données plateforme Studio

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Données par défaut](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Ce didacticiel montre comment utiliser [Données plateforme Studio de Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (STD) pour déplacer des données d’un serveur SQL localement à SQL Azure Data Warehouse. Données plateforme Studio applique les correctifs de compatibilité et les optimisations, plus appropriées, il est le plus rapide pour commencer à utiliser Data Warehouse SQL.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) est un partenaire Microsoft depuis longtemps qui propose divers outils SQL Server. Cette fonctionnalité dans Studio plateforme de données a été créée librement pour une utilisation commerciale et non commerciale.

## <a name="before-you-begin"></a>Avant de commencer
### <a name="create-or-identify-resources"></a>Créez ou identifiez les ressources

Avant de commencer ce didacticiel, vous devez disposer :

- **Base de données locales SQL Server**: les données que vous voulez importer dans SQL Data Warehouse doivent provenir d’un serveur SQL locales (version 2008 R2 ou une version ultérieure). Données plateforme Studio ne peut pas importer des données directement à partir d’une base de données SQL Azure ou des fichiers texte.
- **Compte de stockage Azure**: données plateforme Studio stades les données dans le stockage Blob Azure avant de charger dans SQL Data Warehouse. Le compte de stockage doit utiliser le modèle de déploiement « Gestionnaire de ressources » (la valeur par défaut) plutôt que le modèle de déploiement « Classique ». Si vous n’avez pas un compte de stockage, apprenez à créer un compte de stockage. 
- **Data Warehouse SQL**: ce didacticiel déplace les données locales SQL Server dans SQL Data Warehouse, afin que vous devez avoir un data warehouse en ligne. Si vous n’avez pas déjà un data warehouse, apprenez à créer un magasin de données SQL Azure.

> [AZURE.NOTE] Les performances sont améliorées si le compte de stockage et le data warehouse sont créées dans la même région.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Étape 1 : Se connecter à des données plateforme Studio avec votre compte Azure
Ouvrez votre navigateur web et accédez au site Web [Studio plateforme de données](https://www.dataplatformstudio.com/) . Connectez-vous à l’aide du compte Azure que vous avez utilisé pour créer le magasin de compte et les données de stockage. Si votre adresse de messagerie n’est associée à un professionnel ou scolaire et un compte Microsoft, veillez à sélectionner le compte qui a accès à vos ressources.

> [AZURE.NOTE] Si c’est la première fois à l’aide de données plateforme Studio, vous êtes invité à autoriser l’application à gérer vos ressources Azure.

## <a name="step-2-start-the-import-wizard"></a>Étape 2 : Démarrer l’Assistant Importation
À partir de l’écran principal STD, sélectionnez l’importation au lien Azure SQL Data Warehouse pour démarrer l’Assistant importation.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Étape 3 : Installer la passerelle données plateforme Studio
Pour vous connecter à votre base de données SQL Server localement, vous devez installer la passerelle STD. La passerelle est un agent de client qui permet d’accéder à votre environnement locales, extrait les données et des téléchargements à votre compte de stockage. Vos données passent jamais via des serveurs de Redgate. Pour installer la passerelle :

1.  Cliquez sur le lien **Créer une passerelle**
2. Téléchargez et installez la passerelle via le programme d’installation fournie

![][2]

> [AZURE.NOTE] La passerelle peut être installée sur n’importe quel ordinateur avec un accès réseau à la base de données SQL Server source. Il accède à la base de données SQL Server à l’aide de l’authentification Windows avec les informations d’identification de l’utilisateur actuel.

Une fois installée, l’état de la passerelle passe à connecté et vous pouvez sélectionner suivant.

## <a name="step-4-identify-the-source-database"></a>Étape 4 : Identifier la base de données source
Dans la zone de texte *Entrez le nom du serveur* , entrez le nom du serveur qui héberge votre base de données, puis cliquez sur **suivant**. Puis, dans le menu déroulant, sélectionnez la base de données que vous voulez importer les données.

![][3]

STD examine la base de données sélectionnée pour les tables à importer. Par défaut, std importe toutes les tables dans la base de données. Vous pouvez sélectionner ou désélectionner les tables par développer le lien de toutes les Tables. Sélectionnez le bouton suivant pour avancer.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Étape 5 : Choisir un compte de stockage pour les données de l’étape
STD vous invite à entrer un emplacement pour les données de l’étape. Choisissez un compte de stockage existant à partir de votre abonnement, puis cliquez sur **suivant**.

> [AZURE.NOTE] STD crée un nouveau conteneur blob dans le compte de stockage choisi et utiliser un dossier distinct pour chaque importation.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Étape 6 : Sélectionnez un data warehouse
Ensuite, vous sélectionnez une base de données [SQL Azure Data Warehouse](http://aka.ms/sqldw) en ligne pour importer les données. Une fois que vous avez sélectionné votre base de données, vous devez entrer les informations d’identification pour vous connecter à la base de données, puis cliquez sur **suivant**.

![][5]

> [AZURE.NOTE] STD fusionne les tables de données source dans le data warehouse. STD vous avertit si le nom de table requiert qu’il remplace les tables existantes dans le data warehouse. Vous pouvez supprimer tous les objets existants dans le data warehouse en cochant supprimer tous les objets existants avant l’importation.

## <a name="step-7-import-the-data"></a>Étape 7 : Importer les données
STD confirme que vous voulez importer les données. Cliquez simplement sur le bouton Importer Démarrer pour démarrer l’importation de données.

![][6]

STD affiche une visualisation qui affiche la progression d’extraction et de chargement des données à partir de SQL Server sur site et l’avancement de l’importation dans Data Warehouse SQL.

![][7]

Une fois l’importation terminée, std affiche un résumé de l’importation de données et un rapport de modification des correctifs de compatibilité qui ont été effectuées.

![][8]

## <a name="next-steps"></a>Étapes suivantes
Pour Explorer vos données dans Data Warehouse SQL, commencez par l’affichage :

- [Requête SQL Azure Data Warehouse (Visual Studio)][]
- [Visualiser des données avec Power BI][]

Pour en savoir plus sur données plateforme Studio de Redgate :

- [Visitez la page d’accueil STD](http://www.dataplatformstudio.com/)
- [Regardez une démonstration du STD sur Channel 9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Pour une vue d’ensemble des autres façons de migrer et charger vos données dans Data Warehouse SQL, voir :

- [Migrer votre solution vers Data Warehouse SQL][]
- [Charger les données dans le magasin de données SQL Azure](./sql-data-warehouse-overview-load.md)

Pour obtenir d’autres conseils de développement, consultez la [vue d’ensemble du développement Data Warehouse SQL](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Requête SQL Azure Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualiser des données avec Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrer votre solution vers Data Warehouse SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md