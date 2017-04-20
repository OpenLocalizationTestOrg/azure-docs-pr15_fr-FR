<properties
    pageTitle="Créer et télécharger un disque dur virtuel Ubuntu Linux dans Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation Ubuntu Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Préparer une machine virtuelle Ubuntu Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Images de cloud Ubuntu officiels
Ubuntu publie maintenant officiel durs Azure pour téléchargement à l’adresse [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Si vous avez besoin créer votre propre image Ubuntu spécialisée pour Azure, plutôt que d’utiliser la procédure manuelle ci-après il est recommandé pour démarrer avec ces connus utilisation disques durs virtuels et personnaliser selon vos besoins. Les dernières versions d’image sont toujours accessibles aux emplacements suivants :

 - Ubuntu 12.04/précis : [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/fidèle : [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial : [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Conditions préalables

Cet article suppose que vous avez déjà installé un système d’exploitation Ubuntu Linux sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour plus d’informations, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d’installation Ubuntu**

- Voir également [Général Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.
- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir.
- Lors de l’installation du système Linux il est recommandé d’utiliser des partitions standards plutôt que gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. [Gestionnaire de volume logique](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.
- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire.  Vous trouverez plus d’informations dans les étapes ci-dessous.
- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.


## <a name="manual-steps"></a>Étapes manuelles

> [AZURE.NOTE] Avant de créer votre propre image Ubuntu personnalisée pour Azure, envisagez d’utiliser les images provenant de [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) à la place.


1. Dans le volet central de Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3.  Remplacer les référentiels en cours de l’image à utiliser pensions Azure de Ubuntu. Les procédures varient légèrement en fonction de la version Ubuntu.

    Avant de modifier /etc/apt/sources.list, il est recommandé d’effectuer une sauvegarde :

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Les images Ubuntu Azure maintenant suivent le noyau *activation du matériel* (HOUE). Mettre à jour le système d’exploitation au noyau dernière en exécutant les commandes suivantes :

    Ubuntu 12.04 :

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04 :

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Modifiez la ligne de démarrage noyau pour Grub inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette action ouvrir « / etc./par défaut/grub » dans un éditeur de texte, recherchez la variable appelée `GRUB_CMDLINE_LINUX_DEFAULT` (ou ajoutez-le si nécessaire) et le modifier pour inclure les paramètres suivants :

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Enregistrer et fermer ce fichier puis exécutez «`sudo update-grub`». Ainsi, tous les messages de la console sont envoyées à la première voie série, qui peut aider à support technique Azure avec débogage des problèmes.

6.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

7.  Installer l’Agent Linux Azure :

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Notez que l’installation de la `walinuxagent` package entraîne la suppression du `NetworkManager` et `NetworkManager-gnome` packages, s’ils sont installés.

8.  Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.


## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Ubuntu Linux pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de la première fois que vous vous téléchargez le fichier .vhd vers Azure, consultez les étapes 2 et 3 dans la [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Références ##

Noyau de prise en charge (HOUE) matériel Ubuntu :

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
