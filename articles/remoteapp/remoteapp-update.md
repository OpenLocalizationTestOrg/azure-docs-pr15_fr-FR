<properties
   pageTitle="Mettre à jour votre collection RemoteApp Azure | Microsoft Azure"
   description="Découvrez comment mettre à jour votre collection de sites RemoteApp Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Mettre à jour une collection de sites dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Il arrivera un moment, inévitablement, lorsque vous devez mettre à jour les applications ou une image dans votre collection de sites RemoteApp Azure. Si vous utilisez l’une des images inclus avec votre abonnement Azure RemoteApp, dans la collection de sites un nuage ou hybride, toutes les mises à jour sont gérées par RemoteApp Azure lui-même, vous pouvez compter ;

Toutefois, si vous utilisez une image personnalisée (que vous avez créé à partir de zéro ou que vous avez créé en modifiant l’un de nos images), vous êtes chargé de la maintenance de l’image et les applications. Si vous devez mettre à jour votre image ou une des applications qu’elle contient, vous devez créer une nouvelle version mise à jour de l’image, puis remplacez l’image existante dans votre collection de sites avec cette nouvelle image mis à jour.

Par conséquent, comment procéder pour mettre à jour votre collection de sites ? Il est assez simple :

1. Mettre à jour l’image que vous avez utilisé dans votre collection de sites. Appliquer tous les correctifs ou mises à jour requises et enregistrez-le avec un nouveau nom.
2. [Télécharger](remoteapp-uploadimage.md) ou [Importer des](remoteapp-image-on-azurevm.md) images à RemoteApp.
3. À présent, dans la page de la collection de sites, cliquez sur **mettre à jour**.
4. Sélectionnez la nouvelle image dans la liste **d’Images de modèle** .
4. Voici la difficulté : vous avez besoin déterminer la façon de traiter tous les utilisateurs qui utilisent une application dans la collection de sites. Vous disposez des choix suivants :
    - **Accorder aux utilisateurs 60 minutes après la mise à jour**. Dès que la mise à jour est terminée, Azure RemoteApp affiche un message à tous les utilisateurs actifs leur demandant à enregistrer leur travail et se déconnecter et reconnectez-vous. Après 60 minutes, tous les utilisateurs actifs qui n’ont pas été déconnecté seront automatiquement fermées. Les utilisateurs peuvent immédiatement reconnectez-vous.
    - **Déconnecter des utilisateurs à immédiatement**. Dès que la mise à jour est terminé, fermez tous les utilisateurs automatiquement sans aucun avertissement la session. Si vous choisissez cette option, les utilisateurs peuvent perdre des données. Toutefois, ils peuvent se reconnecter à l’application immédiatement.

1. Activez la case à cocher pour commencer la mise à jour.
