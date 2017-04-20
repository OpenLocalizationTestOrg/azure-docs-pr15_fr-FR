<properties
   pageTitle="Visualiser des données SQL Data Warehouse avec Power BI Microsoft Azure"
   description="Visualiser des données SQL Data Warehouse avec Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualiser des données avec Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprentissage automatique Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ce didacticiel montre comment utiliser Power BI pour vous connecter à SQL Data Warehouse et créer des visualisations de base.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Conditions préalables

Pour lancer ce didacticiel, vous devez :

- Un Data Warehouse SQL préchargé avec la base de données AdventureWorksDW. Pour ce service, voir [créer un Data Warehouse SQL][] et choisir de charger les exemples de données. Si vous possédez un data warehouse déjà mais n’avez pas de données d’exemple, vous pouvez [charger les exemples de données manuellement][].


## <a name="1-connect-to-your-database"></a>1. Connectez-vous à votre base de données

Ouvrez Power BI et se connecter à votre base de données AdventureWorksDW :

1. Se connecter au [portail Azure][].
2. Cliquez sur **bases de données SQL** et choisissez votre base de données AdventureWorks SQL Data Warehouse.

    ![Trouver votre base de données][1]

3. Cliquez sur le bouton « Ouvrir dans Power BI ».

    ![Bouton de Power BI][2]

4. Vous devez maintenant voir la page de connexion SQL Data Warehouse affiche l’adresse web de votre base de données. Cliquez sur Suivant.

    ![Connexion de BI Power][3]

6. Entrez votre nom d’utilisateur Azure SQL server et mot de passe et vous sera entièrement connecté à votre base de données SQL Data Warehouse.

    ![Connexion Power BI][4]

7. Une fois que vous êtes connecté au Power BI, cliquez sur le jeu de données AdventureWorksDW sur la carte gauche. Cette action ouvre la base de données.

    ![Power BI ouvrir AdventureWorksDW][5]



## <a name="2-create-a-report"></a>2. créer un rapport

Vous êtes maintenant prêt à utiliser Power BI pour analyser vos données exemple AdventureWorksDW. Pour effectuer l’analyse, AdventureWorksDW comporte une vue appelée AggregateSales. Cet affichage contient quelques les indicateurs clés pour analyser les ventes de l’entreprise.

1. Pour créer un mappage des montants des ventes en fonction du code postal, dans le volet droit de champs, cliquez sur la vue AggregateSales pour le développer. Cliquez sur les colonnes CodePostal et SalesAmount pour les sélectionner.

    ![Power BI sélectionnez AggregateSales][6]

    Power BI reconnaît automatiquement ce est les données géographiques et la placer dans une carte pour vous.

    ![Carte Power BI map][7]

2. Cette étape permet de créer un graphique à barres qui affiche le montant des ventes par revenu client. Pour créer ce accéder au mode AggregateSales développé. Cliquez sur le champ SalesAmount. Faites glisser le champ revenu client vers la gauche et déposez-le dans axe.

    ![Axe de sélection de Power BI][8]

    Nous avons déplacé le graphique à barres sur le côté gauche.

    ![Power BI barre][9]

3. Cette étape permet de créer un graphique en courbes qui affiche le montant des ventes par date de commande. Pour créer ce accéder au mode AggregateSales développé. Cliquez sur SalesAmount et OrderDate. Dans les visualisations de colonne, cliquez sur l’icône de graphique en courbes ; Il s’agit de la première icône dans la deuxième ligne sous visualisations.

    ![Graphique en courbes select Power BI][10]

    Vous avez à présent un état qui affiche les trois différentes visualisations des données.

    ![Ligne de BI Power][11]

Vous pouvez enregistrer votre avancement à tout moment en cliquant sur **fichier** et sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
À présent que nous avons donné du temps se met en route avec les exemples de données, voir comment [développer][], [charger][]ou [migrer][]. Ou consultez le [site Web de Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[charger les exemples de données manuellement]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Créer un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portail Azure]: https://portal.azure.com/
[Site Web de Power BI]: http://www.powerbi.com/
