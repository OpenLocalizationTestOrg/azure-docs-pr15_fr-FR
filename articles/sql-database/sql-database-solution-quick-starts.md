<properties
   pageTitle="Démarrage rapide de Solution base de données SQL Azure | Microsoft Azure"
   description="Découvrez les Solutions de base de données SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Explorer les Démarrages rapides de Solution de base de données SQL Azure

Cet article contient une vue d’ensemble du démarrage rapide d’Azure SQL de base de données Solution. Ces Démarrages rapides sont situés dans le référentiel exemples GitHub SQL Server et illustrent l’utilisation de la base de données SQL dans une solution complète basée sur des scénarios réels. Pour les didacticiels pas à pas simples qui décrivent l’utilisation d’une fonction particulière de la base de données SQL, consultez [didacticiels Explorer de base de données SQL Azure](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Essayez la démonstration WingTipTickets et les exercices pratiques

La démonstration [WingTipTickets de base de données SQL Azure](https://github.com/microsoft/wingtiptickets) et pratiques illustrent une base de données SQL Azure et exemple basés sur Azure recherche d’application qui est utilisée pour vendre tickets de concert.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Collecter et analyser des données d’utilisation des ressources sur plusieurs pools

[Solution de démarrage rapide : télémétrie Pool élastique à l’aide de PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) fournit une solution pour la collecte et utilisation des ressources de base de données SQL de surveillance dans l’ensemble des pools multiples dans un abonnement. Lorsque vous avez un grand nombre de bases de données dans un abonnement, il est difficile à surveiller chaque pool élastique séparément.

Pour résoudre ce problème, vous pouvez combiner les applets de commande PowerShell de base de données SQL et requêtes T-SQL pour collecter les données d’utilisation des ressources des pools plusieurs et leurs bases de données. Cela vous aide à surveiller et analyser l’utilisation des ressources plus efficacement.

Ce démarrage rapide fournit un ensemble de scripts PowerShell et requêtes T-SQL ainsi que de documentation sur rôle de la solution et comment l’implémenter.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Prise en main élastique de base de données dans un scénario SaaS

 [Solution de démarrage rapide : tableau de bord personnalisé Pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) fournit une solution pour un scénario comme-a-Solution logicielle (SaaS) qui s’appuie sur la fonctionnalité de base de données élastique de base de données SQL pour fournir une base de données rentables et scalable principale pour une application SaaS.

Dans cette solution, vous guidera dans l’implémentation d’une application web. Cette application web vous permet de visualiser le chargement est créé dans une base de données élastique par un générateur de charge qui utilise un tableau de bord personnalisé qui complète du portail Azure.

Ce démarrage rapide fournit un générateur de charge et d’un contrôle web app ainsi que la documentation relative à ce que l’application fait et comment l’utiliser.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Créer une base de données SQL Azure à l’aide de développement Code First et de l’infrastructure d’entités

L’exemple de [Code tout d’abord une nouvelle base de données](https://msdn.microsoft.com/data/jj193542.aspx) et vidéo fournit une introduction au développement Code First qui cible une base de données. Ce scénario cible une base de données qui n’existe pas, mais qui seront créé par Code First. Vous pouvez également le scénario crée une base de données vide dans lequel Code First ajoute les nouveaux tableaux.

Code tout d’abord vous permet de définir votre modèle à l’aide en cours c# ou Visual Basic .NET. Vous pouvez effectuer une configuration supplémentaire facultative en utilisant des attributs sur votre cours et les propriétés ou en utilisant une API fluent.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Intégrer des outils de base de données élastique dans une application Framework entité

L’exemple de [bibliothèque de client de base de données élastique avec Framework entité](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) montre les modifications que vous devez y apporter à une application Framework entité intégrer avec les [Outils de base de données élastique](sql-database-elastic-scale-get-started.md). Le focus se trouve sur [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md) et le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l’entité Framework Code première approche de rédaction.

Le [Code tout d’abord une nouvelle base de données exemple pour FE](http://msdn.microsoft.com/data/jj193542.aspx) sert de notre exemple en cours d’exécution dans l’ensemble de cet exemple. L’exemple de code qui accompagne ce document fait partie de l’ensemble d’outils élastique de base de données des échantillons dans les exemples de code Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Intégrer des outils de base de données élastique sécurité au niveau de la ligne

[Applications partagées avec des outils de base de données élastique et sécurité au niveau de la ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md) affiche les modifications que vous devez apporter à une application Framework entité pour intégrer des [Outils de base de données élastique](sql-database-elastic-scale-get-started.md) avec [sécurité au niveau de la ligne](https://msdn.microsoft.com/library/dn765131). Cet exemple illustre comment utiliser ces technologies ensemble pour créer une application avec un niveau de données extensibles hautement qui prend en charge le milieu des fragments partagés.

Pour cela, à l’aide d’ADO.NET SqlClient ou entité Framework. Cet exemple étend la [bibliothèque de client de base de données élastique avec Framework entité](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) en ajoutant prise en charge des bases de données partagé partagé.
Il crée une application console simple pour la création de blogs et billets, avec quatre clients et deux bases de données partagé partagé.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Créer des enquêtes en ligne avec l’application Tailspin enquêtes

Cet [exemple d’enquêtes Tailspin application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) est une application web partagé, appelée enquêtes, qui permet aux utilisateurs de créer des sondages en ligne. L’exemple corrige certains problèmes clés sur la gestion des identités utilisateur dans une application partagée, y compris d’abonnement, l’authentification, l’autorisation et rôles d’application.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>En savoir plus sur les dernières fonctionnalités de sécurité de base de données SQL avec l’Application de démonstration de stage Contoso

Cette [application Contoso clinique démo](https://github.com/Microsoft/azure-sql-security-sample) présente les dernières fonctionnalités de sécurité de base de données SQL.

## <a name="next-steps"></a>Étapes suivantes

[Explorer les didacticiels de base de données SQL Azure](sql-database-explore-tutorials.md)
