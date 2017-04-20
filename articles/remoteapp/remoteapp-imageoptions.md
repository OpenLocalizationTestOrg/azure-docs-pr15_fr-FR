<properties
    pageTitle="Créer une image RemoteApp Azure | Microsoft Azure"
    description="En savoir plus sur les options disponibles pour la création d’images pour Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="create-an-azure-remoteapp-image"></a>Créer une image RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure utilise des images pour stocker les applications que vous partagez avec d’autres utilisateurs. (Nous effectuez votre image et utilisez-le pour créer des ordinateurs virtuels - de l’accès des utilisateurs lorsqu’ils se dans Azure RemoteApp.) Pour créer une collection Azure RemoteApp avec un choix d’applications, qu’il s’agisse cloud ou hybride, vous commencez par créer une image avec ces applications installées. Ensuite, créez une collection de sites qui utilise cette image, assigner des utilisateurs à la collection de sites et publier des applications à ces utilisateurs.

Vous disposez de plusieurs options pour la création ou l’utilisation d’images. [L’exigence](remoteapp-imagereqs.md) base d’une image est qu’elle exécuter Windows Server 2012 R2 et le Remote Desktop Session hôte (RDSH) rôle est installé. Comment vous obtenez qui est l’endroit où les choses deviennent intéressantes.

Lorsqu’il s’agit d’images, vous disposez des options suivantes :

- Vous pouvez importer et utiliser une [image selon une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Il est préférable pour les applications de métier qui requièrent des paramètres personnalisés. Vous pouvez personnaliser l’image à utiliser pour l’application.
- Vous pouvez [créer et télécharger une image personnalisée](remoteapp-create-custom-image.md). Il est préférable si vous disposez déjà d’une image que vous utilisez pour votre déploiement sur site de Services Bureau à distance.
- Vous pouvez utiliser une des [images de modèle](remoteapp-images.md) incluses dans votre abonnement RemoteApp. Ces images sont créées et conservées par l’équipe RemoteApp et contiennent certaines applications standards (par exemple, la suite Office) que vous pouvez rendre disponible à vos utilisateurs. Notez que seule l’image d’Office 365 ProPlus peut être utilisé dans une structure de production.

Quelle que soit l’endroit où vous obtenez votre image ou comment créer, vous souhaiterez pour vous assurer que vous comprenez les [exigences de l’application](remoteapp-appreqs.md) pour vous assurer que votre application fonctionne parfaitement dans RemoteApp. Ensuite, l’étape suivante consiste à créer une collection de sites [cloud](remoteapp-create-cloud-deployment.md) ou [hybride](remoteapp-create-hybrid-deployment.md) .
