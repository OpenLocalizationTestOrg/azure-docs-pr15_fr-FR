<properties
    pageTitle="Intégrer un compte de stockage CDN | Microsoft Azure"
    description="Découvrez comment utiliser le réseau de remise de contenu (CDN) Azure à fournir un contenu haut débit en mettant en cache d’objets BLOB à partir du stockage Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Intégrer un compte de stockage CDN

CDN peut être activé pour le contenu du cache de votre espace de stockage Azure. Il permet aux développeurs une solution globale pour offrir un contenu haut débit en mettant en cache d’objets BLOB et le contenu statique des instances de calcul en nœuds physiques aux États-Unis, Europe, Asie, Australie et Amérique du Sud.


## <a name="step-1-create-a-storage-account"></a>Étape 1 : Créer un compte de stockage

La procédure suivante permet de créer un nouveau compte de stockage pour un abonnement Azure. Un compte de stockage permet d’accéder aux services de stockage Azure. Le compte de stockage représente le niveau de l’espace de noms pour accéder à chacun des composants du service de stockage Azure : Blob services, services file d’attente et services de tableau. Pour plus d’informations, reportez-vous à l' [Introduction à Microsoft Azure stockage](../storage/storage-introduction.md).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un administrateur de co-création pour l’abonnement associé.

> [AZURE.NOTE] Il existe plusieurs méthodes que vous pouvez utiliser pour créer un compte de stockage, y compris le portail Azure et Powershell.  Pour ce didacticiel, nous utiliserons le portail Azure.  

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Dans le coin supérieur gauche, sélectionnez **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez **données + stockage**, puis cliquez sur **compte de stockage**.

    La carte de **créer un compte de stockage** s’affiche.

    ![Créer le compte de stockage][create-new-storage-account]

4. Dans le champ **nom** , tapez un nom de sous-domaine. Cette entrée peut contenir 3-24 minuscules et chiffres.

    Cette valeur devient le nom d’hôte dans l’URI qui est utilisée pour adresser Blob, file d’attente ou tableau des ressources pour l’abonnement. Pour répondre à une ressource conteneur dans le service d’objets Blob, vous utiliseriez un URI au format suivant, où * &lt;StorageAccountLabel&gt; * fait référence à la valeur que vous avez tapés dans la zone **Entrez une URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importantes :** L’étiquette URL le sous-domaine du compte de stockage URI des formulaires et doit être unique parmi tous les services hébergés dans Azure.

    Cette valeur est également utilisée comme le nom de ce compte de stockage dans le portail, ou lorsque vous accédez à ce compte par programme.

5. Conservez les paramètres par défaut pour le **modèle de déploiement**, de **type de compte**, de **performances**et de **réplication**. 

6. Sélectionnez l' **abonnement** qui est utilisé avec le compte de stockage.

7. Sélectionner ou créer un **Groupe de ressources**.  Pour plus d’informations sur les groupes de ressources, voir [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Sélectionnez un emplacement pour votre compte de stockage.

8. Cliquez sur **créer**. Le processus de création du compte de stockage peut prendre plusieurs minutes.


## <a name="step-2-create-a-new-cdn-profile"></a>Étape 2 : Créer un nouveau profil CDN

Un profil CDN est un ensemble de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous souhaiterez peut-être utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine internet, application web ou d’autres critères.

> [AZURE.TIP] Si vous disposez déjà d’un profil CDN que vous souhaitez utiliser pour ce didacticiel, passez à [l’étape 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Étape 3 : Créer un point de terminaison CDN

**Pour créer un nouveau point de terminaison CDN pour votre compte de stockage**

1. Dans le [Portail de gestion Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous pouvez ont épinglée il au tableau de bord à l’étape précédente.  Si vous pas, vous pouvez trouver en cliquant sur **Parcourir**, puis sur **profils CDN**, en cliquant sur le profil que vous envisagez d’ajouter votre point de terminaison.

    La carte de profil CDN s’affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison** .

    ![Ajouter le bouton de point de terminaison][cdn-new-endpoint-button]

    La carte **d’Ajouter un point de terminaison** s’affiche.

    ![Ajouter la carte de point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources mis en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **type d’origine** , sélectionnez *stockage*.  

5. Dans la liste déroulante **nom d’hôte d’origine** , sélectionnez votre compte de stockage.

6. Conservez les paramètres par défaut pour **chemin d’accès d’origine**, **en-tête hôte d’origine**et le **port de protocole/Origin**.  Vous devez spécifier au moins un protocole (HTTP ou HTTPS).

    > [AZURE.NOTE] Cette configuration permet à tous vos conteneurs publiquement visibles dans votre compte de stockage pour la mise en cache dans le CDN.  Si vous voulez limiter l’étendue à un seul conteneur, utilisez le **chemin d’accès d’origine**.  Remarque le conteneur doit avoir sa visibilité définie sur public.

7. Cliquez sur le bouton **Ajouter** pour créer le nouveau point de terminaison.

8. Une fois que le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour le profil. L’affichage de liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.NOTE] Le point de terminaison immédiatement sera pas disponible pour une utilisation.  Il peut prendre jusqu'à 90 minutes pour l’enregistrement de se propager via le réseau CDN. Les utilisateurs essaient d’utiliser le nom de domaine CDN immédiatement peuvent recevoir code d’état 404 jusqu'à ce que le contenu est disponible via le CDN.


## <a name="step-4-access-cdn-content"></a>Étape 4 : Accès CDN contenu

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse pour un blob mis en cache sera semblable à ce qui suit :

http://< ;*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Une fois que vous activez l’accès à un compte de stockage CDN ou service hébergé, tous les objets accessibles sont éligibles pour la mise en cache de bord CDN. Si vous modifiez un objet qui se trouve en cache dans le CDN, le nouveau contenu ne sera pas disponible via le CDN jusqu'à ce que le CDN actualise son contenu à la fin de la période de temps de vie contenue mis en cache.

## <a name="step-5-remove-content-from-the-cdn"></a>Étape 5 : Supprimer le contenu à partir du CDN

Si vous ne souhaitez plus cache d’un objet dans le réseau de remise contenu Azure (CDN), vous pouvez prendre une des opérations suivantes :

-   Vous pouvez rendre le conteneur privé au lieu de baisse. Pour plus d’informations, voir [Gérer les accès en lecture anonymes à des conteneurs et des objets BLOB](../storage/storage-manage-access-to-resources.md) .
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
-   Vous pouvez modifier votre service hébergé pour ne plus répondre aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN resteront mis en cache avant l’expiration de la période de temps de vie de l’objet ou jusqu'à ce que le point de terminaison est vidé. À la fin de la période de temps de vie, le CDN vérifie si le point de terminaison CDN est toujours valide et l’objet toujours accessible de manière anonyme. Si elle n’est pas le cas, puis l’objet est ne peut plus être mis en cache.


## <a name="additional-resources"></a>Ressources supplémentaires

-   [Comment mapper un CDN contenu à un domaine personnalisé](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
