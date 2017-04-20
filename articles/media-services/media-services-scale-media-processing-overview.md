<properties
    pageTitle="Vue d’ensemble du support de traitement de mise à l’échelle | Microsoft Azure"
    description="Cette rubrique concerne une vue d’ensemble de mise à l’échelle de traitement multimédia avec Azure Media Services."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Vue d’ensemble du support de traitement mise à l’échelle

Cette page vous donne Découvrez comment et pourquoi à l’échelle de traitement de support. 

## <a name="overview"></a>Vue d’ensemble

Un compte Media Services est associé à un Type d’unité réservés, qui détermine la vitesse à laquelle vos supports de traitement des tâches sont traités. Vous pouvez choisir entre les types d’unité réservé suivant : **S1**, **S2**ou **S3**. Par exemple, la tâche de codage même s’exécute plus rapidement lorsque vous utilisez la comparaison de type unité **S2** réservés au type de **S1** . Pour plus d’informations, voir les [Types d’unité réservé](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

En plus de spécifier le type d’unité réservés, vous pouvez spécifier pour configurer votre compte avec unités réservées. Le nombre d’unités réservées générés détermine le nombre de tâches de média pouvant être traités simultanément dans un compte donné. Par exemple, si votre compte comporte cinq unités réservées, puis tâches cinq média exécutera simultanément tant comme il existe des tâches à traiter. Les tâches restantes dans la file d’attente et seront sélectionnés pour le traitement dans un ordre séquentiel quand se termine à une tâche en cours d’exécution. Si un compte n’a pas de n’importe quelle unité réservée sa mise en service, puis tâches ne seront récupérés dans un ordre séquentiel. Dans ce cas, le délai d’attente entre une tâche de fin et le numéro de début des dépend de la disponibilité des ressources dans le système.

## <a name="choosing-between-different-reserved-unit-types"></a>Choix parmi les types d’unité réservé différents

Le tableau suivant vous aide à la décision lors du choix entre différentes vitesses codage. Il fournit plusieurs cas de test d’évaluation également et fournit les associations de sécurité URL que vous pouvez utiliser pour télécharger des vidéos sur lequel vous pouvez effectuer vos propres tests :

Scénarios|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Cas d’utilisation prévue| Codage à débit unique. <br/>Fichiers en SD ou en dessous de solutions, ne fois pas sensibles et de prix.|Débit unique et plusieurs débit.<br/>Utilisation normale pour le codage de SD et HD. |Débit unique et plusieurs débit.<br/>Vidéos de résolution HD et 4K complètes. Heure à laquelle le codage de délai d’exécution sensible, plus rapide. 
Test d’évaluation|[Le fichier d’entrée : 5 minutes long 640x360p en 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codage à un seul débit fichier MP4, à la même résolution, permet d’accéder à environ 11 minutes.|[Fichier d’entrée : 5 minutes long 1280x720p en 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codage avec « H264 unique débit 720p » prédéfinis prend environ 5 minutes.<br/><br/>Codage avec « H264 débit plusieurs 720p « prédéfini prend environ 11,5 minutes.|[Le fichier d’entrée : 5 minutes long 1920x1080p en 29,97 images/seconde](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codage avec « H264 unique débit 1080p » prédéfinis prend environ 2,7 minutes.<br/><br/>Codage avec « H264 débit plusieurs 1080p « prédéfini prend environ 5,7 minutes.

##<a name="considerations"></a>Considérations relatives à la

>[AZURE.IMPORTANT] Passez en revue les considérations décrites dans cette section.  

- Unités réservées fonctionnent pour mettre en parallèle tous les traitement multimédia, y compris l’indexation de tâches à l’aide de Indexer de Media Azure.  Toutefois, contrairement à codage, travaux d’indexation ne sont pas traités plus rapidement avec réservés unités plus rapidement.

- Si vous utilisez le pool partagé, autrement dit, sans les unités réservées, puis vos tâches Encoder ont les mêmes performances comme avec S1 RUs. Cependant, il existe aucune limite supérieure à la fois vos tâches peuvent passer dans l’état en file d’attente et à un moment donné, qu’une tâche est en cours d’exécution.

- Les centres de données suivante n’offrent pas le type d’unité **S2** réservé : Brésil Sud, Inde ouest, Inde centrale et du Sud Inde.

- Les centres de données suivante n’offrent pas le type d’unité **S3** réservé : Brésil, Inde ouest, Inde Sud.

- Le plus grand nombre d’unités spécifiées pour la période de 24 heures est utilisé pour calculer le coût.


##<a name="quotas-and-limitations"></a>Les quotas et les limitations

Pour plus d’informations sur les quotas et limitations et comment ouvrir un tickets de support, consultez [les Quotas et les limitations](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Étape suivante

Atteindre la tâche de traitement de médias mise à l’échelle avec une de ces technologies : 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portail](media-services-portal-scale-media-processing.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
