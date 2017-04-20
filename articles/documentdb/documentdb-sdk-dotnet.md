<properties 
    pageTitle="API .NET DocumentDB & SDK | Microsoft Azure" 
    description="Découvrez les API .NET et SDK, y compris les dates de publication, les dates de retraite et les modifications apportées entre chaque version du Kit de développement .NET DocumentDB." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB et SDK 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTE](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>Kit de développement et de l’API DocumentDB .NET

<table>
<tr><td>**Téléchargement du Kit**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentation de l’API**</td><td>[Documentation de référence API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Exemples**</td><td>[Exemples de code .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Prise en main**</td><td>[Prise en main le Kit de développement .NET DocumentDB](documentdb-get-started.md)</td></tr>
<tr><td>**Didacticiel d’application Web**</td><td>[Développement d’applications Web avec DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**.NET framework pris en charge récent**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notes de publication

> [AZURE.IMPORTANT] En commençant par version 1.9.2, vous pouvez recevoir System.NotSupportedException lors de l’interrogation collections partitionnées. Pour éviter cette erreur, vérifiez que votre processus hôte est 64 bits. Pour les projets exécutables, il est possible en désactivant l’option « Préférer 32 bits » dans la fenêtre de propriétés du projet, sous l’onglet Créer.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Ajouté une connectivité directe prise en charge pour les collections partitionnées.
  - Amélioration des performances pour le niveau de cohérence obsolescence délimitée.
  - Ajout de polygone et LineString DataTypes tout en spécifiant l’indexation de la stratégie de requêtes spatiales geo clôture collection de sites.
  - LINQ en charge les StringEnumConverter, IsoDateTimeConverter et UnixDateTimeConverter lors de la conversion prédicats.
  - Différents correctifs SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Correction d’un problème qui a provoqué la NotFoundException suivante : la session de lecture n’est pas disponible pour le jeton de session d’entrée. Cette exception s’est produite dans certains cas, lors de la zone de lecture d’un compte distribué geo interrogation.
  - La propriété ResponseStream dans la classe ResourceResponse, qui permet d’accéder directement à partir d’une réponse dans le flux sous-jacent exposée.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modifier les classes ResourceResponse, FeedResponse, StoredProcedureResponse et MediaResponse pour implémenter l’interface public correspondant afin qu’ils peuvent être fictive pour test par l’effort de déploiement (TDD).
  - Correction d’un problème qui a provoqué un en-tête clés partition incorrect lors de l’utilisation d’un objet JsonSerializerSettings personnalisé pour sérialisation des données.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Correction d’un problème qui a provoqué longue de requêtes avec l’erreur : jeton d’autorisation n’est pas valide à l’heure actuelle.
  - Correction d’un problème qui supprimé la SqlParameterCollection à partir d’origine croisée partition haut/order by requêtes.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Prise en charge pour les requêtes parallèles pour des collections de partitionnées.
  - Prise en charge supplémentaire pour croisée partition des requêtes ORDER BY et haut pour des collections de partitionnées.
  - Fixe les références manquantes DocumentDB.Spatial.Sql.dll et Microsoft.Azure.Documents.ServiceInterop.dll requis lors du référencement d’un projet DocumentDB avec une référence au package DocumentDB Nuget.
  - Fixe la possibilité d’utiliser des paramètres de types différents lors de l’utilisation de fonctions définies par l’utilisateur dans LINQ. 
  - Corrige un bogue pour les comptes globalement répliquées où Upsert appels étaient redirigé vers cette à lire d’autres emplacements au lieu d’écriture emplacements.
  - Méthodes ajoutées à l’interface IDocumentClient qui étaient manquantes : 
      - Méthode de UpsertAttachmentAsync qui prend mediaStream et options en tant que paramètres
      - Méthode de CreateAttachmentAsync qui permet d’accéder en tant que paramètre options
      - Méthode CreateOfferQuery prenant querySpec en tant que paramètre.
  - Classes publics non scellés qui sont présentés dans l’interface IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Ajouter la prise en charge de base de données de la région à plusieurs comptes.
  - Prise en charge pour recommencer sur demandes limités.  Utilisateur peut personnaliser le nombre de tentatives et le délai d’attente max en configurant la propriété ConnectionPolicy.RetryOptions.
  - Ajouter une nouvelle interface IDocumentClient qui définit les signatures de toutes les propriétés DocumenClient et méthodes.  Dans le cadre de cette modification, également modifier les méthodes d’extension qui créent IQueryable et IOrderedQueryable aux méthodes sur la classe DocumentClient elle-même.
  - Ajout de l’option configuration pour définir la ServicePoint.ConnectionLimit pour un point de terminaison DocumentDB donné Uri.  Utilisez ConnectionPolicy.MaxConnectionLimit pour modifier la valeur par défaut, qui est définie à 50.
  - IPartitionResolver déconseillée et sa mise en œuvre.  Prise en charge de IPartitionResolver est désormais obsolète. Il est recommandé d’utiliser partition Collections de débit et de stockage plus élevé.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Ajouté qu'une surcharge à Uri en fonction de méthode ExecuteStoredProcedureAsync prenant RequestOptions en tant que paramètre.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Temps ajouté à la prise en charge de live (TTL) pour les documents.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Fixe un bogue conditionnement Nuget du Kit de développement .NET pour placer le package dans le cadre d’une solution de Service de nuage Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Mise en œuvre [collections partitionnées](documentdb-partition-data.md) et [niveaux de performances définis par l’utilisateur](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[version 1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fixe]** Interroger lève de point de terminaison DocumentDB : « System.Net.Http.HttpRequestException : erreur lors de la copie du contenu à un flux de données.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ développé prennent en charge, y compris les nouveaux opérateurs pour les expressions de paginations, conditionnelle et comparaison de plage.
    - Opérateur pour activer le comportement SELECT TOP dans LINQ Take
    - Opérateur CompareTo pour permettre les comparaisons de chaîne plage
    - Conditionnel ( ?) et fusionner les opérateurs ( ?)
  - **[Fixe]** ArgumentOutOfRangeException lors de la combinaison projection modèle avec l’emplacement de requête linq.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fixe]** Si vous sélectionnez n’est pas la dernière expression du fournisseur LINQ supposé sans projection et produit Sélectionnez * incorrectement.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Mise en œuvre Upsert, méthodes UpsertXXXAsync ajouté
 - Améliorations des performances pour toutes les requêtes
 - Prise en charge du fournisseur LINQ de conditionnel, de fusion et les méthodes CompareTo des chaînes
 - **[Fixe]** Fournisseur LINQ--> mettre en œuvre contient une méthode sur liste pour générer le code SQL même en tant que sur IEnumerable et matrice
 - **[Fixe]** BackoffRetryUtility utilise à nouveau le même HttpRequestMessage au lieu de créer un nouvel identifiant sur Réessayer
 - **[Obsolète]** UriFactory.CreateCollection--> doit maintenant utiliser UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fixe]** Problèmes de localisation lors de l’utilisation des informations de culture non US comme en naturel-en naturel etc.. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID en fonction de routage
    - Nouvelle application d’assistance UriFactory pour vous aider à ID construction en fonction des liens vers les ressources
    - Nouvelles surcharges sur DocumentClient prendre dans URI
  - Ajout de IsValid() et IsValidDetailed() dans LINQ pour géospatiales
  - Prise en charge du fournisseur LINQ améliorée
    - **Mathématiques** - Abs, Acos, Asin, Atan, plafond, Cos, Exp, plancher, Log, Log10, Pow, arrondi, se, Sin, Sqrt, Tan, tronquer
    - **Chaîne** - Concat, contient, EndsWith, IndexOf, nombre, ToLower, TrimStart, remplacer, inverser, TrimEnd, StartsWith, sous-chaîne, ToUpper
    - **Tableau** : Concat, contient, nombre
    - Opérateur **dans**

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[version 1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Prise en charge pour la modification des règles d’indexation
    - Nouvelle méthode ReplaceDocumentCollectionAsync dans DocumentClient
    - Nouvelle propriété IndexTransformationProgress dans ResourceResponse<T> pour le suivi de pourcentage de l’avancement des modifications de stratégie d’index
    - DocumentCollection.IndexingPolicy devient modifiable
  - Prise en charge pour l’indexation et requête
    - Nouvel espace de noms Microsoft.Azure.Documents.Spatial pour sérialisation/désérialisation de types spatiales comme Point et polygone
    - Nouvelle classe SpatialIndex pour l’indexation GeoJSON données stockées dans DocumentDB
  - **[Fixe]** : requête SQL incorrecte généré à partir de l' expression linq [n ° 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dépendance par rapport à Newtonsoft.Json v5.0.7 
- Modifications pour prendre en charge Order By
  - Prise en charge du fournisseur LINQ pour OrderBy() ou OrderByDescending()
  - IndexingPolicy pour prendre en charge Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Prise en charge de division de données en utilisant les nouvelles classes HashPartitionResolver et RangePartitionResolver et la IPartitionResolver
- DataContract sérialisation
- Prise en charge GUID dans fournisseur LINQ
- Prise en charge UDF dans LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- KIT DE DÉVELOPPEMENT DE DISPONIBILITÉ GÉNÉRALE

> [AZURE.NOTE]
Il a été un changement de nom du package NuGet prévisualisation de GA. Nous déplacé de **Microsoft.Azure.Documents.Client** vers **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Aperçu SDK [obsolète]

## <a name="release--retirement-dates"></a>Version & Dates de retraite
Microsoft fournira notification au moins **12 mois** avant la suppression d’un kit de développement afin de faciliter la transition vers une version plus récente/pris en charge.

Nouvelles fonctionnalités et les fonctionnalités et les optimisations ne sont ajoutées dans le Kit de développement en cours, en tant que tel qu’il est recommandé que vous toujours mise à niveau vers la dernière version SDK dès que possible. 

Toute demande à DocumentDB à l’aide d’un SDK déclassé est rejetée par le service.

> [AZURE.WARNING]
Toutes les versions du Kit de développement DocumentDB Azure pour .NET avant version **1.0.0** seront supprimées sur **29 février 2016**. 
 
<br/>
 
| Version | Date de publication | Date de déclassement 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 septembre 2016 |---
| [1.9.5](#1.9.5) | 01 septembre 2016 |---
| [1.9.4](#1.9.4) | 24 août 2016 |---
| [1.9.3](#1.9.3) | 15 août 2016 |---
| [1.9.2](#1.9.2) | 23 juillet 2016 |---
| 1.9.1 | Déconseillée |---
| 1.9.0 | Déconseillée |---
| [1.8.0](#1.8.0) | 14 juin 2016 |---
| [1.7.1](#1.7.1) | 06 mai 2016 |---
| [1.7.0](#1.7.0) | 26 avril 2016 |---
| [1.6.3](#1.6.3) | 08 avril 2016 |---
| [1.6.2](#1.6.2) | 29 mars 2016 |---
| [version 1.5.3](#1.5.3) | 19 février 2016 |---
| [1.5.2](#1.5.2) | 14 décembre 2015 |---
| [1.5.1](#1.5.1) | 23 novembre 2015 |---
| [1.5.0](#1.5.0) | 05 octobre 2015 |---
| [1.4.1](#1.4.1) | 25 août 2015 |---
| [1.4.0](#1.4.0) | 13 août 2015 |---
| [version 1.3.0](#1.3.0) | 05 août 2015 |---
| [1.2.0](#1.2.0) | 06 juillet 2015 |---
| [1.1.0](#1.1.0) | 30 avril 2015 |---
| [1.0.0](#1.0.0) | 08 avril 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 mars 2015 | 29 février 2016
| [0.9.2-prelease](#0.9.x-preview) | Janvier 2015 | 29 février 2016
| [.9.1-prelease](#0.9.x-preview) | 13 octobre 2014 | 29 février 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 août 2014 | 29 février 2016

## <a name="faq"></a>FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur DocumentDB, consultez la page de service [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
