<properties
    pageTitle="Créer et télécharger un VHD Linux | Microsoft Azure"
    description="Créer et télécharger un disque dur virtuel Azure (disque dur virtuel) avec le modèle de déploiement classique qui contient le système d’exploitation Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vous pouvez également [télécharger une image de disque personnalisé à l’aide du Gestionnaire de ressources Azure](virtual-machines-linux-upload-vhd.md).

Cet article vous explique comment créer et télécharger un disque dur virtuel (disque dur virtuel) afin d’utiliser votre propre image pour créer des machines virtuelles dans Azure. Découvrez comment préparer le système d’exploitation qui vous permet de créer plusieurs machines virtuelles basées sur cette image. 

>  [AZURE.NOTE] Si vous avez quelques instants, Aidez-nous à améliorer la documentation machine virtuelle Linux Azure en [vue d’ensemble](https://aka.ms/linuxdocsurvey) de votre expérience. Chaque réponse nous permet de vous aident à rendre votre travail.

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez les éléments suivants :

- **Système d’exploitation Linux installé dans un fichier .vhd** - vous avez installé une [distribution Linux Azure par l’éditeur](virtual-machines-linux-endorsed-distros.md) (ou voir [les informations de répartitions non approuvée](virtual-machines-linux-create-upload-generic.md)) sur un disque virtuel dans le format de disque dur virtuel. Il existe plusieurs outils pour créer une machine virtuelle et le disque dur virtuel :
    - Installer et configurer [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser le disque dur virtuel en tant que format de votre image. Si nécessaire, vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert`.
    - Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format de VHDX n’est pas pris en charge dans Azure. Lorsque vous créez une machine virtuelle, spécifiez un format disque dur virtuel. Si nécessaire, vous pouvez convertir des disques VHDX à l’aide du disque dur virtuel [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou le [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) applet de commande PowerShell. En outre, Azure ne reconnaît pas télécharger dynamiques disques durs virtuels, vous devez convertir ces disques durs statiques avant de le télécharger. Vous pouvez utiliser des outils tels que [Azure utilitaires de disque dur virtuel pour accéder](https://github.com/Microsoft/azure-vhd-utils-for-go) à convertir des disques dynamiques au cours du processus de téléchargement dans Azure.

- **Interface de ligne azure** - installer dernière [Interface de ligne Azure](../virtual-machines-command-line-tools.md) pour télécharger le disque dur virtuel.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Étape 1 : Préparer l’image à télécharger

Azure prend en charge les différentes versions de Linux (voir [Répartitions approuvée](virtual-machines-linux-endorsed-distros.md)). Les articles suivants vous guident comment préparer les différentes répartitions Linux prises en charge sur Azure. Après avoir terminé les étapes décrites dans les guides suivants, fournis ici une fois que vous avez un fichier de disque dur virtuel est prêt à télécharger dans Azure :

- **[Distribution centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapeau rouge Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - répartitions Non approuvée](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] La plateforme Azure SLA s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsqu’une des répartitions valablement apposées est utilisée avec les détails de la configuration comme indiqué dans la section « Versions pris en charge » dans [Linux sur répartitions Azure-Endorsed](virtual-machines-linux-endorsed-distros.md). Toutes les éditions Linux dans la galerie d’images Azure est valablement apposées répartitions avec la configuration requise.

Consultez également les **[Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir des conseils plus générales relative à la préparation Linux images pour Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Étape 2 : Préparer la connexion à Azure

Vérifiez que vous utilisez l’infrastructure du langage commun Azure dans le modèle de déploiement classique (`azure config mode asm`), puis connectez-vous à votre compte :

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Étape 3 : Télécharger l’image vers Azure

Vous avez besoin d’un compte de stockage pour télécharger votre fichier de disque dur virtuel. Vous pouvez soit en choisir un compte de stockage existant ou [créez-en un](../storage/storage-create-storage-account.md).

Utiliser l’infrastructure du langage commun Azure pour télécharger l’image à l’aide de la commande suivante :

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Dans l’exemple précédent :

- **BlobStorageURL** est l’URL pour le compte de stockage que vous prévoyez d’utiliser
- **YourImagesFolder** est le conteneur dans stockage blob de l’endroit où vous voulez stocker vos images
- **VHDName** est l’étiquette qui apparaît dans le portail pour identifier le disque dur virtuel.
- **PathToVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre ordinateur.

La figure suivante montre un exemple complet :

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Étape 4 : Créer une machine virtuelle à partir de l’image
Vous créez une machine virtuelle en utilisant `azure vm create` de la même façon comme une machine virtuelle normale. Spécifiez le nom que vous avez donné votre image dans l’étape précédente. Dans l’exemple suivant, nous utilisons le nom de l’image **UbuntuLTS** décrite dans l’étape précédente :

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Pour créer votre propre machines virtuelles, fournissent votre propre nom d’utilisateur + mot de passe, emplacement, nom DNS et nom de l’image.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [informations de référence Azure infrastructure du langage commun pour le modèle de déploiement d’Azure classique](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
