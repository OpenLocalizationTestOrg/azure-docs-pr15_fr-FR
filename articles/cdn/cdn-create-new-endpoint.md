<properties
     pageTitle="À l’aide d’Azure CDN | Microsoft Azure"
     description="Cette rubrique vous explique comment activer le réseau de remise de contenu (CDN) pour Azure. Le didacticiel décrit la création d’un nouveau profil CDN et un point de terminaison."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>À l’aide de CDN Azure  

Cette rubrique décrit l’activation d’Azure CDN en créant un nouveau profil CDN et un point de terminaison.

>[AZURE.IMPORTANT] Pour obtenir une présentation fonctionne CDN, ainsi que d’une liste des fonctionnalités, consultez la [Vue d’ensemble du fournisseur](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Créer un nouveau profil CDN

Un profil CDN est un ensemble de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous souhaiterez peut-être utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine internet, application web ou d’autres critères.

> [AZURE.NOTE] Par défaut, un seul abonnement Azure est limité aux profils CDN huit. Chaque profil CDN est limitée aux points de terminaison de dix CDN.
>
> CDN tarification est appliquée au niveau des profils CDN. Si vous souhaitez utiliser une combinaison de niveaux de prix Azure CDN, vous avez besoin de plusieurs profils CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un point de terminaison CDN

**Pour créer un point de terminaison CDN**

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous pouvez ont épinglée il au tableau de bord à l’étape précédente.  Si vous pas, vous pouvez trouver en cliquant sur **Parcourir**, puis sur **profils CDN**, en cliquant sur le profil que vous envisagez d’ajouter votre point de terminaison.

    La carte de profil CDN s’affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison** .

    ![Ajouter le bouton de point de terminaison][cdn-new-endpoint-button]

    La carte **d’Ajouter un point de terminaison** s’affiche.

    ![Ajouter la carte de point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources mis en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **type d’origine** , sélectionnez le type d’origine.  Sélectionnez **stockage** pour un compte de stockage Azure, le **service Cloud** pour un Service Cloud Azure, **Dans le navigateur** pour un Azure Web App, ou **origine personnalisée** pour toute autre origine serveur web accessibles au public (hébergé dans Azure ou un autre emplacement).

    ![Type d’origine CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Dans la liste déroulante **nom d’hôte d’origine** , sélectionnez ou tapez votre domaine d’origine.  La liste déroulante permet de répertorier toutes les origines disponibles du type spécifié à l’étape 4.  Si vous avez sélectionné *origine personnalisé* en tant que votre **origine tapez**, taper le domaine de votre départ personnalisé.

6. Dans la zone de texte **chemin d’accès d’origine** , entrez le chemin d’accès aux ressources que vous souhaitez mettre en cache ou laissez vide pour autoriser cache n’importe quelle ressource au niveau du domaine que vous avez spécifié à l’étape 5.

7. Dans l' **en-tête de l’hôte Origin**, entrez l’en-tête d’hôte souhaité le CDN pour envoyer à chaque demande, ou conservez la valeur par défaut.

    > [AZURE.WARNING] Certains types d’origines, telles que le stockage Azure et applications Web, nécessitent l’en-tête d’hôte pour faire correspondre le domaine d’origine. À moins d’avoir une origine nécessitant un en-tête d’hôte différent de son domaine, vous devez laisser la valeur par défaut.

8. **Protocole** et **port d’origine**, spécifiez les protocoles et les ports utilisés pour accéder à vos ressources à l’origine.  Au moins un protocole (HTTP ou HTTPS) doit être activée.
    
    > [AZURE.NOTE] Le **port d’origine** n’affecte que le port les utilisations de point de terminaison pour récupérer des informations à l’origine.  Le point de terminaison sera uniquement disponible pour les clients de fin sur la valeur par défaut HTTP et ports HTTPS (80 et 443), quel que soit le **port d’origine**.  
    >
    > Points de terminaison **Azure CDN à partir d’Akamai** ne permettent pas la plage de ports TCP complète pour origines.  Pour une liste des ports d’origine qui ne sont pas autorisés, voir [Azure CDN à partir de Ports d’origine Akamai autorisés](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Accéder à CDN contenu à l’aide de HTTPS comporte les contraintes suivantes :
    > 
    > - Vous devez utiliser le certificat SSL fourni par le CDN. Certificats tiers ne sont pas pris en charge.
    > - Vous devez utiliser le domaine CDN condition (`<endpointname>.azureedge.net`) pour accéder au contenu HTTPS. Prise en charge HTTPS n’est pas disponible pour les noms de domaine (enregistrements CNAME) dans la mesure où le CDN ne prend pas en charge les certificats personnalisés pour le moment.

9. Cliquez sur le bouton **Ajouter** pour créer le nouveau point de terminaison.

10. Une fois que le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour le profil. L’affichage de liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] Le point de terminaison immédiatement sera pas disponible pour une utilisation, comme le temps requis pour l’enregistrement de se propager par le biais du CDN.  Les profils <b>Azure CDN à partir d’Akamai</b> , propagation doivent effectuer généralement en une minute.  Les profils <b>Azure CDN de Verizon</b> , propagation doivent effectuer généralement dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.
    >    
    > Les utilisateurs essaient d’utiliser le nom de domaine CDN avant la configuration de point de terminaison a propagée aux POP recevront les codes de réponse HTTP 404.  S’il a été plusieurs heures dans la mesure où vous avez créé votre point de terminaison et que vous recevez toujours les 404 réponses, voir [points de terminaison de résolution des problèmes CDN renvoi 404 statuts](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Voir aussi
- [Contrôle du comportement de mise en cache de requêtes avec les chaînes de requête](cdn-query-string.md)
- [Comment mapper un CDN contenu à un domaine personnalisé](cdn-map-content-to-custom-domain.md)
- [Chargez au préalable les biens sur un point de terminaison Azure CDN](cdn-preload-endpoint.md)
- [Effacer un point de terminaison CDN Azure](cdn-purge-endpoint.md)
- [Résolution des problèmes de points de terminaison CDN renvoi 404 statuts](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
