
<properties
    pageTitle="Télécharger une image personnalisée pour Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment télécharger une image personnalisée pour Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Télécharger une image personnalisée pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

À présent que vous avez créé votre image modèle personnalisé ou que vous avez mis à jour avec les modifications, vous devez télécharger cette image dans votre bibliothèque d’images RemoteApp Azure. Suivez ces étapes.


## <a name="before-you-start"></a>Avant de commencer

1.      Vérifiez que votre image personnalisée répond aux [besoins de l’image](remoteapp-imagereqs.md) et [besoins de l’application](remoteapp-appreqs.md).
2.      Installez le [module Azure PowerShell](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Étape par étape sur la façon de télécharger l’image personnalisée

1.      Ouvrez le portail de gestion Azure et accédez à la page RemoteApp.
2.      Sous l’onglet **images de modèle** , cliquez sur **Télécharger** dans la partie inférieure de la page.
4.      Entrez un nom convivial pour votre image et spécifiez l’emplacement de compte de stockage. Vérifiez que celui-ci se trouve au même emplacement que votre collection de sites RemoteApp ou un emplacement où vous souhaitez en créer un.
5.      Lorsque vous y êtes invité, téléchargez le script sur votre ordinateur local.
6.      Copier les paramètres de commande dans la zone de texte dans votre Presse-papiers.
7.      Ouvrez une fenêtre de Windows PowerShell avec élévation de privilèges.
8.      Dans la fenêtre de Windows PowerShell avec élévation de privilèges, accédez au répertoire même où vous avez téléchargé le script.
9.      Coller la commande copiée et appuyez sur **entrée**.

    Le processus de téléchargement commence et durée peut dépendre de nombreux facteurs tels que votre vitesse du réseau et la taille de l’image

11.    Si votre téléchargement n’aboutit pas en raison d’interruption du réseau ou des éléments tel quel, vous pouvez toujours reprendre le processus de téléchargement que vous avez commencé. Pour reprendre un téléchargement, exécutez le script à nouveau à l’aide de la même ligne de commande.

> [AZURE.WARNING] Ne jamais modifier le script de téléchargement. Vérifications spécifiques ont été apportées pour vous assurer que l’image répond à la configuration requise image et les besoins de l’application.

## <a name="common-problems"></a>Problèmes courants

- Vérifiez que vous utilisez Windows PowerShell, pas Azure PowerShell. Vous devez installer le module Azure PowerShell car certains modules sont nécessaires au cours du processus de téléchargement.
- Modifier le script de jamais, validations existe-t-il votre convenance.
- Si le fichier de disque dur virtuel obtient verrouillé lors du téléchargement, copiez le fichier ou déplacez-le vers un nouveau chargement emplacement et essayez à nouveau. Certains processus de Windows qui empêche téléchargement peuvent être.  
