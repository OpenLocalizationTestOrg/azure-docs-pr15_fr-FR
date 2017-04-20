<properties 
    pageTitle="Demander l’intensité d’utilisation dans DocumentDB | Microsoft Azure" 
    description="Découvrez comment comprendre, spécifiez et estimer les besoins en unité de demande dans DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Demander l’intensité d’utilisation dans DocumentDB
Désormais disponible : DocumentDB [Calculatrice des unités demande](https://www.documentdb.com/capacityplanner). Pour plus d’informations [estimer votre débit a besoin](documentdb-request-units.md#estimating-throughput-needs).

![Calculatrice de débit][5]

##<a name="introduction"></a>Introduction
Cet article fournit une vue d’ensemble des unités de demande dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Lisez cet article, vous serez en mesure de répondre aux questions suivantes :  

-   Que sont les unités d’une requête et requête frais ?
-   Comment spécifier demande unité capacité pour une collection de sites ?
-   Comment estimer qu'unité de demande de mon application nécessaire ?
-   Que se passe-t-il si j’ai dépassement de capacité d’unité de demande pour une collection de sites ?


##<a name="request-units-and-request-charges"></a>Demander l’intensité d’utilisation et demander des frais
DocumentDB offre des performances rapides et prévisibles en *réserver des* ressources pour répondre aux que besoins de débit de votre application.  Comme application charger et schémas d’accès changent au fil du temps, DocumentDB permet de vous permettent de facilement augmenter ou réduire la quantité de débit réservé disponible pour votre application.

Avec DocumentDB, débit réservé est spécifié en termes d’unités de demande de traitement par seconde.  Vous pouvez considérer d’unités demande sous forme de devise de débit, dans laquelle vous *réserver* une quantité de garantie unités demande disponibles pour votre application sur par seconde.  Chaque opération dans DocumentDB - écriture d’un document, d’une requête, mise à jour un document - consomme processeur, la mémoire et sorties par.  Autrement dit, chaque opération entraîne *demande frais*, ce qui est exprimé en *unités de la demande*.  Comprendre les facteurs qui ont une incidence sur demande unité frais, ainsi que des exigences de débit de votre application, vous permet d’exécuter votre application en tant que coût efficacement que possible. 

##<a name="specifying-request-unit-capacity"></a>Spécification de demande unité capacité
Lorsque vous créez une collection de sites DocumentDB, vous spécifiez le nombre d’unités de demande par seconde (RUs) souhaité réservés pour la collection de sites.  Une fois que la collection de sites est créé, la répartition complète du RUs spécifié est réservée pour une utilisation de la collection de sites.  Chaque collection de sites est assuré d’avoir dédiés et isolé caractéristiques de débit.  

Il est important de noter que DocumentDB fonctionne sur un modèle de réservation ; en d’autres termes, vous êtes facturé pour la quantité de débit *réservés* pour la collection de sites, quelle que soit la quantité de que le débit est activement *utilisée*.  N’oubliez pas, cependant, comme le chargement de votre application, les données et modification des modèles d’utilisation, vous pouvez facilement évoluer haut et bas de la quantité de réservé RUs via SDK DocumentDB ou à l’aide du [Portail Azure](https://portal.azure.com).  Pour plus d’informations à débit échelle vers le haut ou vers le bas, voir [niveaux de performances DocumentDB](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Considérations relatives à la demande unité
Lors de l’estimation du nombre d’unités de demande pour réserver pour votre collection de sites DocumentDB, il est important de tenir compte de variables suivantes :

- **Taille du document**. À mesure que les tailles de document augmentent les unités consommées pour lire ou écrire que les données augmente également.
- **Nombre de propriétés de document**. En supposant que par défaut de l’indexation de toutes les propriétés, les unités consommées pour écrire un document augmente en tant que l’augmentation du nombre de propriété.
- **La cohérence des données**. Lorsque vous utilisez des niveaux de cohérence des données de fortes ou obsolescence délimitée, unités supplémentaires seront consommées pour lire des documents.
- **Les propriétés indexées**. Une stratégie d’index sur chaque collection détermine quelles sont les propriétés sont indexées par défaut. Vous pouvez réduire votre consommation d’unités demande en limitant le nombre de propriétés indexées ou en activant indexation lente.
- **L’indexation du document**. Par défaut que chaque document est automatiquement indexé, vous allez consommer moins d’unités demande si vous choisissez de ne pas indexer certains de vos documents.
- **Modèles de requête**. La complexité d’une requête a une incidence sur le nombre d’unités demander sont utilisé pour une opération. Le nombre de prédicats, nature de prédicats, projections, nombre d’UDF et la taille de l’ensemble de données sources tous les influencer le coût des opérations de requête.
- **L’utilisation de script**.  À l’instar de requêtes, déclencheurs et des procédures stockées consomment unités demande en fonction de la complexité des opérations en cours d’exécution. Lorsque vous développez votre application, vérifiez que l’en-tête de frais de requête pour mieux comprendre comment chaque opération consomme demande unité capacité.

##<a name="estimating-throughput-needs"></a>Estimation besoins en débit
Une unité de demande est une mesure normalisée de coût de traitement de requête. Une unité seule demande représente la capacité de traitement requise pour lire (via liaison automatique ou id) un seul document JSON 1 Ko composé de 10 valeurs de propriété unique (à l’exception des propriétés système). Une demande pour créer (insertion), remplacer ou supprimer le même document utilisera plus de traitement à partir du service et par conséquent autres unités de demande.   

> [AZURE.NOTE] Le planning de référence d’unité 1 requête pour un document 1 Ko correspond à une commande GET simple en lien automatique ou l’id du document.

###<a name="use-the-request-unit-calculator"></a>Utiliser la calculatrice des unités demande
Pour vous aider clients fins régler leurs estimations de débit, il existe une web en fonction de [Calculatrice des unités demande](https://www.documentdb.com/capacityplanner) pour aider à évaluer les exigences unité demande pour les opérations courantes, notamment :

- Document crée (écrit)
- Lectures de document
- Supprime du document
- Mises à jour du document

L’outil prend également en charge pour l’estimation des besoins de stockage de données basées sur les exemples de documents que vous avez défini.

À l’aide de l’outil est simple :

1. Télécharger un ou plusieurs documents JSON représentatives.

    ![Télécharger des documents sur la calculatrice des unités demande][2]

2. Pour évaluer les besoins de stockage, entrez le nombre total de documents que qui sera stocké.

3. Entrez le nombre de document créer, lire, mettre à jour et supprimer les opérations que vous avez besoin (sur une base par seconde). Pour évaluer les frais unité demande d’opérations de mise à jour de document, télécharger une copie de l’exemple de document à partir de l’étape 1 ci-dessus qui inclut des mises à jour les champs par défaut.  Par exemple, si mises à jour du document modifient en général deux propriétés nommées lastLogin et userVisits, puis simplement copier l’exemple de document, mettre à jour les valeurs de ces deux propriétés et téléchargez le document copié.

    ![Entrer des exigences de débit dans la calculatrice des unités demande][3]

4. Cliquez sur Calculer et examiner les résultats.

    ![Demander des résultats de la calculatrice unité][4]

>[AZURE.NOTE]Si vous avez des types de documents qui varient considérablement en termes de taille et le nombre de propriétés indexées, puis téléchargez un échantillon de chaque *type* de document par défaut dans l’outil, puis calcule les résultats.

###<a name="use-the-documentdb-request-charge-response-header"></a>Utiliser l’en-tête de réponse DocumentDB demande frais
Chaque réponse à partir du service DocumentDB inclut un en-tête personnalisé (x-ms-demande-frais) qui contient les unités de demande consommées pour la demande. Cet en-tête est également accessible via le SDK DocumentDB. Dans le Kit de développement .NET, RequestCharge est une propriété de l’objet ResourceResponse.  Pour les requêtes, l’Explorateur de requête DocumentDB dans le portail Azure fournit des informations relatives aux frais de demande pour les requêtes exécutées.

![Examen des frais demandeur dans l’Explorateur de requête][1]

En gardant cela à l’esprit, une méthode d’estimation de la quantité de débit réservé requis par votre application consiste à enregistrer les frais unitaires demande associés aux opérations typiques en cours d’exécution par rapport à un document représentatif utilisé par votre application et de puis estimer le nombre d’opérations que vous envisagez d’effectuer chaque seconde.  Veillez à mesurer et inclure des requêtes et DocumentDB l’utilisation de script également.

>[AZURE.NOTE]Si vous avez des types de documents qui varient considérablement en termes de taille et le nombre de propriétés indexées, puis enregistrez les frais unitaires de demande opération applicable associé à chaque *type* de document par défaut.

Par exemple :

1. Enregistrer les frais unitaires demande création (insertion) d’un document par défaut. 
2. Enregistrement les frais unitaires demande de lecture d’un document par défaut.
3. Enregistrement les frais unitaires demande de mise à jour d’un document par défaut.
3. Enregistrement les frais unitaires demande de requêtes de document standard, courantes.
4. Enregistrer les frais unitaires demande des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) exploité par l’application
5. Calculer les unités de demande requis données estimation du nombre d’opérations que vous envisagez de pour exécuter chaque seconde.

##<a name="a-request-unit-estimation-example"></a>Un exemple d’estimation unité demande
Considérez le document de base de connaissances ~ 1 suivant :

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]Documents sont réduites dans DocumentDB, afin que le système calculée la taille du document ci-dessus est légèrement inférieure à 1 Ko.


Le tableau suivant montre demande approximative frais unitaires pour les opérations courantes sur ce document (les frais unitaires demande approximative suppose que le niveau de cohérence compte est défini sur « Session » et que tous les documents sont automatiquement indexés) :

Opération|Frais unitaires demande 
---|---
Créer des documents|~ 15 DEMANDEUR 
Lire le document|~ 1 DEMANDEUR
Document de requête par id|~2.5 DEMANDEUR

En outre, ce tableau indique demande approximative frais unitaires pour les requêtes par défaut utilisées dans l’application :

Requête|Frais unitaires demande|# de Documents renvoyés
---|---|--- 
Sélectionnez alimentation par id|~2.5 DEMANDEUR|1 
Sélectionnez les aliments par fabricant|~ 7 DEMANDEUR|7
Sélectionner une groupe alimentation en ordre en poids|~ 70 DEMANDEUR|100
Sélectionner des 10 aliments supérieure dans un groupe d’alimentation|~ 10 DEMANDEUR|10

>[AZURE.NOTE]Frais demandeur varient en fonction du nombre de documents renvoyés.

Grâce à ces informations, nous pouvons estimer les conditions demandeur pour cette application étant donné le nombre d’opérations et requêtes que nous prévoyons par seconde :

Requête d’opération|Nombre estimé par seconde|RUs requis 
---|---|--- 
Créer des documents|10|150 
Lire le document|100|100 
Sélectionnez les aliments par fabricant|25|175 
Sélectionnez par groupe alimentation|10|700 
Sélectionner les 10 premiers|15|Total 150|155|1275

Dans ce cas, nous prévoyons une demande de débit moyen de 1,275 demandeur/s.  Arrondi jusqu'à la 100 le plus proche, nous serait mise en service de 1 300 demandeur/s pour une collection de sites de cette application.

##<a id="RequestRateTooLarge"></a>Limites de débit réservés excédant
Rappelez-vous que consommation d’unités demande est évaluée comme un taux par seconde. Pour les applications qui dépassent le taux d’unité généré demande d’une collection de sites, demandes à cette collection seront limitées jusqu'à ce que le taux est inférieur au niveau réservé. Lorsqu’un papillon se produit, le serveur manière préemptive mettre fin à la demande avec RequestRateTooLargeException (code d’état HTTP 429) et renvoyer l’en-tête x réessayer ms après ms indiquant le nombre d’heures, en millisecondes, que l’utilisateur doit attendre avant de réessayer la demande.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si vous utilisez les requêtes .NET Client SDK et LINQ, puis la plupart du temps, que vous n’avez pas traiter cette exception, comme la version actuelle du Kit de développement Client .NET captures implicitement cette réponse, respecte spécifiée par le serveur après réessayer en-tête et de tentatives de la demande. À moins que votre compte est effectué simultanément par plusieurs clients, le prochain essai réussit.

Si vous avez plusieurs clients cumulative d’exploitation au-dessus du taux de requête, le comportement par défaut des nouvelles tentatives ne suffisent pas, et le client génère une DocumentClientException avec le code d’état 429 à l’application. Dans ce cas, vous pouvez envisager de gestion des comportement des nouvelles tentatives et logique erreur de votre application des routines de gestion ou en augmentant le débit réservé pour la collection de sites.

##<a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur débit réservé les bases de données Azure DocumentDB, Explorez ces ressources :
 
- [DocumentDB tarifs](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestion de la capacité DocumentDB](documentdb-manage.md) 
- [Modélisation de données dans DocumentDB](documentdb-modeling-data.md)
- [Niveaux de performances DocumentDB](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, voir la [documentation](https://azure.microsoft.com/documentation/services/documentdb/)de DocumentDB Azure. 

Pour commencer avec échelle et tests de performances avec DocumentDB, consultez [performances et échelle réalisés avec Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
