<properties
   pageTitle="Requête SQL Azure Data Warehouse (Visual Studio) | Microsoft Azure"
   description="Requête SQL Data Warehouse avec Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Requête SQL Azure Data Warehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Utilisez Visual Studio pour requête SQL Azure Data Warehouse en quelques minutes. Cette méthode utilise l’extension de SQL Server Data Tools (SSDT) dans Visual Studio. 

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser ce didacticiel, vous devez :

+ Un SQL data warehouse existant. Pour en créer une, voir [créer un Data Warehouse SQL][].
+ SSDT pour Visual Studio. Si vous avez Visual Studio, vous disposez probablement déjà ceci. Pour obtenir des instructions d’installation et les options, voir [Installing Visual Studio et SSDT][].
+ Le nom complet du serveur SQL. Pour trouver cette, voir [se connecter à Data Warehouse SQL][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Connectez-vous à votre Data Warehouse SQL

1. Ouvrir Visual Studio 2013 ou 2015.
2. Ouvrez l’Explorateur d’objets SQL Server. Pour ce faire, cliquez sur **Afficher** > **Explorateur d’objets SQL Server**.

    ![Explorateur d’objets SQL Server][1]

3. Cliquez sur l’icône **Ajouter SQL Server** .

    ![Ajouter SQL Server][2]

4. Renseignez les champs dans la fenêtre se connecter au serveur.

    ![Se connecter au serveur][3]

    - **Nom du serveur**. Entrez le **nom du serveur** précédemment identifié.
    - **Authentification**. Sélectionnez **l’authentification SQL Server** ou **Active Directory intégré**.
    - **Nom d’utilisateur** et **mot de passe**. Entrez le nom d’utilisateur et mot de passe si l’authentification SQL Server ont été sélectionnée ci-dessus.
    - Cliquez sur **se connecter**.

5. Pour Explorer, développez votre serveur SQL Azure. Vous pouvez afficher les bases de données associées au serveur. Développez AdventureWorksDW pour afficher les tables dans votre base de données exemple.

    ![Explorer AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. exécuter un exemple de requête

À présent qu’une connexion a été établie à votre base de données, nous allons écrire une requête.

1. Clic droit sur votre base de données dans l’Explorateur d’objets SQL Server.

2. Sélectionnez **nouvelle requête**. Une nouvelle fenêtre de requête s’ouvre.

    ![Nouvelle requête][5]

3. Copiez cette requête TSQL dans la fenêtre de requête :

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Exécuter la requête. Pour ce faire, cliquez sur la flèche verte ou utilisez le raccourci suivant : `CTRL` + `SHIFT` + `E`.

    ![Exécuter la requête][6]

5. Examiner les résultats de la requête. Dans cet exemple, la table FactInternetSales contient 60398 lignes.

    ![Résultats de la requête][7]

## <a name="next-steps"></a>Étapes suivantes

À présent que vous pouvez vous connecter et de la requête, essayez de [visualiser les données avec PowerBI][].

Pour configurer votre environnement pour l’authentification Azure Active Directory, consultez [authentifier vers SQL Data Warehouse][].

<!--Arcticles-->
[Se connecter à Data Warehouse SQL]: sql-data-warehouse-connect-overview.md
[Créer un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
[L’installation de SSDT et Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Authentification SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualiser les données avec PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
