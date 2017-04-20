<properties 
    pageTitle="Exemple de NoSQL .NET pour DocumentDB | Microsoft Azure" 
    description="Trouver des exemples de c# .NET NoSQL de github pour les tâches courantes dans DocumentDB, y compris les opérations pour les documents JSON en question." 
    keywords="Exemple de NoSQL"
    services="documentdb" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=".net"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/21/2016" 
    ms.author="rnagpal"/>


# <a name="documentdb-net-examples"></a>Exemples de DocumentDB .NET

> [AZURE.SELECTOR]
- [Exemples de .NET](documentdb-dotnet-samples.md)
- [Exemples de Node.js](documentdb-nodejs-samples.md)
- [Exemples de Python](documentdb-python-samples.md)
- [Galerie d’exemples de Code Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Exemples de solutions qui effectuent des opérations et autres opérations courantes sur les ressources DocumentDB Azure sont inclus dans le référentiel GitHub [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) . Cet article fournit :

- Liens vers les tâches dans chacun des fichiers de projet exemple c#. 
- Liens à l’API connexe font référence à contenu.

**Conditions préalables**

1. Vous avez besoin d’un compte Azure à utiliser ces exemples NoSQL :
    - Vous pouvez [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/): pouvez-vous crédits vous pouvez utiliser pour essayer de services Azure payants et même après leur utilisation jusqu'à vous pouvez conserver le compte et libérer de l’utilisation des services Azure, tels que des sites Web. Votre carte de crédit est jamais facturée, sauf si vous explicitement de modifiez vos paramètres et lui demander de vous être facturés.
   - Vous pouvez [Activer les avantages d’abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): Visual Studio votre abonnement fournit les crédits chaque mois que vous pouvez utiliser pour les services Azure payants.
2. Vous devez également le [package Microsoft.Azure.DocumentDB NuGet](http://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). 

> [AZURE.NOTE]
Chaque échantillon est autonome, il se configure et efface après lui-même. Par conséquent, les exemples émettre des appels multiples à CreateDocumentCollectionAsync(). Chaque fois que cela, votre abonnement est facturé pour une heure de l’utilisation par le niveau de performance de la collection de sites en cours de création. 

## <a name="database-examples"></a>Exemples de base de données

La méthode [RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L72-L121) de l’échantillon du projet DatabaseManagement montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une base de données](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L90) | [DocumentClient.CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx)
[Requête un compte pour une base de données](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L81) | [DocumentQueryable.CreateDatabaseQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdatabasequery.aspx)
[Lire une base de données par code](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L102) | [DocumentClient.ReadDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdatabaseasync.aspx)
[Liste des bases de données pour un compte](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L108-L113) | [DocumentClient.ReadDatabaseFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdatabasefeedasync.aspx)
[Supprimer une base de données](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/DatabaseManagement/Program.cs#L118) | [DocumentClient.DeleteDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedatabaseasync.aspx)

## <a name="collection-examples"></a>Exemples de collection de sites 

La méthode [RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/530c8d9cf7c99df7300246da05206c57ce654233/samples/code-samples/CollectionManagement/Program.cs#L96-L185) de l’exemple de projet CollectionManagement montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une collection de sites](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L101) | [DocumentClient.CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)
[Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L130) | [DocumentQueryable.CreateOfferQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)
[Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L142-L143) | [DocumentClient.ReplaceOfferAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
[Obtenir une collection de sites par Id](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L153) | [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx)
[Lire une liste de toutes les collections dans une base de données](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L162) | [DocumentClient.ReadDocumentCollectionFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync.aspx)
[Supprimer une collection de sites](https://github.com/Azure/azure-documentdb-dotnet/blob/89670bc8aefd9bdd932db7f9b6d2fcb9b6acf35e/samples/code-samples/CollectionManagement/Program.cs#L175) | [DocumentClient.DeleteDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync.aspx)

## <a name="document-examples"></a>Exemples de documents

La méthode [RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L97-L102) de l’exemple de projet DocumentManagement montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer un document](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L198) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)
[Lire un document par Id](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L211) | [DocumentClient.ReadDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentasync.aspx)
[Lire tous les documents dans une collection de sites](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L227) | [DocumentClient.ReadDocumentFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentfeedasync.aspx)
[Interrogation des documents](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L248-L251) | [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Remplacer un document](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L263) | [DocumentClient.ReplaceDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentasync.aspx)
[Upsert un document](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L300) | [DocumentClient.UpsertDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.upsertdocumentasync.aspx)
[Suppression d’un document](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L322) | [DocumentClient.DeleteDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.deletedocumentasync.aspx)
[Utiliser les objets dynamiques .NET](https://github.com/Azure/azure-documentdb-dotnet/blob/f374cc601f4cf08d11c88f0c3fa7dcefaf7ecfe8/samples/code-samples/DocumentManagement/Program.cs#L331-L380) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)<br>[DocumentClient.ReadDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentasync.aspx)<br>[DocumentClient.ReplaceDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentasync.aspx)
[Remplacer le document avec une coche ETag conditionnelle](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L398-L440) | [DocumentClient.AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx)<br>[Documents.Client.AccessConditionType](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accessconditiontype.aspx)
[Lire le document uniquement si le document a changé](https://github.com/Azure/azure-documentdb-dotnet/blob/f2b11dec45a195ddeed333560ebba63055f5ed09/samples/code-samples/DocumentManagement/Program.cs#L442-L470) | [DocumentClient.AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx)<br>[Documents.Client.AccessConditionType](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accessconditiontype.aspx)

## <a name="indexing-examples"></a>Exemples d’indexation

La méthode [RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/ea8c977b9c2f37ddc2894911ec239907ab60e40a/samples/code-samples/IndexManagement/Program.cs#L89-L117) de l’exemple de projet IndexManagement montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Exclure un document de l’index](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L125-L163) | [IndexingDirective.Exclude](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingdirective.aspx)
[Utilisation de l’indexation manuel (au lieu d’automatique)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L171-L209) | [IndexingPolicy.Automatic](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.automatic.aspx)
[Utilisation de l’indexation différée (au lieu de cohérence)](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L221-L238) | [IndexingMode.Lazy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.indexingmode.aspx#P:Microsoft.Azure.Documents.IndexingPolicy.IndexingMode)
[Exclure les chemins d’accès du document spécifié de l’index](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L248-L297) | [IndexingPolicy.ExcludedPaths](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.excludedpaths.aspx) 
[Forcer une opération d’analyse plage sur un chemin d’accès indexés hachage](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) | [FeedOptions.EnableScanInQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx)
[Utiliser des index plage sur chaînes](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L342-L405)| [IndexingPolicy.IncludedPaths](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.includedpaths.aspx)<br>[RangeIndex](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.rangeindex.aspx)
[Effectuer une transformation d’index](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L407-L464)| [ReplaceDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync.aspx)

Pour plus d’informations sur l’indexation, voir [règles d’indexation DocumentDB](documentdb-indexing-policies.md).
 
## <a name="partitioning-examples"></a>Partition exemples

L’exemple de fichier partition, [azure-documentdb-net/samples/code-samples/Partitioning/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Partitioning/Program.cs), montre comment effectuer les tâches suivantes. Dans certains cas, les fichiers d’assistance supplémentaires sont utilisées pour effectuer cette tâche.

Tâche | Référence de l’API
--- | ---
[Utiliser un HashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L144-L160) | [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)
[Utiliser un RangePartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L162-L186) | [Plage](https://msdn.microsoft.com/library/azure/mt126048.aspx) avec<br>[RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)
[Mettre en œuvre des programmes de résolution de partition personnalisée](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L285) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Mettre en œuvre une table de choix simple](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L115-L119) avec<br>[LookupPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) | [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)
[Mettre en œuvre une résolution partition qui crée ou clone collections](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L121-L126) avec<br> [ManagedHashPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Mettre en œuvre un jeu supplémentaire](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L128-L134) avec<br>[SpilloverPartitionResolver.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Enregistrement et le chargement des configurations vider](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L136-L137) avec<br>[RunSerializeDeserializeSample](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L295-L311) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx)
[Ajouter, supprimer et ré-équilibrer les données entre des partitions](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L139-L141) avec <br>[RepartitionDataSample](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Partitioning/Program.cs#L313-L345) et<br>[DocumentClientHashPartitioningManager.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) | [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)
[Mettre en œuvre une résolution partition pour le routage pendant un repartitionnement](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) | [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) 

Pour plus d’informations sur partition et ont, voir [Partition et les proportions de données dans DocumentDB](documentdb-partition-data.md).

## <a name="geospatial-examples"></a>Exemples de géospatiales  

L’exemple de fichier géospatiales, [azure-documentdb-net/samples/code-samples/Geospatial/Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs), montre comment effectuer les tâches suivantes.  
 
Tâche | Référence de l’API  
---- | ---  
[Activer géospatiales l’indexation sur une nouvelle collection](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L45-L63) | [IndexingPolicy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpolicy.aspx)<br>[IndexKind.Spatial](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexkind.aspx)<br>[DataType.Point](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.datatype.aspx)  
[Insérer des documents avec des points de GeoJSON](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L116-L126) | [DocumentClient.CreateDocumentAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)<br>[DataType.Point](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.datatype.aspx)   
[Trouver des points dans une distance spécifique](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L152-L194) | [ST_DISTANCE](documentdb-sql-query.md#built-in-functions) ou<br>[GeometryOperationExtensions.Distance] (https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.distance.aspx#M:Microsoft.Azure.Documents.Spatial.GeometryOperationExtensions.Distance(Microsoft.Azure.Documents.Spatial.Geometry,Microsoft.Azure.Documents.Spatial.Geometry)  
[Rechercher des points d’un polygone](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L196-L221) | [ST_WITHIN](documentdb-sql-query.md#built-in-functions) ou<br>[GeometryOperationExtensions.Within] (https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.within.aspx#M:Microsoft.Azure.Documents.Spatial.GeometryOperationExtensions.Within(Microsoft.Azure.Documents.Spatial.Geometry,Microsoft.Azure.Documents.Spatial.Geometry) et<br>[Polygone](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.polygon.aspx)  
[Activer géospatiales l’indexation sur une collection de sites existante](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L312-L336) | [DocumentClient.ReplaceDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync.aspx)<br>[DocumentCollection.IndexingPolicy](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.indexingpolicy.aspx#P:Microsoft.Azure.Documents.DocumentCollection.IndexingPolicy)  
[Valider des données point et polygone](https://github.com/Azure/azure-documentdb-dotnet/blob/7b09c085817e850d683bc59bd864c2f6b552d275/samples/code-samples/Geospatial/Program.cs#L223-L265) | [ST_ISVALID](documentdb-sql-query.md#built-in-functions)<br>[ST_ISVALIDDETAILED](documentdb-sql-query.md#built-in-functions)<br>[GeometryOperationExtensions.IsValid](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid.aspx)<br>[GeometryOperationExtensions.IsValidDetailed](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed.aspx)  
 
Pour plus d’informations sur l’utilisation des données géospatiales, voir [utilisation des données géospatiales dans Azure DocumentDB](documentdb-geospatial.md).  
 
## <a name="query-examples"></a>Exemples de requête

Le fichier de document de requête, [azure-documentdb-net/samples/code-samples/Queries/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/Queries/Program.cs), montre comment effectuer les tâches suivantes à l’aide de la grammaire de requête SQL, le fournisseur LINQ avec requête et Lambda.

Tâche | Référence de l’API
--- | ---
[Requête pour tous les documents](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L122-L138) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête pour l’utilisation de l’égalité ==](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L251-L268) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête pour l’utilisation d’inégalité ! = et pas](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L270-L276) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Interroger à l’aide des opérateurs de plage comme >, <>, =, < =](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L305-L325) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Utilisation d’opérateurs de plage contre les chaînes de requête](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L337-L346) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête avec Order by](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L370-L392) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Travailler avec des sous-documents](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L394-L419) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête avec internes au document joint](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L421-L435) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête avec chaîne, les opérateurs mathématiques et matrice](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L527-L552) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête avec paramétrée SQL à l’aide de SqlQuerySpec](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L140-L174) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)<br>[SqlQuerySpec](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.sqlqueryspec.aspx)
[Requête avec la pagination explicite](https://github.com/Azure/azure-documentdb-dotnet/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/Queries/Program.cs#L554-L576) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Collections de requête partitionnée en parallèle](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L664-L734) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)
[Requête avec Order by pour des collections de partitionnées](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L737-L810) | [DocumentQueryable.CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx)

Pour plus d’informations sur l’écriture de requêtes, voir [requête SQL dans DocumentDB](documentdb-sql-query.md).


## <a name="server-side-programming-examples"></a>Exemples de programmation côté serveur

Le fichier de programmation côté serveur, [azure-documentdb-net/samples/code-samples/ServerSideScripts/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs), montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer une procédure stockée](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L112) | [DocumentClient.CreateStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createstoredprocedureasync.aspx)
[Exécuter une procédure stockée](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L127) | [DocumentClient.ExecuteStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.executestoredprocedureasync.aspx)
[Lire un document de flux pour obtenir une procédure stockée](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L194) | [DocumentClient.ReadDocumentFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readdocumentfeedasync.aspx)
[Créer une procédure stockée avec Order by](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L219) | [DocumentClient.CreateStoredProcedureAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createstoredprocedureasync.aspx) 
[Créer un déclencheur préliminaire](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L264) | [DocumentClient.CreateTriggerAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createtriggerasync.aspx)
[Créer un déclencheur après](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L329) | [DocumentClient.CreateTriggerAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createtriggerasync.aspx)
[Créer un utilisateur défini fonction (UDF)](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/ServerSideScripts/Program.cs#L389) | [DocumentClient.CreateUserDefinedFunctionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync.aspx) 

Pour plus d’informations sur la programmation côté serveur, voir [programmation côté serveur DocumentDB : procédures stockées, déclencheurs de base de données et UDF](documentdb-programming.md).

## <a name="user-management-examples"></a>Exemples de gestion des utilisateurs

Le fichier de gestion des utilisateurs, [azure-documentdb-net/samples/code-samples/UserManagement/Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs), montre comment effectuer les tâches suivantes.

Tâche | Référence de l’API
--- | ---
[Créer un utilisateur](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L81) | [DocumentClient.CreateUserAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createuserasync.aspx)
[Définir des autorisations sur une collection de sites ou un document](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L85) | [DocumentClient.CreatePermissionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createpermissionasync.aspx)
[Obtenir une liste des autorisations d’un utilisateur](https://github.com/Azure/azure-documentdb-net/blob/d17c0ca5be739a359d105cf4112443f65ca2cb72/samples/code-samples/UserManagement/Program.cs#L218) | [DocumentClient.ReadUserAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readuserasync.aspx)<br>[DocumentClient.ReadPermissionFeedAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readpermissionfeedasync.aspx)




