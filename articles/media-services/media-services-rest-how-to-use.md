<properties 
    pageTitle="Vue d’ensemble de l’API REST Media Services | Microsoft Azure" 
    description="Vue d’ensemble de l’API REST de Services de support" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako"/>


# <a name="media-services-rest-api-overview"></a>Vue d’ensemble de l’API REST de Services de support 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure Media Services est un service qui accepte les demandes HTTP basée sur OData et peuvent y répondre dans détaillée JSON ou atom + publication. Étant donné que Media Services est conforme aux règles de conception Azure, il est un ensemble d’en-têtes HTTP requis que chaque client doit utiliser lors de la connexion aux Services de support, ainsi qu’un ensemble d’en-têtes facultatifs qui peut être utilisé. Les sections suivantes décrivent les en-têtes et verbes HTTP que vous pouvez utiliser lorsque création de requêtes et recevoir des réponses à partir des Services de support.

##<a name="considerations"></a>Considérations relatives à la 

Les considérations suivantes s’appliquent lorsque vous utilisez le reste.

- Lorsque vous interrogez entités, il est limité à 1000 entités retournées en même temps, car public reste v2 limite les résultats de requête dans les résultats de 1000. Vous devez utiliser **Ignorer** et **prendre** (.NET) / **haut** (REST) comme décrit dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [Cet exemple API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 

- Lors de l’utilisation de JSON et en spécifiant pour utiliser le mot-clé **__metadata** dans la demande (par exemple, pour fait référence à un objet lié) vous devez définir l’en-tête **Accepter** au [format JSON détaillée](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (voir l’exemple suivant). OData ne comprend pas la propriété **__metadata** dans la demande, sauf si vous affectez la valeur détaillée.  

        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
        
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 
        

## <a name="standard-http-request-headers-supported-by-media-services"></a>En-têtes de demande HTTP standards pris en charge par Media Services

Pour chaque appel que transformer Media Services, un ensemble d’en-têtes requis, que vous devez inclure dans votre demande et un ensemble d’en-têtes facultatifs que vous pouvez également à inclure. Le tableau suivant répertorie les en-têtes requis :


En-tête|Type|Valeur
---|---|---
Autorisation|PORTEUR|PORTEUR est le mécanisme d’autorisation acceptée uniquement. La valeur doit également inclure le jeton d’accès fourni par l’ACS.
x-ms-version|Decimal|2.11
DataServiceVersion|Decimal|3.0
MaxDataServiceVersion|Decimal|3.0



>[AZURE.NOTE] Étant donné que Media Services utilise OData pour exposer son référentiel de métadonnées bien sous-jacent via des API REST, les en-têtes de DataServiceVersion et MaxDataServiceVersion doivent être inclus dans toute demande ; Toutefois, si elles ne sont pas, puis actuellement Media Services suppose que la valeur DataServiceVersion en cours d’utilisation est 3.0.

Voici un ensemble d’en-têtes facultatifs :

En-tête|Type|Valeur
---|---|---
Date|Date RFC 1123|Horodatage de la requête
Accepter|Type de contenu|Le type de contenu requis pour la réponse comme suit :<p> -application/json ; odata = détaillée<p> -application/atom + xml<p> Les réponses peut-être à un autre type de contenu, par exemple une extraction blob, où une réponse correcte contiendra le flux d’objets blob comme la charge utile.
Codage d’accepter|Gzip, compressé|GZIP et DEFLATE codage, le cas échéant. Remarque : Pour les ressources de grande taille, Media Services peut ignorer cet en-tête et retourner des données non compressées.
Langue accepter|« us », « es » et ainsi de suite.|Spécifie la langue par défaut pour la réponse.
Jeu de caractères accepter|Type de jeu de caractères comme « UTF-8 »|Valeur par défaut est UTF-8.
Méthode de X HTTP|Méthode HTTP|Permet aux clients ou les pare-feu qui ne prennent pas en charge les méthodes HTTP telles que place ou suppression d’utiliser ces méthodes, envoyé par tunnel via un appel GET.
Type de contenu|Type de contenu|Les demandes de type de contenu du corps du demande en place ou billet.
id de demande de client|Chaîne|Valeur définie par l’appelant qui identifie la demande donnée. Si spécifié, cette valeur est incluse dans le message de réponse comme un moyen pour mapper la requête. <p><p>**Important**<p>Valeurs doivent être délimitées par 2096b (2k).

## <a name="standard-http-response-headers-supported-by-media-services"></a>En-têtes de réponse HTTP standard pris en charge par Media Services

Voici un ensemble d’en-têtes qui peut être retournée pour vous en fonction de la ressource que votre demande et l’action que vous permet de réaliser.


En-tête|Type|Valeur
---|---|---
id de la demande|Chaîne|Un identificateur unique pour l’opération actuelle, générés par le service.
id de demande de client|Chaîne|Un identificateur spécifié par l’appelant dans la demande d’origine, le cas échéant.
Date|Date RFC 1123|Date à laquelle la demande a été traitée.
Type de contenu|Variable.|Le type de contenu du corps de réponse.
Codage de contenu|Variable.|Gzip ou compressé, le cas échéant.


## <a name="standard-http-verbs-supported-by-media-services"></a>Verbes HTTP standard pris en charge par Media Services

Voici une liste complète des verbes HTTP qui peut être utilisé lorsque les demandes de fabrication HTTP :


Verbe|Description
---|---
Télécharger|Renvoie la valeur actuelle d’un objet.
Publier|Crée un objet en fonction des données fournies ou envoie une commande.
PLACER|Remplace un objet, ou crée un objet nommé (le cas échéant).
SUPPRIMER|Supprime un objet.
FUSION ET PUBLIPOSTAGE|Met à jour un objet existant avec les modifications de la propriété nommée.
TÊTE|Retourne les métadonnées d’un objet d’une réponse GET.

##<a name="limitation"></a>Limitation

Lorsque vous interrogez entités, il est limité à 1000 entités retournées en même temps, car public reste v2 limite les résultats de requête dans les résultats de 1000. Vous devez utiliser **Ignorer** et **prendre** (.NET) / **haut** (REST) comme décrit dans [Cet exemple .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) et [Cet exemple API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 


## <a name="discovering-media-services-model"></a>Découverte de modèle Media Services

Pour rendre entités Media Services plus détectable, l’opération $metadata peut être utilisée. Il permet d’extraire tous les types d’entités valide, propriétés de l’entité, les associations, fonctions, actions et ainsi de suite. L’exemple suivant montre comment construire l’URI : https://media.windows.net/API/$ métadonnées.

Vous devez ajouter « ? api version=2.x » à la fin de l’URI si vous souhaitez afficher les métadonnées dans un navigateur ou ne pas incluez l’en-tête x-ms-version dans votre demande.



##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





 
