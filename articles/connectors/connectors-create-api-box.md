<properties
    pageTitle="Ajouter le lien boîte à vos applications logique | Microsoft Azure"
    description="Vue d’ensemble du connecteur zone avec des paramètres de l’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-box-connector"></a>Prise en main du connecteur de zone
Se connecter à la zone et créer des fichiers et supprimer les fichiers. 

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2015-08-01-aperçu.

Avec la boîte de dialogue, vous pouvez :

- Créer votre flux d’activité basé sur les données que vous obtenez à partir de la zone. 
- Utiliser des déclencheurs lorsqu’un fichier est créé ou mis à jour.
- Utiliser les actions qui copier un fichier, supprimer un fichier et bien plus encore. Ces actions Obtient une réponse et apportez la sortie disponibles pour d’autres actions. Par exemple, lorsqu’un fichier est modifié dans la zone, vous pouvez fournir ce fichier et de messagerie à l’aide d’Office 365.

Pour ajouter une opération dans les applications logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Zone inclut le déclencheur et les actions suivantes.

| Déclencheurs | Actions|
| --- | --- |
|<ul><li>Lors de la création d’un fichier</li><li>Modification d’un fichier</li></ul> | <ul><li>Créer le fichier</li><li>Lors de la création d’un fichier</li><li>Copier un fichier</li><li>Supprimer le fichier</li><li>Extraire archive au dossier</li><li>Obtenir le contenu du fichier à l’aide des id</li><li>Obtenir le contenu du fichier à l’aide du chemin d’accès</li><li>Obtenir les métadonnées de fichier à l’aide des id</li><li>Obtenir les métadonnées de fichier à l’aide de chemin d’accès</li><li>Fichier de mise à jour</li><li>Modification d’un fichier</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-a-connection-to-box"></a>Créer une connexion à la zone
Lorsque vous ajoutez ce connecteur à vos applications logique, vous devez autoriser les applications logique pour vous connecter à votre boîte.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Après avoir créé la connexion, vous entrez des propriétés de la zone. La **référence de l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette connexion zone même dans les autres applications logique.

## <a name="swagger-rest-api-reference"></a>Référence des API REST swagger
S’applique aux versions : 1.0.

### <a name="create-file"></a>Créer le fichier
Télécharge un fichier vers la zone.  
```POST: /datasets/default/files```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|folderPath|chaîne|Oui|requête|Aucun |Télécharger le fichier dans la zone Chemin du dossier|
|nom|chaîne|Oui|requête|Aucun |Nom du fichier à créer dans la zone|
|corps|String(Binary) |Oui|corps|Aucun |Contenu du fichier à télécharger dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="when-a-file-is-created"></a>Lors de la création d’un fichier
Déclenche un flux lorsqu’un fichier est créé dans un dossier boîte.  
```GET: /datasets/default/triggers/onnewfile```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Codedossier|chaîne|Oui|requête|Aucun |Identificateur unique du dossier dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="copy-file"></a>Copier un fichier
Copier un fichier vers la zone.  
```POST: /datasets/default/copyFile```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|chaîne|Oui|requête|Aucun |URL pour le fichier source|
|destination|chaîne|Oui|requête| Aucun|Chemin d’accès du fichier de destination dans la zone, y compris le nom de fichier cible|
|remplacer|valeur booléenne|N°|requête| Aucun|Remplace le fichier de destination si la valeur « true »|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="delete-file"></a>Supprimer le fichier
Supprime un fichier de zone.  
```DELETE: /datasets/default/files/{id}```


| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun |Identificateur unique du fichier à supprimer de zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="extract-archive-to-folder"></a>Extraire archive au dossier
Extrait un fichier d’archive vers un dossier dans la zone (exemple : .zip).  
```POST: /datasets/default/extractFolderV2```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|source|chaîne|Oui|requête| |Chemin d’accès pour le fichier d’archive|
|destination|chaîne|Oui|requête| |Chemin d’accès dans la zone pour extraire le contenu d’archivage|
|remplacer|valeur booléenne|N°|requête| |Remplace les fichiers de destination si la valeur « true »|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-file-content-using-id"></a>Obtenir le contenu du fichier à l’aide des id
Extrait le contenu du fichier à partir de la zone à l’aide des id.  
```GET: /datasets/default/files/{id}/content```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès|Aucun |Identificateur unique du fichier de zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-file-content-using-path"></a>Obtenir le contenu du fichier à l’aide du chemin d’accès
Extrait le contenu du fichier à partir de la zone à l’aide du chemin d’accès.  
```GET: /datasets/default/GetFileContentByPath```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|chemin d’accès|chaîne|Oui|requête|Aucun |Chemin d’accès unique pour le fichier dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-file-metadata-using-id"></a>Obtenir les métadonnées de fichier à l’aide des id
Extrait les métadonnées d’un fichier à partir de la zone à l’aide des id de fichier.  
```GET: /datasets/default/files/{id}```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès| Aucun|Identificateur unique du fichier de zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="get-file-metadata-using-path"></a>Obtenir les métadonnées de fichier à l’aide de chemin d’accès
Extrait les métadonnées d’un fichier à partir de la zone à l’aide du chemin d’accès.  
```GET: /datasets/default/GetFileByPath```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|chemin d’accès|chaîne|Oui|requête|Aucun |Chemin d’accès unique pour le fichier dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="update-file"></a>Fichier de mise à jour
Met à jour un fichier dans la zone.  
```PUT: /datasets/default/files/{id}```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|ID|chaîne|Oui|chemin d’accès| Aucun|Identificateur unique du fichier à mettre à jour dans la zone|
|corps|String(Binary) |Oui|corps|Aucun |Contenu du fichier à mettre à jour dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


### <a name="when-a-file-is-modified"></a>Modification d’un fichier
Déclenche un flux lors de la modification d’un fichier dans un dossier boîte.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nom|Type de données|Obligatoire|Situé dans|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|Codedossier|chaîne|Oui|requête|Aucun |Identificateur unique du dossier dans la zone|

#### <a name="response"></a>Réponse
|Nom|Description|
|---|---|
|200|Bien|
|par défaut|Échoué de l’opération.|


## <a name="object-definitions"></a>Définitions d’objets

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Nom de la propriété | Type de données | Obligatoire|
|---|---|---|
|sous forme de tableau|non défini|aucune|
|objets BLOB|non défini|aucune|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|source|chaîne|aucune|
|nom complet|chaîne|aucune|
|URL|chaîne|aucune|
|tableDisplayName|chaîne|aucune|
|tablePluralName|chaîne|aucune|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|source|chaîne|aucune|
|nom complet|chaîne|aucune|
|URL|chaîne|aucune|

#### <a name="blobmetadata"></a>BlobMetadata

|Nom de la propriété | Type de données |Obligatoire|
|---|---|---|
|ID|chaîne|aucune|
|Nom|chaîne|aucune|
|Nom complet|chaîne|aucune|
|Chemin d’accès|chaîne|aucune|
|LastModified|chaîne|aucune|
|Taille|nombre entier|aucune|
|MediaType|chaîne|aucune|
|IsFolder|valeur booléenne|aucune|
|ETag|chaîne|aucune|
|FileLocator|chaîne|aucune|

## <a name="next-steps"></a>Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).
