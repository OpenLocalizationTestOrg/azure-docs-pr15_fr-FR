<properties
    pageTitle="Créer et télécharger un VHD Linux dans Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation Linux."
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
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Informations pour répartitions Non approuvée #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Important**: Azure la plateforme SLA s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsque [approuvée répartitions](virtual-machines-linux-endorsed-distros.md) est utilisé. Toutes les éditions Linux qui est proposées dans la galerie d’images Azure est valablement apposées répartitions avec la configuration requise.

- [Linux sur Azure - approuvée répartitions](virtual-machines-linux-endorsed-distros.md)
- [Prise en charge des images Linux dans Microsoft Azure](https://support.microsoft.com/kb/2941892)

Toutes les répartitions s’exécutant sur Azure doit répondre à un certain nombre de conditions préalables pour avoir une chance pour s’exécuter correctement sur la plateforme.  Cet article n’est pas exhaustive que chaque distribution est différente. et il est possible que même si vous répondez aux critères suivants vous devrez sensiblement affiner votre système Linux pour vous assurer qu’il s’exécute correctement sur la plateforme.

C’est pour cette raison que nous vous conseillons de démarrer avec l’un de nos [Linux sur Azure approuvée répartitions](virtual-machines-linux-endorsed-distros.md) lorsque cela est possible. Les articles suivants vous guidera tout au long de la préparation de l’éditions valablement apposée Linux différents qui est prises en charge sur Azure :

- **[Distribution centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapeau rouge Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Le reste de cet article se concentre sur conseils généraux pour exécuter votre distribution Linux sur Azure.


## <a name="general-linux-installation-notes"></a>Notes d’Installation Linux général ##

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir. Si vous utilisez VirtualBox cela signifie sélectionnant **taille fixe** plutôt que d’utiliser la valeur par défaut affectée dynamiquement lors de la création du disque.

- Lors de l’installation du système Linux, il est *recommandé* d’utiliser des partitions standards plutôt que gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre machine virtuelle identique pour résoudre les problèmes. [Gestionnaire de volume logique](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données.

- Prise en charge noyau de montage de systèmes de fichiers UDF est requis. Au premier démarrage sur Azure la configuration de mise en service est passée de Linux VM via des supports au format UDF lié à l’invité. L’agent Azure Linux doit être en mesure de monter le système de fichiers UDF pour lire sa configuration et la mise en service de la machine virtuelle.

- Versions de noyau Linux inférieures à 2.6.37 ne sont pas compatibles NUMA sur Hyper-V avec une taille de mémoire virtuelle supérieure. Ce problème principalement impacts répartitions antérieures à l’aide de l’en amont noyau chapeau rouge 2.6.32 et a été résolu dans RHEL 6.6 (noyau-2.6.32-504). Systèmes personnalisés noyaux antérieurs à 2.6.37 ou noyaux basée sur RHEL plus anciens que 2.6.32-504 devez définir le paramètre de démarrage `numa=off` sur la ligne de commande dans grub.conf noyau. Pour plus d’informations, voir rouge chapeau [436883 Ko](https://access.redhat.com/solutions/436883).

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire.  Vous trouverez plus d’informations dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.


### <a name="installing-linux-without-hyper-v"></a>L’installation de Linux sans Hyper-V ###

Dans certains cas, programmes d’installation Linux ne comprennent pas les pilotes pour Hyper-V le disque virtuel initial (initrd ou initramfs), sauf si elle détecte qu’il s’exécute un un environnement Hyper-V.  Lorsque vous utilisez un système de virtualisation différentes (c'est-à-dire Virtualbox, KVM, etc.) pour préparer votre image Linux, vous devrez peut-être reconstruire l’initrd pour vous assurer qu’au moins le `hv_vmbus` et `hv_storvsc` modules noyau sont disponibles sur le disque virtuel initial.  Il s’agit au moins un problème connu sur les systèmes en fonction de la distribution chapeau rouge en amont.

Le mécanisme de reconstruction de l’image initrd ou initramfs peut-être varier selon la distribution. Veuillez consultez la documentation de votre distribution ou prend en charge de la procédure appropriée.  Voici un exemple pour savoir comment reconstruire l’initrd à l’aide du `mkinitrd` utilitaire :

Sauvegarder tout d’abord, l’image initrd existante :

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Ensuite, reconstruire l’initrd avec la `hv_vmbus` et `hv_storvsc` modules noyau :

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Redimensionnement des disques durs virtuels ###

Images de disque dur virtuel sur Azure doivent avoir une taille virtuelle alignée à 1 Mo.  En règle générale, disques durs virtuels créés à l’aide de Hyper-V doivent déjà être correctement alignés.  Si le disque dur virtuel n’est pas correctement aligné vous pouvez recevoir un message d’erreur semblable au suivant lorsque vous essayez de créer une *image* à partir de votre disque dur virtuel :

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Pour résoudre ce problème, vous pouvez redimensionner la machine virtuelle à l’aide de la console Gestionnaire Hyper-V ou l’applet de commande Powershell [Disque dur virtuel de redimensionnement](http://technet.microsoft.com/library/hh848535.aspx) .  Si vous exécutez pas dans un environnement Windows il est recommandé d’utiliser qemu img à convertir (si nécessaire) et redimensionner le disque dur virtuel.

> [AZURE.NOTE] Il existe un problème connu dans les versions qemu img > = 2.2.1 produit comme résultat un disque dur virtuel pas correctement mis en forme. Le problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou inférieur, ou mettre à jour à 2.6 ou version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Redimensionner le disque dur virtuel directement utiliser des outils tels que `qemu-img` ou `vbox-manage` peut entraîner un disque dur virtuel initialisé.  Donc il est recommandé pour la première conversion du disque dur virtuel à une image de disque brutes.  Si l’image de la machine virtuelle a déjà été créé en tant qu’image disque brut (la valeur par défaut pour certains hyperviseurs telles que KVM) vous pouvez ignorer cette étape :

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calculer la taille de l’image de disque pour vous assurer que la taille virtuelle est alignée à 1 Mo.  Le script shell bash suivantes peut aider à ceci.  Utilise le script «`qemu-img info`» pour déterminer la taille de l’image de disque virtuelle, puis calcule la taille et la 1 Mo suivante :

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Redimensionner le disque brut à l’aide de $rounded_size tel que défini dans le script ci-dessus :

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. À présent, convertissez le disque brutes pour revenir à un disque dur virtuel de taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Configuration requise de noyau Linux ##

Les pilotes Linux Services liste (d’intégration verticale) pour Hyper-V et Azure sont fournis directement au noyau Linux en amont. Plusieurs répartitions incluant une version de noyau Linux récente (c'est-à-dire 3.x) doit disposer de ces pilotes déjà, ou dans le cas contraire fournir des versions utilisées dont leur.  Ces pilotes sont constamment mis à jour dans le noyau en amont avec les nouveaux correctifs et fonctionnalités, afin que lorsque cela est possible qu’il est recommandé d’exécuter un [approuvée distribution](virtual-machines-linux-endorsed-distros.md) incluant ces solutions et les mises à jour.

Si vous exécutez une variante du rouge chapeau Enterprise Linux versions **6.0 6.3**, vous devez installer les pilotes les plus récents liste de verticale pour Hyper-V. Vous trouverez les pilotes [à cet emplacement](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). À partir de RHEL **6.4 +** (et dérivés) les liste de verticale pilotes sont déjà inclus dans le noyau et donc aucun package d’installation supplémentaires n’est nécessaires pour exécuter ces systèmes sur Azure.

Si un noyau personnalisé est requis, il est recommandé d’utiliser une version plus récente de noyau (c'est-à-dire **3,8 +**). Pour ces répartitions ou les fournisseurs qui gèrent leur propre noyau, des efforts seront requises pour backport régulièrement les pilotes de la liste de verticale du noyau en amont à votre noyau personnalisé.  Même si vous exécutez déjà une version de noyau relativement récente, il est recommandé pour effectuer le suivi de tous les correctifs en amont dans les pilotes de liste de verticale et backport celles selon vos besoins. L’emplacement des fichiers de source du pilote liste de verticale est disponible dans le fichier [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dans l’arborescence de source de noyau Linux :

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Au minimum, l’absence de correctifs suivants ont été connus pour générer des problèmes sur Azure et donc ces doivent être incluses dans le noyau. Cette liste n’est pas exhaustive ou achevé pour toutes les répartitions :

- [ata_piix : différer disques aux axes stratégiques Hyper-V par défaut](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc : les paquets en transit dans le chemin d’accès de réinitialisation du compte](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc : éviter l’utilisation de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc : désactiver rédiger même RAID et les pilotes de carte hôte virtuel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc : pointeur NULL supprimer la référence de réparation](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc : échecs de mémoire tampon simultanée peuvent entraîner figer e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs : protéger contre l’exécution de double de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>L’Agent Linux Azure ##

L' [Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md) (waagent) est nécessaire pour mettre en service correctement une machine virtuelle Linux dans Azure. Vous pouvez obtenir la dernière version, les problèmes de fichiers ou envoyer des demandes d’extraction à [mis en pension Linux Agent GitHub](https://github.com/Azure/WALinuxAgent).

- L’agent Linux est publié sous la licence Apache 2.0. Globales pour les nombreuses déjà indiquent t/mn ou deb packages pour l’agent, et donc dans certains cas, cela peut être installée et mises à jour avec un petit effort.

- L’Agent de Linux Azure nécessite Python v2.6 +.

- L’agent requiert également le module python pyasn1. La plupart des versions fournissez un package distinct qui peut être installé.

- Dans certains cas, l’Agent de Linux Azure peut ne pas être compatible avec NetworkManager. Nombre des packages t/min/Deb fournies par répartitions configurer NetworkManager comme un conflit pour le package waagent et par conséquent va désinstaller NetworkManager lorsque vous installez le package de l’agent Linux.


## <a name="general-linux-system-requirements"></a>Général Linux requise ##

- Modifiez la ligne noyau démarrage GRUB ou GRUB2 pour inclure les paramètres suivants. Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes :

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes.

    En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants s’ils existent :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduire la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus, qui peut être problématique sur la plus petite taille de la mémoire virtuelle.

- Installation de l’Agent Linux Azure

    L’Agent de Linux Azure est requis pour la mise en service d’une image Linux sur Azure.  Globales pour les nombreuses fournissent l’agent comme un package t/mn ou Deb (le package est généralement appelé « WALinuxAgent » ou « walinuxagent »).  L’agent peut également être installé manuellement en suivant les étapes décrites dans le [Guide d’agents Linux](virtual-machines-linux-agent-user-guide.md).

- Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

- Ne créez pas d’espace d’échange sur le disque du système d’exploitation

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- En tant que dernière étape, exécutez les commandes suivantes pour deprovision la machine virtuelle :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] Sur Virtualbox, vous pouvez voir l’erreur suivante après l’exécution de ' waagent-force - annulation ' : `[Errno 5] Input/output error`. Ce message d’erreur n’est pas critique et peut être ignoré.

- Vous devrez puis arrêtez la machine virtuelle et téléchargez le disque dur virtuel sur Azure.
