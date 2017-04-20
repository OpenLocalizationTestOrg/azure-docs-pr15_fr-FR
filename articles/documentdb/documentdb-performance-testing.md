<properties 
    pageTitle="DocumentDB échelle et tester les performances | Microsoft Azure" 
    description="Découvrez comment effectuer des tests de performances avec Azure DocumentDB et échelle"
    keywords="tests de performances"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>Performances et échelle réalisés avec DocumentDB Azure
Tests de performance et échelle est une étape clé de développement d’applications. Pour de nombreuses applications le niveau de base de données a un impact sur les performances globales et extensibilité élevées et n’est donc un composant essentiel de tester les performances. [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est conçu pour échelle élastique et des performances prévisibles et par conséquent une solution adaptée aux applications que vous avez besoin d’un niveau de base de données de haute performance. 

Cet article est une référence pour les développeurs mise en œuvre des suites de test de performances de leurs charges de travail DocumentDB ou l’évaluation DocumentDB scénarios d’applications High performance. Il se concentre principalement sur les performances par rapport aux tests de la base de données, mais également sur les meilleures pratiques pour les applications de production.

Après la lecture de cet article, vous ne pourrez pas répondre aux questions suivantes :   

- Où puis-je trouver une exemple .NET d’application cliente pour tester les performances de DocumentDB Azure ? 
- Comment obtenir les niveaux de haut débit avec Azure DocumentDB à partir de mon application cliente ?

Pour commencer avec un code, téléchargez le projet à partir de [l’Échantillon de test de Performance DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [AZURE.NOTE] L’objectif de cette application est de meilleures pratiques permettant d’extraire des meilleures performances déconnecter DocumentDB avec un petit nombre d’ordinateurs clients. Cela n’a pas été pour montrer la capacité maximale du service, qui peut s’ajuster limitlessly.

Si vous cherchez des options de configuration côté client pour améliorer les performances de DocumentDB, voir [optimiser les performances DocumentDB](documentdb-performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Exécuter le test d’application de performance
Prise en main le moyen consiste à compiler et exécuter l’échantillon .NET ci-dessous, comme décrit dans les étapes ci-dessous. Vous pouvez également examiner le code source et mise en œuvre des configurations similaires à vos propres applications clientes.

**Étape 1 :** Télécharger le projet à partir de [l’Échantillon de test de Performance DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)ou branche du référentiel Github.

**Étape 2 :** Modifier les paramètres pour EndpointUrl, AuthorizationKey, CollectionThroughput et DocumentTemplate (facultatif) dans App.config.

> [AZURE.NOTE] Avant la configuration des collections de sites avec un haut débit, reportez-vous à la [Page tarification](https://azure.microsoft.com/pricing/details/documentdb/) pour estimer les coûts par collection de sites. Stockage de factures DocumentDB et débit indépendamment sur une base horaire, afin que vous pouvez enregistrer les coûts en supprimant ou en réduisant le débit de vos collections DocumentDB après le test.

**Étape 3 :** Compiler et exécutez l’application console à partir de la ligne de commande. Vous devriez voir le résultat comme suit :

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**Étape 4 (si nécessaire) :** Le débit signalé (demandeur/s) à partir de l’outil doit être identique ou supérieur à celui de la collection de sites mis en service. Dans le cas contraire, augmenter la DegreeOfParallelism par petits incréments peut-être vous aider à atteindre la limite. Si le débit à partir de votre application client des plateaux, lancer plusieurs instances de l’application sur les ordinateurs identiques ou différents vous permettra atteint la limite de mise en service à travers les différentes instances. Si vous avez besoin d’aide de cette étape, veuillez, rédiger un message électronique à askdocdb@microsoft.com ou remplir une demande d’assistance.

Une fois que l’application en cours d’exécution, vous pouvez essayer les [stratégies d’indexation](documentdb-indexing-policies.md) et différents [niveaux de cohérence](documentdb-consistency-levels.md) pour mieux comprendre leur impact sur débit et la latence. Vous pouvez également examiner le code source et mise en œuvre des configurations similaires à votre propre test suites ou les applications de production.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons étudié comment vous pouvez effectuer les performances et les proportions réalisés avec DocumentDB à l’aide d’une application console .NET. Consultez les liens ci-dessous pour plus d’informations sur l’utilisation de DocumentDB.

* [Exemple de test de performances DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Options de configuration client pour améliorer les performances de DocumentDB](documentdb-performance-tips.md)
* [Partition dans DocumentDB côté serveur](documentdb-partition-data.md)
* [Collections de DocumentDB et les niveaux de performance](documentdb-performance-levels.md)
* [Documentation DocumentDB .NET SDK sur MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Exemples de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
* [Blog DocumentDB sur optimiser les performances](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
