<properties 
    pageTitle="Guide de démarrage rapide : Machine apprentissage recommandations API | Microsoft Azure" 
    description="Azure apprentissage automatique recommandations - Guide de démarrage rapide" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Guide de démarrage rapide de l’API Machine apprentissage recommandations

>[AZURE.NOTE]Vous devez commencer au moyen du Service de troubles de l’API de recommandations au lieu de cette version. Le Service de recommandations cognitifs remplacera ce service, et toutes les nouvelles fonctionnalités seront développées il. Elle comporte des nouvelles fonctionnalités telles que le traitement par lots de prise en charge, une meilleure API Explorer, une expérience de l’inscription/facturation surface, plus cohérentes nettoyage API, etc..
> Pour plus d’informations sur la [migration vers le nouveau Service cognitifs](http://aka.ms/recomigrate)


Ce document décrit comment intégré à votre service ou l’application à utiliser Microsoft Azure Machine apprentissage recommandations. Vous trouverez plus d’informations sur l’API de recommandations dans la [Galerie](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Vue d’ensemble

Pour utiliser Azure Machine apprentissage recommandations, vous devez procéder comme suit :

* Créer un modèle : un modèle est un conteneur de vos données d’utilisation, les données du catalogue et le modèle de recommandations.
* Importer des données catalogue - catalogue contient des informations de métadonnées sur les éléments. 
* Importer des données d’utilisation - données d’utilisation peuvent être téléchargés dans une des méthodes de 2 (ou les deux) :
    * En téléchargeant un fichier qui contient les données d’utilisation.
    * En envoyant les événements d’acquisition de données.
    En règle générale, vous téléchargez un fichier de l’utilisation afin de pouvoir créer un modèle de recommandation initiale (démarrage) et utilisez-le jusqu'à ce que le système recueille suffisamment de données en utilisant le format d’acquisition de données.
* Créez un modèle de recommandation : il s’agit d’une opération asynchrone dans lequel le système de recommandation prend toutes les données de l’utilisation et crée un modèle de recommandations. Cette opération peut prendre plusieurs minutes ou plusieurs heures selon la taille des données et les paramètres de configuration de génération. Lors du déclenchement de la version, vous obtiendrez un ID de génération. Utilisez-le pour vérifier lorsque le processus de génération est terminé avant de commencer à utiliser les recommandations.
* Utilisation de recommandations - recommandations obtenir un élément spécifique ou une liste d’éléments.

Toutes les étapes ci-dessus sont effectuées via l’API Azure Machine apprentissage recommandations.  Vous pouvez télécharger un exemple d’application qui mettent en œuvre, chacune de ces étapes à partir de la [ainsi que la galerie.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Limitations

* Le nombre maximal de modèles par abonnement est égal à 10.
* Le nombre maximal d’éléments pouvant être un catalogue est 100 000.
* Le nombre maximal de points de l’utilisation qui sont conservées est ~ 5 000 000. La plus ancienne sera supprimée si nouveaux doit être téléchargés ou signalés.
* La taille maximale de données qui peuvent être envoyées dans billet (importation données catalogue, importer des données de l’utilisation, par exemple) est de 200 Mo.
* Le nombre de transactions par seconde pour construction d’un modèle recommandation n’est pas active est ~ 2TPS. Construction d’un modèle recommandation est active peut contenir jusqu'à 20TPS.

##<a name="integration"></a>Intégration

###<a name="authentication"></a>Authentification
Microsoft Azure Marketplace prend en charge la méthode d’authentification OAuth ou Basic. Vous pouvez trouver facilement les touches de compte en accédant à la clé sur le marché sous [paramètres de votre compte](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Authentification de base
Ajoutez l’en-tête d’autorisation :

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Convertir en base 64 (c#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Convertir en base 64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>URI de service
La racine du service URI de l’API de recommandations Azure Machine d’apprentissage est [ici.](https://api.datamarket.azure.com/amla/recommendations/v2/)

Le service complet URI est exprimé à l’aide des éléments de la spécification OData.

###<a name="api-version"></a>Version de l’API
Chaque appel API aura, à la fin, un paramètre de requête appelé apiVersion doit être définie sur « 1.0 ».

###<a name="ids-are-case-sensitive"></a>Codes respectent la casse
ID, retournée par une des API, respectent la casse et doit être utilisé comme telles lorsqu’il est passé en tant que paramètres dans les appels API suivants. Par exemple, ID de modèle et de catalogue respectent la casse.

###<a name="create-a-model"></a>Créer un modèle
Création d’une demande « Créer modèle » :

| Méthode HTTP | URI |
|:--------|:--------|
|Publier     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
|   %{modelname/}   |   Uniquement des lettres (A-Z, d’a à z), des nombres (0 à 9), des traits d’union (-) et trait de soulignement (_) sont autorisés.<br>Longueur maximale : 20 |
|   apiVersion      | 1.0 |
|||
| Corps de la requête | AUCUN |


**Réponse**:

Code d’état HTTP : 200

- `feed/entry/content/properties/id`-Contient l’ID du modèle.
**Remarque**: ID de modèle respecte la casse.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importer des données de catalogue

Si vous téléchargez plusieurs fichiers de catalogue vers le même modèle avec plusieurs appels, nous insère les nouveaux éléments de catalogue. Éléments existants restent avec les valeurs d’origine.

| Méthode HTTP | URI |
|:--------|:--------|
|Publier     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
|   modelId |   Identificateur unique du modèle (respecter la casse)  |
| nom de fichier | Identificateur textuel du catalogue.<br>Uniquement des lettres (A-Z, d’a à z), des nombres (0 à 9), des traits d’union (-) et trait de soulignement (_) sont autorisés.<br>Longueur maximale : 50 |
|   apiVersion      | 1.0 |
|||
| Corps de la requête | Données de catalogue. Format :<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nom</th><th>Obligatoire</th><th>Type</th><th>Description</th></tr><tr><td>Id d’élément</td><td>Oui</td><td>Longueur alphanumérique, max 50</td><td>Identificateur unique d’un élément</td></tr><tr><td>Nom de l’élément</td><td>Oui</td><td>Longueur alphanumérique, max 255</td><td>Nom de l’élément</td></tr><tr><td>Catégorie de l’élément</td><td>Oui</td><td>Longueur alphanumérique, max 255</td><td>Catégorie à laquelle cet élément appartient (par exemple, cuisine carnets films...)</td></tr><tr><td>Description</td><td>N°</td><td>Longueur alphanumérique, max et 4 000</td><td>Description de cet élément</td></tr></table><br>Taille de fichier maximale est de 200 Mo.<br><br>Exemple :<br><pre>2406e770-769c-4189-89de-1c9283f93a96, Clara Callan, livre<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, la salle oublier : réserver une Fiction (classeur Byzantium),<br>Carnet de 3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework,<br>552a1940-21e4-4399-82bb-594b46d7ed54, retenue d’animaux, livre</pre> |


**Réponse**:

Code d’état HTTP : 200

- `Feed\entry\content\properties\LineCount`-Nombre de lignes acceptées.
- `Feed\entry\content\properties\ErrorCount`-Nombre de lignes qui n’ont pas été insérés en raison d’une erreur.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importer des données d’utilisation

####<a name="uploading-a-file"></a>Téléchargement d’un fichier
Cette section montre comment télécharger des données d’utilisation à l’aide d’un fichier. Vous pouvez appeler cette API avec les données d’utilisation. Toutes les données d’utilisation seront enregistrées pour tous les appels.

| Méthode HTTP | URI |
|:--------|:--------|
|Publier     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
|   modelId |   Identificateur unique du modèle (respecter la casse) |
| nom de fichier | Identificateur textuel du catalogue.<br>Uniquement des lettres (A-Z, d’a à z), des nombres (0 à 9), des traits d’union (-) et trait de soulignement (_) sont autorisés.<br>Longueur maximale : 50 |
|   apiVersion      | 1.0 |
|||
| Corps de la requête | Données d’utilisation. Format :<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nom</th><th>Obligatoire</th><th>Type</th><th>Description</th></tr><tr><td>Id d’utilisateur</td><td>Oui</td><td>Alphanumérique</td><td>Identificateur unique d’un utilisateur</td></tr><tr><td>Id d’élément</td><td>Oui</td><td>Longueur alphanumérique, max 50</td><td>Identificateur unique d’un élément</td></tr><tr><td>Heure</td><td>N°</td><td>Date au format : AAAA/MM/ddTHH (par exemple, 2013/06/20T10:00:00)</td><td>Temps de données</td></tr><tr><td>Événement</td><td>Non, si fourni que vous devez également placer la date</td><td>Une des opérations suivantes :<br>• Cliquez sur<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Achat</td><td></td></tr></table><br>Taille de fichier maximale est de 200 Mo.<br><br>Exemple :<br><pre>149452, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Réponse**:

Code d’état HTTP : 200

- `Feed\entry\content\properties\LineCount`-Nombre de lignes acceptées.
- `Feed\entry\content\properties\ErrorCount`-Nombre de lignes qui n’ont pas été insérés en raison d’une erreur.
- `Feed\entry\content\properties\FileId`-Identificateur de fichier.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>À l’aide d’acquisition de données
Cette section montre comment envoyer des événements en temps réel à Azure Machine apprentissage recommandations, généralement à partir de votre site Web.

| Méthode HTTP | URI |
|:--------|:--------|
|Publier     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Corps de la requête| Entrée de données d’événement pour chaque événement que vous voulez envoyer. Vous devez envoyer pour la même session utilisateur ou navigateur le même ID dans le champ ID de session. (Voir exemple de corps de l’événement ci-dessous).|


- Exemple d’événement « Clic » :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemple d’événement 'RecommendationClick' :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemple d’événement 'AddShopCart' :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemple d’événement 'RemoveShopCart' :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemple d’événement « Achat » :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Exemple d’envoi 2 événements, « Cliquez » et « AddShopCart » :

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Réponse**: code d’état HTTP : 200

###<a name="build-a-recommendation-model"></a>Créez un modèle de recommandations

| Méthode HTTP | URI |
|:--------|:--------|
|Publier     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
| modelId | Identificateur unique du modèle (respecter la casse)  |
| userDescription | Identificateur textuel du catalogue. Notez que si vous utilisez des espaces vous devez coder avec 20 % à la place. Voir l’exemple ci-dessus.<br>Longueur maximale : 50 |
| apiVersion | 1.0 |
|||
| Corps de la requête | AUCUN |

**Réponse**:

Code d’état HTTP : 200

Il s’agit d’une API asynchrone. Vous obtenez un ID de version en tant que réponse. Pour savoir quand la build est terminée, vous devez appeler l’API « Obtenir crée état d’un modèle », recherchez cet identifiant générer dans la réponse. Notez qu’une version peut prendre de quelques minutes et heures selon la taille des données.

Vous ne pouvez pas consommer recommandations jusqu'à ce que la génération se termine.

État de build valide :

- Création : modèle a été créé.
- En attente – génération de modèle a été déclenchée et il est en attente.
- Construction – modèle est générée.
- Succès – Build terminée avec succès.
- Erreur – génération se termine par une défaillance.
- Annulé – génération a été annulée.
- Annulation – Build est en cours d’annulation.


Notez que l’ID de build sont accessibles sous le chemin d’accès suivant :`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Obtenir l’état de génération d’un modèle

| Méthode HTTP | URI |
|:--------|:--------|
|Télécharger     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
|   modelId         |   Identificateur unique du modèle (respecter la casse)    |
|   onlyLastBuild   |   Indique s’il faut renvoyer tous les l’historique de version du modèle ou uniquement l’état de la version la plus récente. |
|   apiVersion      |   1.0                                 |


**Réponse**:

Code d’état HTTP : 200

La réponse inclut une entrée par générer. Chaque entrée comporte les données suivantes :

- `feed/entry/content/properties/UserName`– Nom de l’utilisateur.
- `feed/entry/content/properties/ModelName`– Nom du modèle.
- `feed/entry/content/properties/ModelId`– Identificateur unique du modèle.
- `feed/entry/content/properties/IsDeployed`– Si la version est déployée (également appelé build active).
- `feed/entry/content/properties/BuildId`– Créer identificateur unique.
- `feed/entry/content/properties/BuildType`-Type de la version.
- `feed/entry/content/properties/Status`– Créer état. Peut être une des opérations suivantes : erreur, construction, en attente, Cancelling, annulé, succès
- `feed/entry/content/properties/StatusMessage`– Message d’état détaillées (s’applique uniquement aux États spécifiques).
- `feed/entry/content/properties/Progress`– Créer des cours (%).
- `feed/entry/content/properties/StartTime`– Heure de début générer.
- `feed/entry/content/properties/EndTime`– Créer l’heure de fin.
- `feed/entry/content/properties/ExecutionTime`– Créer la durée.
- `feed/entry/content/properties/ProgressStep`– Plus d’informations sur l’étape actuelle figurant dans une version en cours.

État de build valide :
- – Entrée de générer la demande a été créée.
- En attente – demande de Build a été déclenchée et il est en attente.
- Construction – Build est en cours.
- Succès – Build terminée avec succès.
- Erreur – génération se termine par une défaillance.
- Annulé – génération a été annulée.
- Annulation – Build est en cours d’annulation.

Valeurs valides pour le type de build :
- Rang - rang créer. (Pour classer les détails de build, reportez-vous au document « Machine apprentissage recommandation API documentation »).
- Recommandation - génération de recommandations.
- FBT - fréquemment acheté ensemble générer.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Afficher des recommandations

| Méthode HTTP | URI |
|:--------|:--------|
|Télécharger     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
| modelId | Identificateur unique du modèle (respecter la casse) |
| numéros d’objet | Liste séparées par des virgules des éléments à recommander.<br>Longueur maximale : 1024 |
| numberOfResults | Nombre de résultats requis |
| includeMetatadata | Toujours false une utilisation ultérieure |
| apiVersion | 1.0 |

**Réponse :**

Code d’état HTTP : 200

La réponse inclut une entrée par élément recommandé. Chaque entrée comporte les données suivantes :

- `Feed\entry\content\properties\Id`-ID d’élément recommandés.
- `Feed\entry\content\properties\Name`-Nom de l’élément.
- `Feed\entry\content\properties\Rating`-Évaluation de la recommandation ; nombre plus élevé signifie confiance plus élevé.
- `Feed\entry\content\properties\Reasoning`-Recommandations raisonnement (par exemple, des explications recommandation).

OData XML

La réponse de l’exemple ci-dessous inclut 10 éléments recommandées :

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Modèle de mise à jour
Vous pouvez mettre à jour la description du modèle ou l’ID de build active.
*ID de build active* - chaque génération pour chaque modèle possède un ID de génération. L’ID de build active est la première génération réussie de chaque nouveau modèle. Une fois que vous avez un ID build active et vous faire crée supplémentaires pour le même modèle, vous devez définir explicitement en tant que l’ID de build par défaut si vous voulez. Lorsque vous utilisez des recommandations, si vous ne spécifiez pas l’ID de build que vous souhaitez utiliser, par défaut sera utilisé automatiquement.

Ce mécanisme vous permet de - une fois que vous disposez d’un modèle recommandation en production - pour générer des nouveaux modèles et les tester avant de les promouvoir en production.

| Méthode HTTP | URI |
|:--------|:--------|
|PLACER     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemple :<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Nom du paramètre  |   Valeurs valides                        |
|:--------          |:--------                              |
| ID | Identificateur unique du modèle (respecter la casse) |
| apiVersion | 1.0 |
|||
| Corps de la requête | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Notez que les balises XML Description et ActiveBuildId sont facultatives. Si vous ne souhaitez pas définir Description ou ActiveBuildId, supprimez la balise entière. |

**Réponse**:

Code d’état HTTP : 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Juridiques
Ce document est fourni « en tant que-est ». Informations et avis exprimés dans ce document, y compris les URL et autres références de site Web Internet, peuvent changer sans préavis. Voici quelques exemples mentionnés dans les exemples sont fournis pour illustration uniquement et sont fictifs. Aucune association réelle connexion ou ne doit être déduite. Ce document ne vous fournit pas avec aucun droit légal sur n’importe quel autre propriété intellectuelle dans tous les produits Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à des fins référence. © Microsoft 2014. Tous droits réservés. 
 
