<properties 
    pageTitle="Gérer les extrémités de la diffusion en continu avec le portail Azure | Microsoft Azure" 
    description="Cette rubrique indique comment gérer les extrémités de la diffusion en continu avec le portail Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gérer les extrémités de la diffusion en continu avec le portail Azure

## <a name="overview"></a>Vue d’ensemble

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Dans Microsoft Azure Media Services, un **Point de terminaison de diffusion en continu** correspond à un service de diffusion en continu qui peut fournir un contenu directement dans une application de lecteur client ou à un réseau de remise du contenu (CDN) pour la distribution davantage. Media Services également intégration transparente Azure CDN. Le flux sortant à partir d’un service StreamingEndpoint peut être un flux en direct ou une vidéo à la demande bien dans votre compte Media Services.

En outre, vous pouvez contrôler la capacité du service de diffusion en continu le point de terminaison pour gérer les besoins croissants de bande passante en ajustant unités de diffusion en continu. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production. Unités de diffusion en continu vous fournissent les capacités de sortie dédié qui peuvent être achetée par incréments de 200 Mbps et des fonctionnalités supplémentaires, notamment : [emballage dynamique](media-services-dynamic-packaging-overview.md)CDN et intégration configuration avancée.

>[AZURE.NOTE]Vous êtes facturé uniquement lorsque votre point de terminaison de diffusion en continu est en état d’exécution.

Cette rubrique fournit une vue d’ensemble des fonctionnalités principales fournies par les points de terminaison de la diffusion. La rubrique indique également comment utiliser le portail Azure pour gérer les points de terminaison de diffusion en continu. Pour plus d’informations sur la façon de mettre à l’échelle du point de terminaison de diffusion en continu, consultez [la](media-services-portal-scale-streaming-endpoints.md) rubrique suivante.

## <a name="start-managing-streaming-endpoints"></a>Commencer la gestion des points de terminaison de diffusion en continu

Pour commencer à gérer les points de terminaison de diffusion en continu de votre compte, procédez comme suit.

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Dans la fenêtre **paramètres** , sélectionnez les **points de terminaison de la diffusion**.

    ![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Ajouter/supprimer un point de terminaison de diffusion en continu

Pour ajouter/supprimer le point de terminaison de diffusion en continu à l’aide du portail Azure, procédez comme suit :

1. Pour ajouter un point de terminaison de diffusion en continu, cliquez sur le **+ point de terminaison** en haut de la page. 
2. Pour supprimer un point de terminaison de diffusion en continu, appuyez sur le bouton **Supprimer** . 

    La valeur par défaut point de terminaison de la diffusion ne peuvent pas être supprimée.
2. Cliquez sur le bouton **Démarrer** pour démarrer la diffusion en continu point de terminaison.

    ![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Par défaut, vous pouvez avoir jusqu'à deux points de terminaison de diffusion en continu. Si vous demandez plus d’informations, consultez [les Quotas et limitations](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Configuration de point de terminaison de la diffusion en continu

Point de terminaison de diffusion en continu permet de configurer les propriétés suivantes lorsque vous avez au moins 1 unité d’échelle : 

- Contrôle d’accès
- Contrôle du cache
- Croisées stratégies d’accès de site

Pour plus d’informations sur ces propriétés, voir [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Vous pouvez configurer le point de terminaison de diffusion en continu en procédant comme suit :

1. Sélectionnez le point de terminaison de diffusion en continu que vous souhaitez configurer.
1. Cliquez sur **paramètres**.
  
Une brève description des champs ci-dessous.

![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Stratégie de cache maximale : permet de configurer la durée de vie du cache de biens pris en charge par ce point de terminaison de diffusion en continu. Si aucune valeur n’est définie, la valeur par défaut est utilisé. Les valeurs par défaut peuvent également être définis directement dans le stockage Azure. Si Azure CDN est activé pour le point de terminaison de diffusion en continu, vous ne devez pas définir la valeur de stratégie de cache à moins de 600 secondes.  

2. Les adresses IP autorisées : permet de spécifier des adresses IP qui seraient autorisés à se connecter au point de terminaison de diffusion en continu publié. Si aucune adresse IP spécifié, n’importe quelle adresse IP pourront se connecter. Adresses IP peuvent être spécifiés comme une seule adresse IP (par exemple, « 10.0.0.1 »), une plage d’IP à l’aide d’une adresse IP et un masque CIDR (par exemple, « 10.0.0.1/22 ») ou une plage IP à l’aide des adresse IP et un masque en numérotation décimale (par exemple, « 10.0.0.1(255.255.255.0)').

3. Configuration pour l’authentification de l’en-tête de signature Akamai : permet de spécifier la configuration de la demande de d’authentification signature en-tête à partir de serveurs Akamai. Expiration est au format UTC.



##<a id="enable_cdn"></a>Activer l’intégration Azure CDN

Vous pouvez spécifier pour activer l’intégration Azure CDN pour un point de terminaison Streaming (elle est désactivée par défaut).

Pour activer l’intégration Azure CDN vrai :

- Point de terminaison de la diffusion en continu doit avoir au moins une unité de diffusion en continu. Si plus tard vous souhaitez définir les unités de l’échelle à 0, vous devez tout d’abord désactiver l’intégration CDN. Par défaut, lorsque vous créez une nouvelle diffusion en continu unité de diffusion en continu un point de terminaison est définie automatiquement.

- Point de terminaison de la diffusion en continu doit être placé dans un état arrêté. Une fois le CDN est activé, vous pouvez démarrer la diffusion en continu point de terminaison. 

Cela peut prendre jusqu'à 90 min de l’intégration Azure CDN obtenir activé.  Il faut jusqu'à deux heures pour que les modifications être active dans tous les POP de CDN.

Intégration CDN est activée dans tous les centres de données Azure : nous ouest, nous Moyen-Orient, Europe du Nord, Europe ouest, Japon ouest, Moyen-Orient Japon, Asie du Sud-est et Asie de l’est.

Une fois qu’elle est activée, la configuration de **Contrôle d’accès** est désactivée.

![Point de terminaison de diffusion en continu](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Azure Media Services intégration avec Azure CDN est installée sur **Azure CDN de Verizon**.  Si vous souhaitez utiliser **Azure CDN à partir d’Akamai** pour Azure Media Services, vous devez [configurer manuellement le point de terminaison](../cdn/cdn-create-new-endpoint.md).  Pour plus d’informations sur les fonctionnalités Azure CDN, consultez la [vue d’ensemble du fournisseur](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Considérations supplémentaires

- Lorsque CDN est activé pour un point de terminaison de diffusion en continu, les clients ne peut pas demander du contenu directement à partir de l’origine. Si vous avez besoin pour tester votre contenu avec ou sans CDN, vous pouvez créer un autre point de terminaison diffusion en continu qui n’est pas CDN activé.
- Votre nom d’hôte diffusion en continu de point de terminaison reste la même après avoir activé CDN. Vous n’avez pas besoin effectuer des modifications à votre flux de travail media services après que CDN est activée. Par exemple, si votre nom d’hôte du point de terminaison diffusion en continu est strasbourg.streaming.mediaservices.windows.net, après avoir activé CDN, le même nom d’hôte exact est utilisé.
- Pour les nouveaux points de terminaison diffusion en continu, vous pouvez activer CDN simplement en créant un point de terminaison ; pour les points de terminaison de diffusion en continu existants, vous devez tout d’abord arrêter le point de terminaison, puis activez le CDN.
 

Pour plus d’informations, voir [intégration annonce Azure Media Services avec Azure CDN (réseau de distribution de contenu)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Étapes suivantes

Passez en revue Media Services rubriques d’apprentissage professionnelles.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
