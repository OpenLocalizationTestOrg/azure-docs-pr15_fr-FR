<properties
    pageTitle="CDN mise en cache de stratégie dans Extension des Services de support"
    description="Cette rubrique fournit une vue d’ensemble d’un CDN mise en cache de stratégie dans Extension des Services de support."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>CDN mise en cache de stratégie dans Extension des Services de support

Azure Media Services fournit que HTTP en fonction de la diffusion adaptative et télécharger progressive. HTTP en fonction de diffusion en continu est très extensible avec les avantages de la mise en cache proxy et couches CDN, ainsi que la mise en cache côté client. Points de terminaison de la diffusion fournit des fonctionnalités de diffusion en continu générales et également la configuration pour les en-têtes de cache HTTP. Points de terminaison de la diffusion définit HTTP Cache-Control : max-age et les en-têtes de date d’expiration. Vous pouvez obtenir plus d’informations pour les en-têtes HTTP cache de [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>En-têtes de mise en cache par défaut

Par défaut diffusion en continu-points de terminaison appliquent des en-têtes de cache 3 jours de données de diffusion en continu à la demande (media réel fragments/segments) et manifest(playlist). Pour en continu points de terminaison de diffusion en continu appliquent des en-têtes du cache de 3 jours de données (réelle du média fragments/segments), 2 secondes cache en-tête pour les demandes de manifest(playlist). En-têtes du cache de la diffusion en continu à la demande s’appliquent lorsque live programme se transforme en à la demande (une archive en ligne).

##<a name="azure-cdn-integration"></a>Intégration CDN Azure

Azure Media Services fournit [CDN intégré](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) pour la diffusion en continu-points de terminaison. En-têtes de contrôle du cache s’applique de la même façon que les points de terminaison de diffusion en continu à CDN activées points de terminaison de diffusion en continu. Azure CDN utilise diffusion en continu de point de terminaison configuré les valeurs de cache pour définir la durée de vie des objets mis en cache en interne et utilise également cette valeur pour définir la remise en-têtes du cache. Lorsqu’à l’aide de CDN activé les points de terminaison de diffusion en continu il n’est pas recommandé pour définir les valeurs de cache de petite taille. Définition des valeurs petites réduire les performances et réduire l’avantage du CDN. Il n’est pas autorisé à définir des en-têtes de cache inférieures à 600 secondes CDN activé points de terminaison de diffusion en continu.

>[AZURE.IMPORTANT] Azure Media Services intégration avec Azure CDN est installée sur **Azure CDN de Verizon**.  Si vous souhaitez utiliser **Azure CDN à partir d’Akamai** pour Azure Media Services, vous devez [configurer manuellement le point de terminaison](cdn-create-new-endpoint.md).  Pour plus d’informations sur les fonctionnalités Azure CDN, consultez la [vue d’ensemble du fournisseur](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configuration des en-têtes de cache avec Azure Media Services

Vous pouvez utiliser le portail de gestion Azure ou Azure Media Services API pour configurer les valeurs d’en-tête de cache.

1. Pour configurer les en-têtes de cache à l’aide de la gestion des portail reportez-vous à la section [comment gérer les extrémités de diffusion en continu](../media-services/media-services-portal-manage-streaming-endpoints.md) configuration du point de terminaison de la diffusion.
2. Azure Media Services API REST, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services SDK .NET, [Propriétés StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

##<a name="cache-configuration-precedence-order"></a>Ordre de priorité de configuration du cache

1. Valeur de cache Azure Media Services configuré remplace la valeur par défaut.
2. S’il n’existe aucune configuration manuelle, les valeurs par défaut s’applique.
3. En cache 2 secondes par défaut en-têtes applique actives en continu manifest(playlist) quelle que soit configuration Azure Media ou stockage Azure et substitution de cette valeur n’est pas disponible.
