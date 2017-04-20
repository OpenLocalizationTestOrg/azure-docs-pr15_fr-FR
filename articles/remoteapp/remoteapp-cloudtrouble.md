
<properties
    pageTitle="Résoudre les problèmes de collections de cloud RemoteApp - création | Microsoft Azure"
    description="Découvrez comment résoudre les problèmes de création d’une collection de sites RemoteApp cloud"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Résoudre les problèmes de créer des collections de cloud RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Si vous rencontrez des problèmes pour créer une collection de cloud, consultez les informations suivantes.

## <a name="your-image-is-invalid"></a>Votre image n’est pas valide ##
Si vous voyez un message comme « GoldImageInvalid » lorsque vous attendez Azure mise en service de votre collection de sites, cela signifie que votre image du modèle ne répond pas à la [défini image exigences](remoteapp-imagereqs.md). Par conséquent, accédez lire ces [exigences](remoteapp-imagereqs.md), résoudre votre image, essayez de créer à nouveau votre collection de sites.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Les erreurs dans les vues dans le portail de gestion d’Azure

    DNS server could not be reached
    ProvisioningTimeout

Cloud collections souvent fail lors de la création en raison que vous utilisez des images personnalisées.  Si vous voyez un des messages d’erreur ci-dessus et que vous utilisez une image personnalisée pour créer la collection, vérifiez les éléments suivants :

- Assurez-vous que l’image personnalisée que vous avez téléchargée répond aux besoins de l’image.
- La plupart du temps le problème courant est que l’image n’a pas correctement préparée avec Sysprep.  
- Vérifiez que l’image peut démarrer dans Hyper-V ou essayez de créer une VM IAAS directement dans votre abonnement Azure à l’aide de l’image. Si la machine virtuelle ne parvient pas à démarrer et lancer pas, puis cela indique généralement que l’image personnalisée n’a pas été préparé correctement.  Vérifiez que l’image personnalisée a été généré suivant la procédure créer une image du modèle personnalisé pour RemoteApp

Si vous utilisez l’une des images Microsoft inclus avec votre abonnement, essayez de créer la collection de sites à nouveau. Si le problème persiste contactez le support technique Microsoft.

    PlatformImageTrialModeOnly

Si vous voyez cette erreur cela signifie généralement que vous été mis à niveau vers un compte payé mais que vous essayez d’utiliser une image fournis par Microsoft valide uniquement pendant le mode d’évaluation du service. Dans ce cas, essayez de créer à nouveau votre collection de cloud, mais n’oubliez pas de spécifier la bonne image.
