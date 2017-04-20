<properties
    pageTitle="Créer et télécharger un disque dur virtuel SUSE Linux dans Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation SUSE Linux."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Préparer une machine virtuelle SLES ou openSUSE Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé un SUSE ou un système d’exploitation Linux openSUSE sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour plus d’informations, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / notes d’installation openSUSE

- Voir également [Général Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir.

- Lors de l’installation du système Linux il est recommandé d’utiliser des partitions standards plutôt que gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. [Gestionnaire de volume logique](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire.  Vous trouverez plus d’informations dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.


## <a name="use-suse-studio"></a>Utilisez SUSE Studio
[SUSE Studio](http://www.susestudio.com) pouvez facilement créer et gérer vos images SLES et openSUSE pour Azure et Hyper-V. Il s’agit de l’approche recommandée pour la personnalisation de vos propres images SLES et openSUSE.

Comme alternative à la création de votre propre disque dur virtuel, SUSE publie également images BYOS (mettre votre propre abonnement) pour SLES en [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Préparer le Service Pack 4 SUSE Linux Enterprise Server 11 ##

1. Dans le volet central de Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Enregistrez votre système SUSE Linux Enterprise pour lui permettre de télécharger des mises à jour et installer des packages.

4. Mettre à jour le système avec les derniers correctifs :

        # sudo zypper update

5. Installer l’Agent de Linux Azure à partir du référentiel SLES :

        # sudo zypper install WALinuxAgent

6. Vérifier si waagent est défini sur « sous » dans la commande chkconfig et dans le cas contraire, activez-le pour démarrer automatiquement :
               
        # sudo chkconfig waagent on

7. Vérifiez si le service waagent est en cours d’exécution et dans le cas contraire, le démarrer : 

        # sudo service waagent start
                
8. Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette ouvrir « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Cela permet de garantir tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes.

9. Vérifiez que /boot/grub/menu.lst et/etc/fstab les deux référence le disque à l’aide de son UUID (par uuid) au lieu de l’ID du disque (par id). 

    Obtenir UUID sur disque
    
        # ls /dev/disk/by-uuid/

    Si /dev/disk/by-id / est utilisée, mettre à jour /boot/grub/menu.lst et/etc/fstab avec la valeur correcte par uuid

    Avant de modifier
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Après la modification
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Les règles suivantes peuvent provoquer des problèmes lorsque clonant une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. Il est recommandé de modifier le fichier « / etc/sysconfig/réseau/dhcp » et change le `DHCLIENT_SET_HOSTNAME` paramètre à ce qui suit :

        DHCLIENT_SET_HOSTNAME="no"

12. Dans « / etc./sudoers », commenter ou supprimer les lignes suivantes s’ils existent :

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

14. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.


----------

## <a name="prepare-opensuse-131"></a>Préparer openSUSE 13.1 + ##

1. Dans le volet central de Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Sur le shell, exécutez la commande «`zypper lr`». Si cette commande renvoie la sortie semblable à ce qui suit, puis les référentiels sont configurés comme prévu--aucune ajustements ne sont nécessaires (Notez que les numéros de version peuvent varier) :

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Si la commande ne renvoie « Aucun référentiel définies... » puis utilisez les commandes suivantes pour ajouter ces pensions :

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Vous pouvez ensuite vérifier les référentiels ont été ajoutés en exécutant la commande «`zypper lr`» à nouveau. Au cas où un des référentiels de mise à jour appropriée n’est pas activé, activez-le avec la commande suivante :

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Mettre à jour le noyau vers la dernière version disponible :

        # sudo zypper up kernel-default

    Ou mettre à jour le système avec les derniers correctifs :

        # sudo zypper update

5.  Installez l’Agent Linux Azure.

        # sudo zypper install WALinuxAgent

6.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Cela permet de garantir tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. En outre, supprimer les paramètres suivants de la ligne de démarrage noyau s’ils existent :

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  Il est recommandé de modifier le fichier « / etc/sysconfig/réseau/dhcp » et change le `DHCLIENT_SET_HOSTNAME` paramètre à ce qui suit :

        DHCLIENT_SET_HOSTNAME="no"

8.  **Importantes :** Dans « / etc./sudoers », commenter ou supprimer les lignes suivantes s’ils existent :

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

10. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Garantir que le Linux Agent Azure s’exécute au démarrage :

        # sudo systemctl enable waagent.service

13. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel SUSE Linux pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de la première fois que vous vous téléchargez le fichier .vhd vers Azure, consultez les étapes 2 et 3 dans la [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
