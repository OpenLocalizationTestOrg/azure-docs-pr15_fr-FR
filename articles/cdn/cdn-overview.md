<properties
    pageTitle="Vue d’ensemble Azure CDN | Microsoft Azure"
    description="Découvrez ce qui est le réseau de remise de contenu (CDN) Azure et comment l’utiliser pour fournir un contenu haut débit en mettant en cache BLOB et le contenu statique."
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
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Vue d’ensemble du réseau de distribution de contenu Azure (CDN)

> [AZURE.NOTE] Ce document décrit ce que le réseau de remise de contenu (CDN) d’Azure, son fonctionnement et les fonctionnalités de chaque produit Azure CDN.  Si vous souhaitez ignorer ces informations et accéder directement à un didacticiel sur la création d’un point de terminaison CDN, reportez-vous [à l’aide de Azure CDN](cdn-create-new-endpoint.md).  Si vous voulez afficher la liste des emplacements de nœud CDN actuels, voir [Azure CDN POP emplacements](cdn-pop-locations.md).

Le réseau de remise de contenu (CDN) Azure met en cache du contenu web statique en emplacements stratégiques pour fournir débit maximal pour offrir un contenu aux utilisateurs.  Le CDN offre aux développeurs une solution globale pour offrir un contenu haut débit en mettant en cache le contenu en nœuds physiques dans le monde entier. 

Avantages de l’utilisation du CDN aux éléments de site web de cache :

- Optimiser les performances et utilisateur expérience pour les utilisateurs finaux, particulièrement quand l’aide d’applications dans laquelle plusieurs aller-retour est requis pour charger le contenu.
- Grande échelle afin de mieux gérer instantanée élevée, comme au début d’un événement de lancement du produit.
- Par distribuer les demandes d’utilisateur et héberger un contenu à partir de serveurs edge, moins le trafic est envoyé à l’origine.


## <a name="how-it-works"></a>Mode de fonctionnement

![Vue d’ensemble du fournisseur](./media/cdn-overview/cdn-overview.png)

1. Un utilisateur (Alice) demande un fichier (également appelé un bien) à l’aide d’une URL avec un nom de domaine spécial, tel que `<endpointname>.azureedge.net`.  DNS achemine la demande vers les plus performants emplacement de Point de présence (POP).  En règle générale, il s’agit de la POP géographiquement plus proche de l’utilisateur.

2. Si les serveurs de bord de la fenêtre contextuelle n’ont pas le fichier dans leur cache, le serveur edge demande le fichier à l’origine.  L’origine peut être une application Web Azure, Azure Cloud Service, compte de stockage Azure ou n’importe quel serveur web accessibles au public.

3. L’origine renvoie le fichier sur le serveur edge, y compris les en-têtes HTTP facultatifs décrivant Time-to-Live du fichier (TTL).

4. Le serveur edge met en cache le fichier et renvoie le fichier au demandeur d’origine (Alice).  Le fichier reste mis en cache sur le serveur edge avant l’expiration de la durée de vie.  Si l’origine n’a pas été spécifier une durée de vie, la durée de vie par défaut est sept jours.

5. Autres utilisateurs peuvent demander le même fichier à l’aide de ce même URL et peuvent également être dirigés vers ce même POP.

6. Si la durée de vie pour le fichier n’a pas expiré, le serveur edge renvoie le fichier à partir du cache.  Le résultat est une expérience utilisateur plus rapide et plus réactive.


## <a name="azure-cdn-features"></a>Fonctionnalités CDN Azure

Il existe trois produits Azure CDN : **Azure CDN Standard à partir d’Akamai** **Azure CDN Standard de Verizon**et **Azure CDN Premium auprès de Verizon**.  Le tableau suivant répertorie les fonctionnalités disponibles avec chaque produit.

|       | Akamai standard | Verizon standard | Verizon Premium |
|-------|-----------------|------------------|-----------------|
| Intégration simple avec Azure des services tels que [stockage](cdn-create-a-storage-account-with-cdn.md), [Les Services en nuage](cdn-cloud-service-with-cdn.md), [Applications Web](../app-service-web/cdn-websites-with-cdn.md)et [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;**|
| Gestion via [l’API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md)ou [PowerShell](./cdn-manage-powershell.md). | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Prise en charge HTTPS | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Équilibrage de charge | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| Protection [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| IPv4/IPv6 double pile | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Prise en charge du nom de domaine personnalisé](cdn-map-content-to-custom-domain.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [La mise en cache de chaîne de requête](cdn-query-string.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Filtrage geo](cdn-restrict-access-by-country.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Fast purge](cdn-purge-endpoint.md) | **& #x 2713 ;** | **& #x 2713 ;** | **& #x 2713 ;** |
| [Chargement avant de biens](cdn-preload-endpoint.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Analytique Core](cdn-analyze-usage-patterns.md) |  | **& #x 2713 ;** | **& #x 2713 ;** |
| [Prise en charge HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713 ;**  |  |  |
| [Rapports HTTP avancés](cdn-advanced-http-reports.md) | | | **& #x 2713 ;** |
| [Statistiques en temps réel](cdn-real-time-stats.md) | | | **& #x 2713 ;** |
| [Alertes en temps réel](cdn-real-time-alerts.md) | | | **& #x 2713 ;** |
| [Moteur de distribution de contenu personnalisables, fondées sur des règles](cdn-rules-engine.md) | | | **& #x 2713 ;** |
| Paramètres de cache/en-tête (à l’aide du [moteur de règles](cdn-rules-engine.md))  | | | **& #x 2713 ;** |
| Redirection/réécriture d’URL (à l’aide du [moteur de règles](cdn-rules-engine.md)) | | | **& #x 2713 ;** |
| Règles de l’appareil mobile (à l’aide du [moteur de règles](cdn-rules-engine.md))  | | | **& #x 2713 ;** |

>[AZURE.TIP] Existe-t-il une fonctionnalité que vous voulez voir dans Azure CDN ?  [Donnez vos commentaires](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser CDN, reportez-vous [à l’aide de Azure CDN](./cdn-create-new-endpoint.md).

Si vous êtes un client CDN existant, vous pouvez désormais gérer votre point de terminaison CDN via le [portail Microsoft Azure](https://portal.azure.com) ou avec [PowerShell](cdn-manage-powershell.md).

Pour afficher le CDN en action, consultez la [vidéo de notre session Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Découvrez comment automatiser Azure CDN avec [.NET](./cdn-app-dev-net.md) ou [Node.js](./cdn-app-dev-node.md).

Informations de tarification, voir [CDN tarifs](https://azure.microsoft.com/pricing/details/cdn/).
