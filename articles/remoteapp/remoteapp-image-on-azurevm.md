<properties
    pageTitle="Créer une image Azure RemoteApp basée sur une machine virtuelle Azure | Microsoft Azure"
    description="Apprenez à créer une image pour Azure RemoteApp à partir d’une machine virtuelle Azure."
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



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Créer une image Azure RemoteApp basée sur une machine virtuelle Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Vous pouvez créer des images Azure RemoteApp (qui contiennent les applications que vous partagez dans votre collection de sites) à partir d’une machine virtuelle Azure. Vous pouvez également choisir d’utiliser une image de machine virtuelle que nous avons ajouté à la galerie d’images machine virtuelle Azure qui répond à toutes les conditions d’image RemoteApp Azure : vous pouvez utiliser cette image machine virtuelle comme point de départ pour votre propre machine virtuelle, si vous voulez. Recherchez simplement l’image « Windows Server hôte Bureau à distance Session » dans la bibliothèque.

Il existe deux étapes pour créer votre propre image basée sur une machine virtuelle Azure - créer l’image, puis téléchargez-le à partir de la bibliothèque machine virtuelle Azure à Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Créer une image personnalisée basée sur une machine virtuelle Azure

Utilisez ces étapes pour créer une image selon une machine virtuelle Azure.

1. Créer une machine virtuelle Azure. Vous pouvez utiliser le « Windows Server hôte Bureau à distance Session » ou l’image « Windows Server Remote Desktop Session hôte avec Microsoft Office 365 ProPlus » à partir de la galerie d’images Azure machine virtuelle. Cette image répond à toutes les conditions d’image modèle RemoteApp Azure.

    Pour plus d’informations, voir [créer une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Se connecter à la machine virtuelle et installer et configurer les applications que vous voulez partager par le biais RemoteApp. Veillez à effectuer toutes les configurations Windows supplémentaires requises par vos applications.

    Pour plus d’informations, voir [comment ouvrir une session sur un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Si vous utilisez l’une des images hôte de la Session Windows Server de bureau à distance, il est un script de validation inclus afin de garantir votre machine virtuelle répond à la pré-reqs. RemoteApp Pour exécuter un script, double-cliquez sur **ValidateRemoteAppImage** sur le bureau. Assurez-vous que toutes les erreurs signalées par le script sont résolus avant de procéder à l’étape suivante.

4. SYSPREP généralisez et capturer l’image. Pour obtenir des instructions, voir [Comment faire pour capturer une Machine virtuelle Windows à utiliser en tant que modèle](../virtual-machines/virtual-machines-windows-classic-capture-image.md) .



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importer l’image dans la bibliothèque d’images RemoteApp Azure

Utilisez ces étapes pour importer la nouvelle image dans Azure RemoteApp :

1. Sous l’onglet **Images de modèle** :
    - Si vous n’avez aucune image existante, cliquez sur **télécharger ou importer une Image du modèle**.
    - Si vous avez déjà au moins une image, cliquez sur **+** pour ajouter une nouvelle image.

2. Sélectionnez **Importer une image à partir de vos Machines virtuelles** bibliothèque, puis cliquez sur **suivant**.

3. Sur la page suivante, sélectionnez votre image personnalisée dans la liste et confirmez que vous avez suivi les étapes indiquées lorsque vous avez créé votre image. Cliquez sur **suivant**.
4. Entrez un nom pour la nouvelle image RemoteApp et choisissez l’emplacement, puis cliquez sur la case à cocher pour démarrer le processus d’importation.

> [AZURE.NOTE] Vous pouvez importer des images à partir de n’importe quel endroit Azure pris en charge par le Machines virtuelles Azure à n’importe quel endroit Azure pris en charge par RemoteApp Azure. Selon les emplacements de l’importation peut prendre jusqu'à 25 minutes.

Vous êtes maintenant prêt à créer votre nouvelle collection, soit une collection de [cloud](remoteapp-create-cloud-deployment.md) ou [hybride](remoteapp-create-hybrid-deployment.md), selon vos besoins.
