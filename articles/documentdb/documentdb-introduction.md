<properties 
    pageTitle="Présentation de DocumentDB, une base de données JSON | Microsoft Azure" 
    description="En savoir plus sur Azure DocumentDB, une base de données NoSQL JSON. Cette base de données de document est conçu pour big data, extensibilité élevées élastique et disponibilité." 
    keywords="base de données JSON, base de données de document"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introduction à DocumentDB : une base de données NoSQL JSON

##<a name="what-is-documentdb"></a>Quelles sont les DocumentDB ?

DocumentDB est un service de base de données NoSQL entièrement géré conçu pour les performances rapides et prévisibles, haute disponibilité, mise à l’échelle élastique, distribution global et facilité de développement. Comme exempt de schéma NoSQL base de données, DocumentDB fournit des fonctions de requête SQL riches et familiers avec latence faible cohérent sur les données JSON - s’assurer que 99 % de vos lectures sont pris en charge sous 10 millisecondes et 99 % de votre écriture sont pris en charge sous 15 millisecondes. Ces avantages uniques marque DocumentDB une grande adaptée web, mobile, jeu et IoT et nombreuses autres applications ayant besoin d’échelle transparente et réplication globale.

## <a name="how-can-i-learn-about-documentdb"></a>Comment puis-je obtenir des informations sur DocumentDB ? 

Une méthode rapide pour en savoir plus sur DocumentDB et voyez par vous-même consiste à suivre ces trois étapes : 

1. Regarder la minute deux [What DocumentDB ?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) vidéo, qui présente les avantages liés à l’aide de DocumentDB.
2. Regardez la minute trois [DocumentDB créer sur Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vidéo, qui met en surbrillance de la prise en main DocumentDB à l’aide du portail Azure.
3. Visitez le [Laboratoire de requête](http://www.documentdb.com/sql/demo), où vous pouvez expliquant en détail différentes activités pour en savoir plus sur les riches fonctionnalités de requête disponibles dans DocumentDB. Ensuite, à l’onglet Sandbox de tête sur vos propres requêtes SQL personnalisées et exécutez expérimenter DocumentDB.

Revenez ensuite à cet article, où nous allons pour approfondir.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>Les fonctionnalités et les fonctionnalités clés offertes par DocumentDB ?  

DocumentDB Azure propose les principales fonctionnalités suivantes et les avantages :

-   **Débit ELASTIQUEMENT scalable et stockage :** Facilement évoluer ou mettre à l’échelle vers le bas de votre base de données DocumentDB JSON pour répondre aux besoins de vos applications. Vos données sont stockées sur des disques Flash (SSD) de faible latence prévisibles. DocumentDB prend en charge les conteneurs de stockage des données JSON appelées pouvant évoluer à débit généré et tailles de stockage quasiment illimitée. Vous pouvez ELASTIQUEMENT évoluer DocumentDB avec des performances prévisibles en toute transparence fur et à votre application. 

-   **Plusieurs région réplication :** DocumentDB réplique en toute transparence vos données à tous les régions que vous avez associé à votre compte DocumentDB, ce qui vous permet de développer des applications qui nécessitent un accès global aux données tout en fournissant compromis entre la cohérence et les performances, toutes les garanties correspondantes. DocumentDB fournit basculement régionaux transparent avec API hébergement multiple et la possibilité de mettre à l’échelle ELASTIQUEMENT débit et stockage dans le monde. Découvrez plus en [distribuer des données globalement avec DocumentDB](documentdb-distribute-data-globally.md).

-   **Requêtes Ad hoc avec une syntaxe SQL courante :** Stocker des documents JSON hétérogènes dans DocumentDB et interroger ces documents grâce à une syntaxe SQL familier. DocumentDB utilise un verrou hautement simultané, gratuit, journal structurées technologie d’indexation à indexer automatiquement tout le contenu du document. Cela permet des requêtes en temps réel riches sans avoir besoin de spécifier des indicateurs de schéma, index secondaires ou des vues. Pour plus d’informations [DocumentDB de requête](documentdb-sql-query.md). 

-   **L’exécution de JavaScript au sein de la base de données :** Expression logique de l’application comme des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF) à l’aide d’un code JavaScript standard. Ainsi, votre logique de l’application de fonctionner sur les données sans vous soucier de la différence entre l’application et le schéma de base de données. DocumentDB fournit la d’exécution de la logique de l’application JavaScript directement à l’intérieur du moteur de base de données. L’intégration de JavaScript autorise l’exécution d’insertion, remplacer, DELETE et opérations de sélection à partir d’un programme JavaScript comme étant une transaction isolée. Pour plus d’informations [DocumentDB côté serveur programmation](documentdb-programming.md).

-   **Niveaux de cohérence ACCORDABLE :** Sélectionner à partir de quatre niveaux de cohérence bien définies jusqu'à atteindre optimale compromis entre la cohérence et les performances. Pour les requêtes et les opérations de lecture, DocumentDB propose quatre niveaux de cohérence distinct : forte, délimitée-obsolescence, session et éventuelle. Ces niveaux de cohérence granulaires et bien définis vous autorise à effectuer son compromis entre la cohérence, la disponibilité et latence. Pour plus d’informations [à l’aide de niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB](documentdb-consistency-levels.md).

-   **Entièrement gérés :** Inutile pour gérer les ressources de base de données et machine. Comme un service de Microsoft Azure entièrement gérés, ne pas besoin gérer des machines virtuelles déployer et configurer les logiciels, gérer la mise à l’échelle, ou traiter les mises à niveau de la couche de données complexes. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionaux. Vous pouvez facilement ajouter un compte DocumentDB et mise en service de capacité que vous le souhaitez, ce qui permet de vous recentrer sur votre application au lieu d’exploitation et la gestion de votre base de données. 

-   **Ouvert par la conception :** Commencer rapidement à l’aide d’outils et les compétences existantes. Programmation DocumentDB est simple, conviviale et ne nécessite pas d’adoption de nouveaux outils ou adhérer à des extensions personnalisées JSON ou un code JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, requête et JavaScript traitement via une interface HTTP RESTful simple. DocumentDB adopte existants, langues, standards et formats tout en bénéficiant des fonctionnalités de base de données sur les valeur élevée.

-   **L’indexation automatique :** Par défaut, DocumentDB [index automatiquement](documentdb-indexing.md) tous les documents dans la base de données et ne pas attendre ou requièrent un schéma ou la création d’index secondaire. Ne voulez pas indexer tout ? Ne vous inquiétez pas, vous pouvez [cesser d’utiliser vos fichiers JSON les chemins d’accès](documentdb-indexing-policies.md) trop.

##<a name="data-management"></a>Comment DocumentDB gérer des données ?

DocumentDB Azure gère les données JSON au moyen de ressources de base de données précis. Ces ressources sont répliquées de disponibilité et sont non-parasites par leur URI logique. DocumentDB offre qu'un simple HTTP en fonction de modèle de programmation RESTful pour toutes les ressources. 

Le compte de base de données DocumentDB est un espace de noms unique qui permet d’accéder à DocumentDB Azure. Avant de pouvoir créer un compte de base de données, vous devez disposer d’un abonnement Azure, qui permet d’accéder à un éventail de services Azure. 

Toutes les ressources DocumentDB sont comme modèle et stockées en tant que documents JSON. Les ressources sont gérées comme éléments, qui sont JSON des documents contenant les métadonnées et en tant que flux qui sont des collections d’éléments. Ensembles d’éléments figurent dans leurs flux correspondantes.

L’image ci-dessous montre les relations entre les ressources DocumentDB :

![La relation hiérarchique entre les ressources dans DocumentDB, une base de données NoSQL JSON][1] 

Un compte de base de données est constitué d’un ensemble de bases de données, chacun contenant plusieurs collections de sites, chacun d'entre eux peut contenir des procédures stockées, déclencheurs, UDF, documents et les pièces jointes connexes. Une base de données est également associé à des utilisateurs, chacune avec un ensemble d’autorisations pour accéder aux différentes d’autres collections de sites, des procédures stockées, déclencheurs, UDF, des documents ou les pièces jointes. Tandis que les bases de données, les utilisateurs, les autorisations et les collections de sites sont définies par le système de ressources avec des schémas connus : documents, des procédures stockées, des déclencheurs, des UDF et des pièces jointes contiennent arbitraire, définies par l’utilisateur du contenu JSON.  

##<a name="develop"></a>Comment puis-je développer des applications avec DocumentDB ?

Azure DocumentDB expose ressources via une API REST qui peut être appelé par n’importe quelle langue capable de procéder à des demandes HTTP/HTTPS. En outre, DocumentDB fournit des bibliothèques de programmation pour plusieurs langues courantes. Ces bibliothèques simplifient de nombreux aspects du travail avec Azure DocumentDB en gérant des détails tels que la mise en cache de l’adresse, gestion des exceptions, nouvelles tentatives automatiques et ainsi de suite. Les bibliothèques sont actuellement disponibles pour les langues et plateformes suivants :  

Télécharger | Documentation
--- | ---
[KIT DE DÉVELOPPEMENT .NET](http://go.microsoft.com/fwlink/?LinkID=402989) | [Bibliothèque .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Kit de développement logiciel Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) | [Bibliothèque Node.js](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Bibliothèque Java](http://azure.github.io/azure-documentdb-java/)
[Kit de développement logiciel JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Bibliothèque JavaScript](http://azure.github.io/azure-documentdb-js/)
n/a | [Kit de développement JavaScript côté serveur](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Bibliothèque Python](http://azure.github.io/azure-documentdb-python/)

Créer, lire, mettre à jour et supprimer des opérations au-delà de base, DocumentDB fournit une interface de requête SQL riche pour récupérer les documents JSON et côté serveur prend en charge pour l’exécution de transactions JavaScript de logique d’application. Les interfaces de l’exécution de requêtes et de script sont disponibles à toutes les bibliothèques de plateforme ainsi que les API REST. 

### <a name="sql-query"></a>Requête SQL
Azure DocumentDB prend en charge l’interrogation de documents à l’aide d’un langage SQL, qui est associé à une racine dans le système de type JavaScript et expressions avec prise en charge pour les requêtes relationnelles, hiérarchiques et spatiales. Le langage de requête DocumentDB est une interface simple mais puissante d’interroger les documents JSON. La langue prend en charge un sous-ensemble de la grammaire SQL ANSI et ajoute intégration complète de l’objet JavaScript, les tableaux, construction d’un objet et appel de la fonction. DocumentDB fournit son modèle de requête sans n’importe quel schéma explicite ou des conseils d’indexation auprès du développeur.

Fonctions définies par l’utilisateur (UDF) peuvent être enregistrées avec DocumentDB et référencées dans le cadre d’une requête SQL, étendant par conséquent la grammaire pour prendre en charge de la logique de l’application personnalisée. Ces UDF sont écrits en tant que programmes JavaScript et exécutés au sein de la base de données. 

Pour les développeurs .NET, DocumentDB propose également un fournisseur de requête LINQ dans le cadre du [Kit de développement .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transactions et l’exécution de JavaScript
DocumentDB vous permet d’écrire logique de l’application en tant que programmes nommées entièrement écrits en JavaScript. Ces programmes enregistrés pour une collection de sites et peuvent émettre des opérations de base de données sur les documents au sein d’une collection de sites donnée. JavaScript peut être enregistré pour être exécutées comme un déclencheur, la procédure stockée ou la fonction définie par l’utilisateur. Déclencheurs et des procédures stockées peuvent créer, lire, mettre à jour et supprimer des documents alors que les fonctions définies par l’utilisateur s’exécuter dans le cadre de la logique d’exécution de requête sans accès en écriture à la collection.

Exécution d’un code JavaScript au sein de DocumentDB est basée sur les concepts pris en charge par les systèmes de base de données relationnelle, avec JavaScript en guise de remplacement moderne pour Transact-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiance avec isolement de capture instantanée. Au cours de son exécution, si le code JavaScript lève une exception, la transaction entière est annulée.

## <a name="next-steps"></a>Étapes suivantes
Vous disposez déjà d’un compte Azure ? Puis vous pouvez commencer avec DocumentDB dans le [Portail Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) en [créant un compte de base de données DocumentDB](documentdb-create-account.md).

Vous n’avez un compte Azure ? Vous pouvez :

- Inscrivez-vous pour une [version d’évaluation gratuite Azure](https://azure.microsoft.com/free/), qui vous donne des 30 derniers jours et 200 $ pour essayer de tous les services Azure. 
- Si vous avez un abonnement MSDN, vous sont éligibles pour [150 $ dans crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) à utiliser sur n’importe quel service Azure. 

Puis, lorsque vous êtes prêt pour en savoir plus, visitez notre [rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) pour naviguer dans toutes les ressources de formation disponibles. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
