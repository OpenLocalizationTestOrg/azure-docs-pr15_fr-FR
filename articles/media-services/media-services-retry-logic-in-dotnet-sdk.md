<properties
    pageTitle="Réessayer logique dans le Kit de développement de Services de support pour .NET | Microsoft Azure"
    description="La rubrique vous donne une vue d’ensemble de la logique de nouvelle tentative de Media Services SDK pour .NET."
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


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Réessayer logique dans le Kit de développement de Services de support pour .NET

Lorsque vous travaillez avec les services Microsoft Azure, défaillances temporaires peuvent se produire. Si un problème temporaire survient, dans la plupart des cas, après plusieurs tentatives l’opération réussit. Le Kit de développement de Services de support pour .NET met en œuvre la logique de nouvelle tentative pour gérer des défaillances temporaires associés aux exceptions et les erreurs qui sont créés par des requêtes web, l’exécution des requêtes, l’enregistrement des modifications et les opérations de stockage.  Par défaut, le Kit de développement de Services de support pour .NET s’exécute quatre nouvelles tentatives avant nouveau levée à votre application. Le code dans votre application doit ensuite gérer cette exception correctement.  
  
 Voici une brève indication de stratégies d’une requête Web, stockage, requête et SaveChanges :  
  
-   La stratégie de stockage est utilisée pour les opérations de stockage blob (chargements ou téléchargement de fichiers de biens).  
  
-   La stratégie d’une requête Web est utilisée pour les requêtes web générique (par exemple, pour obtenir un jeton d’authentification et en résolvant le point de terminaison cluster utilisateurs).  
  
-   La stratégie de requête est utilisée pour interroger entités reste (par exemple, mediaContext.Assets.Where(...)).  
  
-   La stratégie SaveChanges est utilisée pour faire tout ce que les modifications des données au sein du service (par exemple, la création d’une entité une entité, appel d’une fonction de service pour une opération de mise à jour).  
  
 Cette rubrique répertorie les types d’exception et codes d’erreur qui sont gérés par le Kit de développement de Services de support pour .NET logique de nouvelle tentative.  
  
## <a name="exception-types"></a>Types d’exception  

Le tableau suivant décrit les exceptions le Kit de développement de Services de support pour .NET gère ou ne traite pas pour certaines opérations qui peuvent provoquer des erreurs transitoires.  
  
Exception|Requête Web|Espace de stockage|Requête|SaveChanges
----|------|----|---|---
WebException<br/>Pour plus d’informations, consultez la section [WebException les codes d’état](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) .|Oui|Oui|Oui|Oui  
DataServiceClientException<br/> Pour plus d’informations, voir [codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui
DataServiceQueryException<br/> Pour plus d’informations, voir [codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui  
DataServiceRequestException<br/> Pour plus d’informations, voir [codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|N°|Oui|Oui|Oui  
DataServiceTransportException|N°|N°|Oui|Oui
TimeoutException|Oui|Oui|Oui|N°
SocketException|Oui|Oui|Oui|Oui  
StorageException|N°|Oui|N°|N° 
IOException|N°|Oui|N°|N°
  
###  <a name="WebExceptionStatus"></a>Codes d’état WebException  

Le tableau suivant indique les codes d’erreur WebException la logique de nouvelle tentative est activée. L’énumération [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) définit les codes d’état.  
  
État|Requête Web|Espace de stockage|Requête|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|Oui|Oui|Oui|Oui
NameResolutionFailure|Oui|Oui|Oui|Oui  
ProxyNameResolutionFailure|Oui|Oui|Oui|Oui  
SendFailure|Oui|Oui|Oui|Oui
PipelineFailure|Oui|Oui|Oui|N°  
ConnectionClosed|Oui|Oui|Oui|N°  
KeepAliveFailure|Oui|Oui|Oui|N°  
UnknownError|Oui|Oui|Oui|N° 
ReceiveFailure|Oui|Oui|Oui|N°  
RequestCanceled|Oui|Oui|Oui|N°  
Délai d’attente|Oui|Oui|Oui|N°
ProtocolError <br/>La nouvelle tentative sur ProtocolError est contrôlée par la gestion de code d’état HTTP. Pour plus d’informations, voir [codes d’état d’erreur HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode).|Oui|Oui|Oui|Oui|  
  
###  <a name="HTTPStatusCode"></a>Codes d’état d’erreur HTTP  

Lorsque les opérations de requête et SaveChanges lever DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, le code d’état HTTP d’erreur est renvoyé dans la propriété StatusCode.  Le tableau suivant indique les codes d’erreur la logique de nouvelle tentative est activée.  
  
 
État|Requête Web|Espace de stockage|Requête|SaveChanges 
---|----|----|----|----
401|N°|Oui|N°|N°
403|N°|Oui<br/>Gestion des nouvelles tentatives avec les attentes plus de temps.|N°|N°  
408|Oui|Oui|Oui|Oui
429|Oui|Oui|Oui|Oui  
500|Oui|Oui|Oui|N°  
502|Oui|Oui|Oui|N°  
503|Oui|Oui|Oui|Oui  
504|Oui|Oui|Oui|N°  
  
Si vous souhaitez supprimer un aperçu de l’implémentation réelle de Media Services SDK pour .NET logique de nouvelle tentative, consultez [azure sdk de media services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
