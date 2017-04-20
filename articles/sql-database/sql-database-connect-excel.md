<properties
    pageTitle="Se connecter Excel à base de données SQL | Microsoft Azure"
    description="Découvrez comment établir une connexion à la base de données SQL Azure dans le cloud Microsoft Excel. Importer des données dans Excel pour l’exploration de données et de rapports."
    services="sql-database"
    keywords="se connecter excel à sql, importer des données vers excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Didacticiel de base de données SQL : se connecter Excel à une base de données SQL Azure et créer un rapport

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Découvrez comment vous connecter Excel à une base de données SQL dans le cloud afin que vous pouvez importer des données et créer des tableaux et des graphiques en fonction des valeurs dans la base de données. Dans ce didacticiel, que vous allez configurer la connexion entre Excel et une table de base de données, enregistrez le fichier qui stocke les données et les informations de connexion pour Excel, puis créez un graphique croisé dynamique à partir des valeurs de base de données.

Vous devez une base de données SQL Azure avant de commencer. Si vous n’en avez, voir [créer votre première base de données SQL](sql-database-get-started.md) pour obtenir une base de données avec des exemples de données vers le haut et en cours d’exécution en quelques minutes. Dans cet article, vous allez importer des données d’exemple dans Excel à partir de cet article, mais vous pouvez suivre une procédure similaire sur vos propres données.

Vous devez également une copie d’Excel. Cet article utilise [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Se connecter Excel à une base de données SQL et créer un fichier odc

1.  Pour connecter Excel à la base de données SQL, ouvrez Excel et créez un nouveau classeur ou ouvrez un classeur Excel existant.

2.  Dans la barre de menus en haut de la page cliquez sur **les données**, cliquez sur **à partir d’autres Sources**, puis cliquez sur **à partir de SQL Server**.

    ![Sélectionner les données source : se connecter Excel à base de données SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    L’Assistant connexion de données s’ouvre.

3.  Dans la boîte de dialogue **se connecter au serveur de base de données** , tapez la base de données SQL **nom du serveur** que vous voulez vous connecter sous la forme <*nom du serveur*>**. database.windows.net**. Par exemple, **adworkserver.database.windows.net**.

4.  Sous **références de connexion**, cliquez sur **utiliser le nom d’utilisateur et mot de passe suivants**, tapez le **Nom d’utilisateur** et le **mot de passe** configurer pour le serveur de base de données SQL lorsque vous l’avez créé, puis cliquez sur **suivant**.

    ![Tapez les informations d’identification serveur nom et la connexion](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] En fonction de votre environnement réseau, vous ne pourrez pas vous connecter ou vous risquez de perdre la connexion si le serveur de base de données SQL n’autorise pas le trafic de votre adresse IP du client. Accédez au [portail Azure](https://portal.azure.com/), cliquez sur serveurs SQL Server, cliquez sur votre serveur, cliquez sur pare-feu sous paramètres et ajouter votre adresse IP du client. Pour plus d’informations, voir [comment configurer les paramètres de pare-feu](sql-database-configure-firewall-settings.md) .

5. Dans la boîte de dialogue **Sélectionner une base de données et Table** , sélectionnez la base de données que vous souhaitez utiliser dans la liste, puis cliquez sur les tables ou vues auxquelles vous voulez travailler avec (nous avons choisi **vGetAllCategories**), puis cliquez sur **suivant**.

    ![Sélectionnez une base de données et une table.](./media/sql-database-connect-excel/select-database-and-table.png)

    La boîte de dialogue **Enregistrer un fichier de connexion de données et terminer** l’endroit où vous fournissez des informations sur le fichier de connexion (*.odc) de base de données Office qui utilise Excel s’ouvre. Vous pouvez laisser les valeurs par défaut ou personnaliser vos sélections.

6. Vous pouvez laisser les valeurs par défaut, mais en particulier, notez le **Nom de fichier** . Une **Description**, un **Nom convivial**et des **Mots clés de recherche** et les autres utilisateurs n’oubliez pas ce que vous vous connectez à et recherchez la connexion. Cliquez sur **toujours tenter d’utiliser ce fichier pour actualiser les données** si vous souhaitez que les informations de connexion stockées dans le fichier odc peut mettre à jour lorsque vous y connecter, puis cliquez sur **Terminer**.

    ![Enregistrement d’un fichier odc](./media/sql-database-connect-excel/save-odc-file.png)

    La boîte de dialogue **Importer des données** s’affiche.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importer les données dans Excel et créer un graphique croisé dynamique
Maintenant que vous avez établi la connexion et créé le fichier avec les informations de connexion et de données, vous êtes en train de lire pour importer les données.

1. Dans la boîte de dialogue **Importer des données** , cliquez sur l’option souhaitée pour présenter vos données dans la feuille de calcul, puis sur **OK**. Nous avons choisi le **graphique croisé dynamique**. Vous pouvez également choisir de créer une **nouvelle feuille de calcul** ou pour **Ajouter ces données à un modèle de données**. Pour plus d’informations sur les modèles de données, voir [créer un modèle de données dans Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Cliquez sur **Propriétés** pour Explorer les informations sur le fichier odc que vous avez créé à l’étape précédente et pour définir les options d’actualisation des données.

    ![Choisir le format des données dans Excel](./media/sql-database-connect-excel/import-data.png)

    La feuille de calcul comporte maintenant un tableau croisé dynamique vide et le graphique.

8. Sous **Champs de tableau croisé dynamique**, sélectionnez toutes les cases à cocher-pour les champs que vous voulez afficher.

    ![Configurer le rapport de base de données.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Si vous voulez vous connecter d’autres feuilles de calcul et classeurs Excel à la base de données, cliquez sur **données**et cliquez sur **connexions**, cliquez sur **Ajouter**, sélectionnez la connexion que vous avez créé dans la liste, puis cliquez sur **Ouvrir**.
> ![Ouvrir une connexion à partir d’un autre classeur](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [se connecter à la base de données SQL avec SQL Server Management Studio](sql-database-connect-query-ssms.md) pour interroger avancées et l’analyse.
- En savoir plus sur les avantages des [pools élastiques](sql-database-elastic-pool.md).
- Découvrez comment [créer une application web qui se connecte à la base de données SQL sur le serveur principal](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
