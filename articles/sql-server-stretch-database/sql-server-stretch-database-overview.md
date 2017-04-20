<properties
    pageTitle="Étirer vue d’ensemble de la base de données | Microsoft Azure"
    description="Découvrez comment étirement de base de données migre vos données froid en toute sécurité et en toute transparence dans le cloud Microsoft Azure."
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
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Étirer vue d’ensemble de la base de données

Base de données étirement migre vos données froid en toute sécurité et en toute transparence dans le cloud Microsoft Azure.

Si vous voulez simplement prise en main de la base de données étirement immédiatement, voir [prise en main en exécutant la base de données pour étirement Assistant Activer](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quels sont les avantages de base de données étirement ?
Base de données étirement offre les avantages suivants :

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fournit des coûts\-disponibilité efficace pour ces données
Étirer chaudes et froides transactions dynamiquement des données SQL Server à Microsoft Azure avec la base de données SQL Server étirement. Contrairement de stockage des données à froid classique, vos données sont toujours en ligne et disponible à la requête. Vous pouvez fournir plus chronologies de rétention de données sans rompre la banque de grands tableaux comme historique des commandes client. Bénéficier de faible coût d’Azure plutôt que la mise à l’échelle coûteux sur\-locaux de stockage. Vous choisissez le niveau de tarification et configurez les paramètres du portail Azure pour garder le contrôle sur les coûts. Mettre à l’échelle vers le haut ou vers le bas selon vos besoins. Visitez la page [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) pour plus d’informations.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Ne nécessite pas de modifications apportées aux requêtes ou des applications
Accéder à vos données SQL Server en toute transparence utilisiez s’il s’agit\-locaux ou elle est étirée dans le cloud.  Vous définissez la stratégie qui détermine l’emplacement de stockage des données et SQL Server gère le déplacement des données en arrière-plan. L’intégralité du tableau est toujours en ligne et utilisable dans une requête. Et, étirement de base de données ne nécessite pas les modifications apportées aux requêtes existantes ou des applications, l’emplacement des données est complètement transparente à l’application.

### <a name="streamlines-on-premises-data-maintenance"></a>Rationalise sur\-locaux maintenance des données
Réduire sur\-espace de stockage pour vos données et de la maintenance des locaux. Effectuer des sauvegardes régulières pour votre sur\-locaux données s’exécutent plus rapidement et de fin dans la fenêtre de maintenance. Effectuer des sauvegardes pour la partie cloud de vos données automatiquement. Votre sur\-besoins de stockage local sont considérablement réduits. Stockage Azure peut être 80 % moins cher que l’ajout d’activé\-locaux SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>Conserve vos données sécurisées même lors de la migration
Profitez de toute sérénité comme vous étirez vos applications les plus importantes en toute sécurité dans le cloud. Toujours chiffrées SQL Server fournit un chiffrement pour vos données en mouvement. Sécurité de niveau ligne (RLS) et d’autres fonctionnalités de sécurité de SQL Server avancées également confier étirement de base de données pour protéger vos données.

## <a name="what-does-stretch-database-do"></a>Que faire étirement de base de données ?
Après avoir activé étirement de base de données pour une instance de SQL Server, une base de données et au moins une table, base de données étirement commence silencieusement migrer vos données froides vers Azure.

-   Si vous stockez ces données dans une table distincte, vous pouvez déplacer l’intégralité du tableau.

-   Si votre table contient des données chaudes et froides, vous pouvez spécifier une fonction de filtre pour sélectionner les lignes à déplacer.

**Vous n’êtes pas obligé de modifier les requêtes existantes et les applications clientes.** Vous avez encore d’un accès transparent aux données locales et à distance, même pendant la migration de données. Il est un peu de latence des requêtes à distance, mais vous rencontrez uniquement cette latence lorsque vous exécutez une requête les données à froid.

La **que base de données étirement garantit qu’aucune donnée n’est perdue** si une erreur se produit lors de la migration. Il possède également une logique de traiter les problèmes de connexion pouvant se produire pendant la migration. Une vue de gestion dynamique indique l’état de la migration.

**Vous pouvez en pause la migration de données** pour résoudre les problèmes sur le serveur local ou afin d’optimiser la bande passante réseau disponible.

![Vue d’ensemble de la base de données étirement][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>Base de données étirement vous concerne ?
Si vous pouvez apporter les instructions suivantes, étirement de base de données peuvent vous aider à répondre à vos besoins et résoudre vos problèmes.

|Si vous êtes un décideurs|Si vous êtes un utilisateur DBA|
|------------------------------|-------------------|
|Je dois conserver les données pendant une longue période.|La taille de ma tables se déconnecter de contrôle.|
|J’ai parfois interroger les données à froid.|Mes utilisateurs disent qu’ils souhaitent accéder à ces données, mais elles utilisent rarement il.|
|J’ai applications, y compris les anciennes applications, que je ne souhaite pas mettre à jour.|Je dois conserver achetant et en ajoutant davantage de stockage.|
|Je veux trouver un moyen de réaliser des économies sur le stockage.|Je ne parviens pas à sauvegarder ou restaurer ces tables volumineuses dans le contrat SLA.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Quel type de bases de données et de tables sont adaptées au étirement de base de données ?
Écarter les bases de données de base de données cibles transactions avec de grandes quantités de données à froid, généralement stockées dans un petit nombre de tables. Ces tables peuvent contenir plusieurs un milliards de lignes.

Si vous utilisez la fonctionnalité de tableau temporel de SQL Server 2016, utiliser étirement de base de données pour déplacer tout ou partie de la table historique associés aux coûts\-efficace de stockage dans Azure. Pour plus d’informations, voir [Gérer rétention des données historiques dans des Tables temporelles version système](https://msdn.microsoft.com/library/mt637341.aspx).

Utilisez étirement Advisor de base de données, une fonctionnalité de SQL Server 2016 Upgrade Advisor, pour identifier les bases de données et des tables de base de données étirement. Pour plus d’informations, voir [identifier les bases de données et des tables de base de données étirement](sql-server-stretch-database-identify-databases.md). Pour en savoir plus sur les problèmes de blocage potentiels, voir [Limitations de base de données étirement](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Testez étirement de base de données
**Testez étirement de base de données avec la base de données exemple AdventureWorks.** Pour obtenir la base de données exemple AdventureWorks, téléchargez-la au moins le fichier de base de données et le fichier exemples et des scripts à partir [d’ici](https://www.microsoft.com/download/details.aspx?id=49502). Une fois que vous restaurez la base de données exemple sur une instance de SQL Server 2016, décompresser le fichier exemples et ouvrez le fichier exemples DB étirement à partir du dossier de base de données étirement. Exécuter les scripts dans ce fichier pour vérifier l’espace utilisé par vos données avant et après l’activation d’étirement de base de données, pour suivre la progression de la migration de données et pour confirmer que vous pouvez continuer à interroger des données existantes et insérer de nouvelles données pendant et après la migration de données.

## <a name="next-step"></a>Étape suivante
**Identifier les bases de données et des tables qui sont adaptées étirement de base de données.** Téléchargez le Conseiller de mise à niveau SQL Server 2016, puis exécutez le Conseiller de base de données étirement pour identifier les bases de données et les tables qui sont adaptées étirement de base de données. Étirement Conseiller de base de données identifie également les problèmes de blocage. Pour plus d’informations, voir [identifier les bases de données et des tables de base de données étirement](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
