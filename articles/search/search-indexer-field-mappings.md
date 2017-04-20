<properties
pageTitle="Mappages de champs dans les indexeurs recherche Azure"
description="Configurer les mappages de champ indexeur recherche Azure pour tenir compte des différences entre les noms de champs et de représentations de données"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Mappages de champs dans les indexeurs recherche Azure

Lorsque vous utilisez indexeurs Azure recherche, vous pouvez parfois vous retrouver dans les situations où vos données d’entrée ne correspondant exactement au schéma de votre index cible. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour transformer vos données dans la forme souhaitée. 

Certaines situations dans lesquelles les mappages de champs sont utiles :
 
- Votre source de données comporte un champ `_id`, mais recherche Azure ne permet pas de noms de champs en commençant par un trait de soulignement. Un mappage des champs vous permet à un champ « Renommer ». 
- Vous voulez remplir plusieurs champs dans l’index avec les mêmes données de sources de données, par exemple parce que vous voulez appliquer différents analyseurs à ces champs. Mappages de champs vous permettent de « branche » dans un champ de source de données.
- Pour en base 64 coder ou décoder vos données. Mappages de champs prennent en charge plusieurs **fonctions de mappage**, y compris les fonctions pour en base 64 encodage et de décodage.   


> [AZURE.IMPORTANT] Fonctionnalité mappages du champ est actuellement dans l’aperçu. Il est disponible uniquement dans l’API REST à l’aide de la version **2015-02-28-aperçu**. Veuillez n’oubliez pas, afficher un aperçu des API destiné de test et d’évaluation et ne doit pas être utilisé dans les environnements de production.

## <a name="setting-up-field-mappings"></a>Configurer des mappages de champs

Vous pouvez ajouter des mappages de champs lors de la création d’un nouvel indexeur à l’aide de l’API d' [Indexeur créer](search-api-indexers-2015-02-28-preview.md#create-indexer) . Vous pouvez gérer les mappages de champs sur un indexeur indexation à l’aide de l’API d' [Indexeur mise à jour](search-api-indexers-2015-02-28-preview.md#update-indexer) . 

Un mappage des champs comprend 3 parties : 

1. A `sourceFieldName`, qui représente un champ dans votre source de données. Cette propriété est requise. 

2. Facultatif `targetFieldName`, qui représente un champ dans votre index de recherche. En cas d’omission, le même nom dans la source de données est utilisé. 

3. Facultatif `mappingFunction`, qui peuvent transformer vos données en utilisant l’un de plusieurs des fonctions prédéfinies. La liste complète des fonctions est [inférieur à](#mappingFunctions).

Mappages de champs sont ajoutés à la `fieldMappings` tableau sur la définition d’indexeur. 

Par exemple, voici comment vous pouvez tenir compte des différences dans les noms de champ : 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Un indexeur peut avoir plusieurs mappages de champs. Par exemple, voici comment vous pouvez « branche » un champ :

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Recherche Azure utilise comparaison respectant la casse pour résoudre les noms de champ et fonction dans les mappages de champ. Cette fonctionnalité est pratique (vous ne devez choisir tous les la casse approprié), mais cela signifie que votre source de données ou un index ne peut pas contenir des champs qui diffèrent uniquement par la casse.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Fonctions de mappage de champ

Ces fonctions sont actuellement prises en charge : 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Effectue le codage en base 64 *adaptée aux URL* de la chaîne d’entrée. Suppose que l’entrée est codée UTF-8. 

#### <a name="sample-use-case"></a>Cas d’utilisation exemple 

Seuls les caractères adaptée aux URL peuvent apparaître dans une clé de document recherche Azure (du fait que les clients doivent être en mesure de résoudre le document à l’aide de l’API de recherche, par exemple). Si vos données contiennent des caractères non fiables l’URL et que vous voulez utiliser pour remplir un champ de clé dans votre index de recherche, utilisez cette fonction.   

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Effectue décodage en base 64 de la chaîne d’entrée. L’entrée est considéré comme un *adaptée aux URL* chaîne codé en base 64. 

#### <a name="sample-use-case"></a>Cas d’utilisation exemple 

Les valeurs de métadonnées personnalisées BLOB doivent être codé en ASCII. Vous pouvez utiliser le codage en base 64 pour représenter les chaînes Unicode arbitraires dans blob des métadonnées personnalisées. Toutefois, pour le rendre recherche significatif, vous pouvez utiliser cette fonction pour retrouver les données codées en chaînes « standard » lors du remplissage votre index de recherche.  

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Fractionne un champ de chaîne en utilisant le séparateur spécifié et sélectionne le jeton à la position spécifiée dans le fractionnement qui en résulte.

Par exemple, si l’entrée est `Jane Doe`, la `delimiter` est `" "`(espace) et le `position` est égal à 0, le résultat est `Jane`; Si la `position` correspond à 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n’existe pas, une erreur est renvoyée.

#### <a name="sample-use-case"></a>Cas d’utilisation exemple 

Votre source de données contient un `PersonName` champ et que vous voulez indexer comme distincte deux `FirstName` et `LastName` champs. Vous pouvez utiliser cette fonction pour fractionner la saisie en utilisant le caractère espace comme séparateur.

#### <a name="parameters"></a>Paramètres

- `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d’entrée.
- `position`: position de base zéro d’entier du jeton choisir après la chaîne d’entrée est fractionnée.    

#### <a name="example"></a>Exemple

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Convertit une chaîne mise en forme sous forme de tableau JSON de chaînes dans un tableau de chaînes qui peut être utilisé pour remplir une `Collection(Edm.String)` champ dans l’index. 

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, puis le champ cible de type `Collection(Edm.String)` est remplie avec les trois valeurs `red`, `white` et `blue`. Pour les valeurs d’entrée ne peut pas être analysés sous forme de tableaux de chaîne JSON, une erreur est renvoyée. 

#### <a name="sample-use-case"></a>Cas d’utilisation exemple

Base de données SQL Azure n’a pas un type de données intégré naturellement mappe vers `Collection(Edm.String)` champs de recherche Azure. Pour remplir les champs de chaîne de collection de sites, mettre en forme de votre source de données sous forme d’un tableau de chaîne JSON et utilisation de cette fonction. 

#### <a name="example"></a>Exemple 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer la recherche Azure

Si vous avez des demandes de fonctionnalités ou idées d’amélioration, veuillez contacter nous sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).