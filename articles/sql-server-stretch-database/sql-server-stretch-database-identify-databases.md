<properties
    pageTitle="Identifier des bases de données et des tables de base de données étirement en exécutant étirement de base de données Gestionnaire | Microsoft Azure"
    description="Apprenez à identifier les bases de données et les tables qui sont adaptées étirement de base de données."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identifier des bases de données et des tables de base de données étirement en exécutant étirement de base de données Gestionnaire

Pour identifier les bases de données et des tables qui sont adaptées étirement de base de données, téléchargez le Conseiller de mise à niveau SQL Server 2016, puis exécutez le Conseiller de base de données étirement. Étirement Conseiller de base de données identifie également les problèmes de blocage.

## <a name="download-and-install-upgrade-advisor"></a>Télécharger et installer le Conseiller de mise à niveau
Téléchargez et installez le Conseiller de mise à niveau à partir [d’ici](http://go.microsoft.com/fwlink/?LinkID=613421). Cet outil n’est pas inclus dans le support d’installation de SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Exécuter le conseiller étirement de base de données

1.  Exécuter le Conseiller de mise à niveau.

2.  Sélectionnez **scénarios**, puis **Exécutez étirement de base de données ADVISOR**.

3.  Dans la carte **Exécuter le conseiller étirement de base de données** , cliquez sur **Sélectionner les bases de données à analyser**.

4.  Dans la carte **Sélectionnez bases de données** , entrez ou sélectionnez le nom du serveur et les informations d’authentification. Cliquez sur **se connecter**.

5.  Une liste des bases de données sur le serveur sélectionné apparaît. Sélectionnez les bases de données que vous souhaitez analyser. Cliquez sur **Sélectionner**.

6.  Dans la carte **Exécuter le conseiller étirement de base de données** , cliquez sur **exécuter**.  L’analyse s’exécute.

## <a name="review-the-results"></a>Examiner les résultats

1.  Lorsque l’analyse est terminée, sur la carte **analyser les bases de données** , sélectionnez une des bases de données qui vous analysées pour afficher la carte de **résultats de l’analyse** .

    La carte de **résultats de l’analyse** répertorie les tables recommandés dans la base de données sélectionnée qui répondent aux critères recommandation par défaut.

2.  Dans la liste des tables sur la carte de **résultats de l’analyse** , sélectionnez une des tables recommandés pour afficher la carte de **résultats dans le tableau** .

    Si les problèmes de blocage, la carte de **résultats dans le tableau** répertorie les problèmes de blocage de la table sélectionnée. Pour plus d’informations sur les problèmes de blocage détecté par le Gestionnaire d’étirement de base de données, voir [Limitations de base de données étirement](sql-server-stretch-database-limitations.md).

3.  Dans la liste des problèmes de blocage sur la carte de **résultats dans le tableau** , sélectionnez une des problèmes pour afficher plus d’informations sur le problème sélectionné et propose des étapes d’atténuation. Suivre les étapes atténuation suggérée si vous voulez configurer la table sélectionnée pour étirement de base de données.

## <a name="next-step"></a>Étape suivante
Activer la base de données étirement.

-   Pour activer la base de données étirer sur une **base de données**, voir [Activer étirement de base de données pour une base de données](sql-server-stretch-database-enable-database.md).

-   Pour activer étirement de base de données sur une autre **table**, lorsque étirement est déjà activé sur la base de données, voir [Activer étirement de base de données pour une table](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Voir aussi

[Limitations de base de données étirement](sql-server-stretch-database-limitations.md)

[Activer étirement de base de données pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer étirement de base de données pour une table](sql-server-stretch-database-enable-table.md)

[Toutes les rubriques de service de base de données Azure SQL Server étirement](sql-server-stretch-database-index-all-articles.md)
