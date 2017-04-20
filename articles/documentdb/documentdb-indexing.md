<properties 
    pageTitle="L’indexation automatique dans DocumentDB | Microsoft Azure" 
    description="Découvrez comment fonctionne indexation automatique dans Azure DocumentDB." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>
    
# <a name="automatic-indexing-in-azure-documentdb"></a>Automatique l’indexation en DocumentDB Azure

Cet article est extrait de papier [« indépendant du schéma de l’indexation avec Azure DocumentDB »](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , qui sera présenté à la [Conférence interne 41st sur très des bases de données](http://www.vldb.org/2015/) entre le 31 août - 4 septembre 2015, et est une introduction à la façon dont l’indexation fonctionne dans Azure DocumentDB. 

Après la lecture de cela, vous répondra les questions suivantes :

- Comment DocumentDB déduire le schéma à partir d’un document JSON ?
- Comment DocumentDB créer un index dans tous les documents hétérogènes ?
- Comment DocumentDB effectue l’indexation automatique à l’échelle ?

##<a id="HowDocumentDBIndexingWorks"></a>Fonctionne de l’indexation DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est conçu spécialement pour JSON pour un vrai exempt de schéma de base de données. Il ne pas attendre ou requièrent un schéma ou les définitions d’index secondaire d’indexer des données à l’échelle. Cela vous permet de définir rapidement et modifions modèles de données d’application à l’aide de DocumentDB. Lorsque vous ajoutez des documents à une collection de sites, DocumentDB index automatiquement toutes les propriétés de document pour qu’ils soient disponibles à la requête. L’indexation automatique vous permet de stocker des documents appartenant à des schémas complètement arbitraires sans vous préoccuper des schémas ou index secondaires.

Dans le but d’éliminer la différence d’impédance entre la base de données et les modèles de programmation d’application, DocumentDB exploite la simplicité de JSON et l’absence de spécification schéma. Il n’affecte pas les documents et permet de documents au sein d’une collection DocumentDB varier schéma, en plus des valeurs instance spécifique. Contrairement à d’autres bases de données de document, moteur de base de données de DocumentDB fonctionne directement au niveau de la grammaire JSON, restant indépendantes par rapport au concept d’un schéma de document et de flou la limite entre les valeurs de la structure et instance de documents. Cette option, tour, vous permettent de conserver pour index automatiquement des documents sans schéma ou à partir d’index secondaire.

L’indexation dans DocumentDB tire parti du fait que grammaire JSON permet d’être **représentée sous forme d’arborescences**de documents. Pour un document JSON être représentée sous forme d’arborescence, un nœud racine factice doit être créé qui comporte le reste des nœuds réels dans le document en dessous. Chaque étiquette, y compris les index de tableau dans un document JSON devienne un nœud de l’arborescence. L’illustration suivante montre un exemple de document JSON et son arborescence correspondante.

>[AZURE.NOTE] Étant donné que JSON est explicites c'est-à-dire que chaque document comprend schéma (métadonnées) et les données, par exemple, `{"locationId": 5, "city": "Moscow"}` révèle qu’il existe deux propriétés `locationId` et `city`, et qu’ils ont une valeur de propriété numériques et de chaîne. DocumentDB est en mesure de déduire le schéma de documents et les indexer lorsqu’ils sont insérés ou remplacés, sans que vous ayez jamais définir des schémas ou index secondaires.


**Documents JSON comme arborescences :**

![Documents en tant qu’arborescences](media/documentdb-indexing/DocumentsAsTrees.png)

Par exemple, dans l’exemple ci-dessus :

- La propriété JSON `{"headquarters": "Belgium"}` propriété dans l’exemple ci-dessus correspond au chemin d’accès/siège social/Belgique.
- Le tableau JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` correspond aux chemins d’accès `/exports/[]/city/Moscow` et `/exports/[]/city/Athens`.

Avec automatique indexation (1) chaque chemin d’accès dans une arborescence du document est indexé (sauf si le développeur a explicitement configuré la politique d’indexation pour exclure certains modèles chemin d’accès). (2) chaque mise à jour d’un document à une collection DocumentDB permet d’accéder à mettre à jour de la structure de l’index (c'est-à-dire, causes Ajout ou la suppression des nœuds). Parmi les exigences principales de l’indexation automatique de documents pour vous assurer que le coût pour l’index et un document avec sa structure imbriquée de la requête, par exemple 10 niveaux, est identique à celle d’un document JSON plat composé de paires clé-valeur qu’un seul niveau. Par conséquent, une représentation de chemin d’accès normalisée est la base sur laquelle les deux sous-systèmes de requête et d’indexation automatiques sont créés.

Une implication importante de traitement à la fois les valeurs de schéma et une instance de manière uniforme en ce qui concerne les chemins d’accès est qui logiquement, comme les documents individuels, un index des deux documents indiqué qui conserve un mappage entre les chemins d’accès et les ID de document contenant ce chemin d’accès peuvent également être représentées sous forme d’arborescence. DocumentDB utilise ce fait pour générer une arborescence d’index qui est créée à partir de l’union de tous les arbres représentant des documents individuels au sein de la collection de sites. L’arborescence d’index dans collections DocumentDB augmente au fil du temps nouveaux documents obtenir ajoutés ou mis à jour à la collection.


**Index DocumentDB sous forme d’arborescence :**

![Index sous forme d’arborescence](media/documentdb-indexing/IndexAsTree.png)

Bien que leur exempt de schéma, SQL et JavaScript de DocumentDB requête langues procurent relationnelles projections et filtres, navigation hiérarchique documents, opérations spatiales et l’appel d’UDF entièrement écrit en JavaScript. Pour l’exécution de DocumentDB requête est en mesure de prendre en charge ces requêtes dans la mesure où elle peut fonctionner directement sur cette arborescence index des données.

La stratégie d’indexation par défaut index toutes les propriétés de tous les documents automatiquement et fournit des requêtes cohérentes (c'est-à-dire l’index est mis à jour synchrone avec l’écriture de document). Comment DocumentDB prend-elle en charge les mises à jour cohérentes à l’arborescence d’index à l’échelle ? DocumentDB utilise écriture optimisée, verrouillage gratuit et journal structurés techniques de gestion des index. Cela signifie que DocumentDB peut prendre en charge un volume prolongée des écritures rapides lors de toujours traiter les requêtes cohérentes. 

DocumentDB l’indexation est conçu pour l’efficacité du stockage et de gérer plusieurs Location. Pour l’efficacité du coût, les frais de stockage sur le disque de l’index est faible et prévisibles. Mises à jour de l’index sont également exécutés dans le budget des ressources système allouées par DocumentDB collection.

##<a name="NextSteps"></a>Étapes suivantes
- Téléchargez [« indépendant du schéma de l’indexation avec Azure DocumentDB »](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), à présenter lors de la conférence 41st interne de très grandes bases de données, le 31 août - 4 septembre 2015.
- [Requête avec DocumentDB SQL](documentdb-sql-query.md)
- En savoir plus sur la personnalisation de l’index DocumentDB [ici](documentdb-indexing-policies.md)
 
