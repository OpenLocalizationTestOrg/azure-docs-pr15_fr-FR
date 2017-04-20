<properties
    pageTitle="Quelle est la base de données SQL ? Introduction à la base de données SQL | Microsoft Azure"
    description="Obtenir une présentation de base de données SQL : détails techniques et fonctionnalités de Microsoft relationnelles base de données système de gestion (SGBDR) dans le cloud."
    keywords="Introduction à sql, Introduction à sql, quelle est la base de données sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>Quelle est la base de données SQL ? Introduction à la base de données SQL

Base de données SQL est un service de base de données relationnelle en nuage basé sur le moteur de Microsoft SQL Server pointe, avec des fonctions critiques. Base de données SQL de performances prévisibles, extensibilité élevées sans interruptions liées aux tâches, la continuité des activités et la protection des données, tout avec administration proche de zéro. Vous pouvez vous concentrer sur développement d’applications rapide et accélérer sur le marché, plutôt que Gestion des machines virtuelles et l’infrastructure. Parce qu’il est basé sur le moteur de [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , base de données SQL prend en charge des outils SQL Server existant, bibliothèques et des API, ce qui facilite la déplacer et étendre dans le cloud.

Cet article est une introduction aux fonctionnalités liées aux performances et extensibilité élevées facilité de gestion, avec des liens pour Explorer les détails et les concepts de base de données SQL. Si vous êtes prêt à travailler dans, vous pouvez [créer votre première base de données SQL](sql-database-get-started.md) ou [créer un pool de base de données élastique](sql-database-elastic-pool-create-portal.md) en minutes. Si vous voulez obtenir de plus amples, regardez cette vidéo 30 minutes.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuster les performances et échelle sans interruption de service

Bases de données SQL est disponible dans basique, Standard et Premium *niveaux de service*. Chaque niveau de service offre [différents niveaux de performances et de fonctionnalités](sql-database-service-tiers.md) pour prendre en charge lightweight aux charges de travail lourds de base de données. Vous pouvez créer votre première application dans une base de données petite pour quelques euros mois, puis [Modifier le niveau de service](sql-database-scale-up.md) manuellement ou par programme à tout moment comme votre application accède viruse dans le monde entier, sans interruption de service pour votre application ou vos clients.

Pour de nombreux d’entreprises et d’applications, la possibilité de créer des bases de données et de numérotation de performances de base de données unique vers le haut ou vers le bas à la demande est suffisant, surtout si des modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d’utilisation inattendus, il est difficile à gérer les coûts et votre modèle d’entreprise.

[Pools élastiques](sql-database-elastic-pool.md) dans la base de données SQL pour résoudre ce problème. Le concept est simple. Vous allouez performances vers un regroupement et payez pour les performances collectives du pool plutôt que les performances de base de données unique. Vous n’avez pas besoin composer des performances de base de données vers le haut ou vers le bas. Les bases de données dans le pool, appelé *élastiques bases de données*, ajuster automatiquement vers le haut et vers le bas jusqu'à la demande de réunion. Bases de données élastiques consomment mais ne dépassent pas les limites du pool, de sorte que votre coût reste prévisible même si l’utilisation de base de données ne. De plus, vous pouvez [Ajouter et supprimer des bases de données vers le pool](sql-database-elastic-pool-manage-portal.md), mise à l’échelle de votre application à partir d’un petit nombre de bases de données à des milliers, toutes les tâches dans un budget que vous contrôlez. Pour en savoir plus sur les modèles de conception pour les applications SaaS utilisant des pools élastiques, voir [Modèles de conception pour les Applications de SaaS client à plusieurs avec la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Dans les deux cas, vous accédez — simples ou élastique : vous n’êtes pas obligé de. Vous pouvez fusionner les bases de données unique avec des pools élastique de base de données et modifier les niveaux de service de bases de données unique et pools pour créer des conceptions innovantes. Par ailleurs, avec la puissance et la portée d’Azure, vous pouvez et combiner des services Azure avec base de données SQL de répondent à vos besoins uniques application moderne, renforcer l’efficacité des coûts et des ressources et déverrouiller les nouvelles opportunités commerciales.

Mais comment vous comparez les performances relatives des bases de données et des pools de base de données ? Comment savoir le taquet cliquez avec le bouton droit sur lorsque vous composez monter et Descendre ? La réponse est l’unité de Transaction de base de données (DTU) pour les bases de données unique et la DTU élastique (eDTU) pour les bases de données élastiques et pools de base de données. Voir [performances et options de base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus d’informations.

## <a name="keep-your-app-and-business-running"></a>Conserver votre application et entreprise en cours d’exécution

Secteur non significatif 99,99 % disponibilité niveau contrat de service du Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), optimisé par un réseau global de centres de données gérés par Microsoft, permet de maintenir votre application en cours d’exécution 24/7. Chaque base de données SQL, vous tirer parti de la protection des données intégrée, tolérance de panne et protection des données que vous devriez dans le cas contraire à concevoir, acheter, créer et gérer. Même dans ce cas, en fonction des besoins de votre entreprise, vous pouvez exiger des niveaux supplémentaires de protection afin de votre application et votre entreprise peuvent récupérer rapidement en cas de sinistre, un message d’erreur ou autre chose. Avec base de données SQL, chaque niveau de service propose un menu différent de fonctionnalités que vous pouvez utiliser pour vous aider et en cours d’exécution et restez par catégorie. Vous pouvez utiliser restaurer point-à-temps pour renvoyer une base de données à un état antérieur, jusqu’au premier 35 jours. En outre, si le centre de données qui héberge vos bases de données connaît une panne, vous pouvez basculer à duplications de base de données dans une zone différente. Ou vous pouvez utiliser des doubles pour mises à niveau ou d’un déménagement à différentes régions.

![Geo-réplication de base de données SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Pour plus d’informations sur les fonctionnalités de continuité différents business disponibles pour les niveaux de service différent, voir [Continuité des activités](sql-database-business-continuity.md) .

## <a name="secure-your-data"></a>Protéger vos données
SQL Server possède un sécurité des données unie que base de données SQL respectueux avec des fonctionnalités qui limitent l’accès, protéger vos données et vous aident à contrôler l’activité. Pour un récapitulatif rapide des options de sécurité dans la base de données SQL, consultez [sécurisation de votre base de données SQL](sql-database-security.md) . Consultez le [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL](https://msdn.microsoft.com/library/bb510589) pour une vue plus complète des fonctionnalités de sécurité. Et visiter le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/security/) pour plus d’informations sur la sécurité de plateforme d’Azure.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez lu présentation de base de données SQL et répondu à la question « Qu’est base de données SQL ? », vous êtes prêt à :

- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/) pour la base de données unique et des comparaisons de coûts élastique de base de données et calculatrices.
- Obtenir des informations à propos des [pools élastiques](sql-database-elastic-pool.md).
- Commencer à [créer votre première base de données](sql-database-get-started.md).
- [Se connecter et de la requête avec SSMS](sql-database-connect-query-ssms.md)
- Créer votre première application c#, Java, Node.js, PHP, Python ou Ruby : [les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
- Afficher un index des titres et les descriptions de [toutes les rubriques de service de base de données sql Azure](sql-database-index-all-articles.md).
