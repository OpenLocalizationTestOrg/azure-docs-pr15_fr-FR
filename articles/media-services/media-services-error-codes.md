<properties
    pageTitle="Codes d’erreur Azure Media Services | Microsoft Azure"
    description="La rubrique vous donne une vue d’ensemble des codes d’erreur Azure Media Services."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Codes d’erreur Azure Media Services

Lorsque vous utilisez Microsoft Azure Media Services, vous pouvez recevoir les codes d’erreur HTTP à partir du service en fonction des problèmes, tels que les jetons d’authentification expiration aux actions qui ne sont pas prises en charge dans les Services de support. Voici une liste des **codes d’erreur HTTP** qui peut être retourné par Media Services et les causes possibles pour eux.  
  
## <a name="400-bad-request"></a>400 demande incorrecte

La demande contient des informations incorrectes et est refusée en raison d’une des raisons suivantes :

- Une version non prises en charge de l’API est spécifiée. Pour la version la plus récente, voir [le programme d’installation pour le développement d’API REST Media Services](media-services-rest-how-to-use.md).
- La version de l’API Media Services n’est pas spécifiée. Pour plus d’informations sur la spécification de la version de l’API, consultez l’article [connexion à Media Services avec l’API REST de Services de support](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Si vous utilisez .NET ou Java SDK se pour connecter aux Services de support, la version de l’API est spécifiée pour vous chaque fois que vous essayez et effectuez une action contre Media Services.
- Une propriété non définie a été spécifiée. Le nom de la propriété est le message d’erreur. Seules les propriétés qui sont membres d’une entité donnée peuvent être spécifiées. Voir [Référence des API REST Azure Media Services](http://msdn.microsoft.com/library/azure/hh973617.aspx) pour obtenir une liste d’entités et leurs propriétés.
- Une valeur de propriété non valide a été spécifiée. Le nom de la propriété est le message d’erreur. Consultez le lien précédent pour les types de propriété valide et leurs valeurs.
- Une valeur de propriété est manquante et est requise.
- Partie de l’URL spécifiée contient une valeur incorrecte.
- Une tentative a été effectuée pour mettre à jour une propriété WriteOnce.
- Une tentative a été effectuée pour créer une tâche qui a un bien d’entrée avec une AssetFile principal n’a été spécifié ou n’a pas pu être déterminé..
- Une tentative a été effectuée pour mettre à jour un repère associations de sécurité. Repères de sa peuvent uniquement être créés ou supprimés. Repères de diffusion en continu peut être mis à jour. Pour plus d’informations, voir [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Une opération non prises en charge ou une requête a été envoyé. 

## <a name="401-unauthorized"></a>401 non autorisé

La demande n’a pas pu être authentifiée (avant qu’il peut être autorisé) en raison d’une des raisons suivantes :

- En-tête d’authentification manquant.
- Valeur de l’en-tête d’authentification incorrecte.
    - Le jeton a expiré. Si vous utilisez l’API REST directement, consultez l’article [connexion à Media Services avec l’API REST de Services de support](media-services-rest-connect_programmatically.md) pour apprendre à générer un nouveau jeton d’authentification. Si vous utilisez .NET ou Java SDK, créez un objet CloudMediaContext ou MediaContract pour générer le jeton. Pour plus d’informations sur la façon de procéder, consultez l’article [connexion à Media Services avec le Kit de développement de Services de support pour .NET](media-services-dotnet-connect-programmatically.md).
    - Le jeton contient une signature non valide.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 Interdit

La requête n’est pas autorisée en raison d’une des raisons suivantes :

- Le compte Media Services est introuvable ou a été supprimé.
- Le compte Media Services est désactivé et le type de requête n’est pas HTTP GET. Opérations de service renverra une réponse 403 également.
- Le jeton d’authentification ne contient-elle pas d’informations d’identification de l’utilisateur : nom de compte et/ou SubscriptionId. Vous trouverez ces informations dans l’extension de l’interface utilisateur de Services de support pour votre compte Media Services dans le portail de gestion Azure.
- La ressource ne sont pas accessibles.
    - Une tentative a été effectuée à utiliser un MediaProcessor qui n’est pas disponible pour votre compte Media Services.
    - Une tentative a été effectuée pour mettre à jour un modèle de tâche défini par Media Services.
    - Une tentative d’écraser Locator certaines Media Services l’autre compte.
    - Une tentative d’écraser ContentKey certaines Media Services l’autre compte.

- La ressource n’a pas pu être créée en raison d’un quota de service qui a été atteinte pour le compte Media Services. Pour plus d’informations sur les quotas de service, voir [les Quotas et les limites](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Ressource introuvable

La requête n’est pas autorisée sur une ressource en raison d’une des raisons suivantes :

- Une tentative a été effectuée pour mettre à jour une entité qui n’existe pas.
- Une tentative a été effectuée pour supprimer une entité qui n’existe pas.
- Une tentative a été effectuée pour créer une entité qui permet d’accéder à une entité qui n’existe pas.
- Une tentative a été faite pour obtenir une entité qui n’existe pas.
- Une tentative a été effectuée pour spécifier un compte de stockage qui n’est pas associé au compte Media Services.  

## <a name="409-conflict"></a>409 conflit

La requête n’est pas autorisée en raison d’une des raisons suivantes :

- Plusieurs AssetFile comporte le nom spécifié dans l’élément.
- Une tentative a été effectuée pour créer un deuxième AssetFile principal au sein de l’actif.
- Une tentative a été effectuée pour créer un ContentKey avec l’Id spécifié déjà utilisé.
- Une tentative a été effectuée pour créer un repère avec l’Id spécifié déjà utilisé.
- Plusieurs IngestManifestFile comporte le nom spécifié dans le IngestManifest.
- Une tentative a été effectuée à lier un deuxième chiffrement de stockage ContentKey du bien chiffrées de stockage.
- Une tentative a été effectuée pour lier la même ContentKey à l’actif.
- Une tentative a été effectuée pour créer un repère à une ressource dont le conteneur stockage est manquant ou n’est plus associé à l’actif.
- Une tentative a été effectuée pour créer un repère à une ressource qui a déjà 5 Locator en cours d’utilisation. (Stockage azure applique la limite de cinq stratégies d’accès partagé sur un seul conteneur de stockage.)
- Liaison compte de stockage d’un bien pour une IngestManifestAsset n’est pas identique au compte de stockage utilisé par le parent IngestManifest.  

## <a name="500-internal-server-error"></a>Erreur interne du serveur 500

Pendant le traitement de la demande, Media Services rencontre une erreur qui empêche le traitement de continuer. Cela peut être dû à une des raisons suivantes :

- Création d’une ressource ou une tâche échoue, car les informations de quota d’un compte Media Services service sont temporairement indisponibles.
- Création d’un conteneur de stockage blob actif ou IngestManifest échoue, car les informations de compte de stockage du compte sont temporairement indisponibles.
- Autre erreur inattendue. 

## <a name="503-service-unavailable"></a>503 Service indisponible

Le serveur est actuellement incapable de recevoir des demandes. Cette erreur peut être provoquée par un excès demandes du service. Media Services la limitation mécanisme limite l’utilisation des ressources pour les applications qui vous demande un excès au service.

>[AZURE.NOTE]Vérifier le message d’erreur et la chaîne de code d’erreur pour obtenir des informations plus détaillées sur la raison pour laquelle vous vous trouvez l’erreur 503. Cette erreur ne signifie pas toujours la limitation.

Description d’état possibles est :

- « Le serveur est occupé (e). Séries précédentes de ce type de demande a pris plus de {0} secondes. »
- « Le serveur est occupé (e). Plus de requêtes {0} par seconde peuvent limiter les. »
- « Le serveur est occupé (e). Plus de que peuvent limiter les demandes de {0} dans {1} secondes. »

Pour gérer cette erreur, nous vous recommandons d’utiliser exponentielle recul logique des nouvelles tentatives. Que signifie utilise progressivement plus attend entre les nouvelles tentatives pour les réponses d’erreurs consécutives.  Pour plus d’informations, voir [Bloc d’Application de gestion des pannes transitoires](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Si vous utilisez [Azure Media Services SDK pour .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), la logique de nouvelle tentative de l’erreur 503 a été implémentée par le Kit de développement.  
  
## <a name="see-also"></a>Voir aussi  

[Codes d’erreur de gestion des Services multimédia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
