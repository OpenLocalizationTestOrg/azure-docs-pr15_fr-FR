<properties
    pageTitle="Créer et télécharger un VHD Linux basée sur CentOS dans Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation Linux basée sur CentOS."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Préparer une machine virtuelle CentOS Azure

- [Préparer un ordinateur CentOS 6.x virtuel Azure](#centos-6x)
- [Préparer une machine virtuelle CentOS 7.0 + Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé un CentOS (ou dérivé similaire) système d’exploitation Linux un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour plus d’informations, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


**Notes d’installation centOS**

- Voir également [Général Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir.

- Lors de l’installation du système Linux il est recommandé d’utiliser des partitions standards plutôt que gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes.  [Gestionnaire de volume logique](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.

- NUMA n’est pas prise en charge de tailles de machine virtuelle plus grandes en raison d’un bogue dans les versions de noyau Linux sous 2.6.37. Ce problème concerne principalement répartitions à l’aide de l’en amont noyau chapeau rouge 2.6.32. Installation manuelle de l’agent Azure Linux (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Vous trouverez plus d’informations dans les étapes ci-dessous.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire.  Vous trouverez plus d’informations dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.


## <a name="centos-6x"></a>CentOS 6.x ##

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir une fenêtre de la console pour la machine virtuelle.

3. Désinstaller NetworkManager en exécutant la commande suivante :

        # sudo rpm -e --nodeps NetworkManager

    **Remarque :** Si le package n’est pas déjà installé, cette commande échoue avec un message d’erreur. Il s’agit normalement.

4.  Créez un fichier nommé **réseau** dans la `/etc/sysconfig/` répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Créez un fichier nommé **ifcfg eth0** dans le `/etc/sysconfig/network-scripts/` répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Les règles suivantes peuvent provoquer des problèmes lorsque clonant une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on


8. **6.3 centOS uniquement**: installer les pilotes pour l’intégration des Linux Services (liste de verticale).

    **Important : L’étape n’est valide pour CentOS 6.3 et les versions antérieures.**  Dans CentOS 6.4 + les Services d’intégration Linux sont *déjà disponibles dans le noyau standard*.

    - Suivez les instructions d’installation sur la [page de téléchargement de liste de verticale](https://www.microsoft.com/en-us/download/details.aspx?id=46842) et installez le t/min à votre image.  


9. Installez le package python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

10. Si vous voulez utiliser les copies en miroir OpenLogic qui sont hébergés dans les centres de données Azure, puis remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo par les référentiels suivants.  Cela ajoutera également le référentiel **[openlogic]** qui inclut des packages pour l’agent Azure Linux :

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Remarque :** Le reste de ce guide suppose que vous utilisez au moins le mis en pension [openlogic], qui sera utilisé pour installer l’agent Azure Linux ci-dessous.


11. Ajouter la ligne suivante à /etc/yum.conf :

        http_caching=packages

    Ainsi que **de 6.3 CentOS uniquement** ajouter la ligne suivante :

        exclude=kernel*

12. Désactiver le module yum « fastestmirror » en modifiant le fichier « / etc/yum/pluginconf.d/fastestmirror.conf » et sous `[main]`, tapez ce qui suit :

        set enabled=0

13. Exécutez la commande suivante pour effacer les métadonnées yum actuelles :

        # yum clean all

14. **Sous CentOS 6.3 uniquement**, mettre à jour le noyau à l’aide de la commande suivante :

        # sudo yum --disableexcludes=all install kernel

15. Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans la version du noyau utilisé par CentOS 6.

    En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduire la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus, qui peut être problématique sur la plus petite taille de la mémoire virtuelle.


16. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

17. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent

    Notez que l’installation du package WALinuxAgent entraîne la suppression du NetworkManager et packages NetworkManager gnome s’ils n’ont pas déjà supprimés comme décrit dans l’étape 2.

18. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Modifications CentOS 7 (et dérivés similaires)**

Préparation d’une machine virtuelle CentOS 7 d’Azure est très similaire à 6 CentOS, cependant, il existe plusieurs différences importantes à noter :

 - Le package NetworkManager n’est plus est en conflit avec l’agent Azure Linux. Ce package est installé par défaut et nous vous recommandons de qu’il n’est pas supprimé.
 - GRUB2 est maintenant utilisé comme le chargeur de démarrage par défaut, la procédure de modification des paramètres de noyau a changé (voir ci-dessous).
 - XFS est désormais le système de fichiers par défaut. Le système de fichiers ext4 peut toujours être utilisé si vous le souhaitez.


**Étapes de configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir une fenêtre de la console pour la machine virtuelle.

3.  Créez un fichier nommé **réseau** dans la `/etc/sysconfig/` répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Créez un fichier nommé **ifcfg eth0** dans le `/etc/sysconfig/network-scripts/` répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Les règles suivantes peuvent provoquer des problèmes lorsque clonant une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

7. Installez le package python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

8. Si vous voulez utiliser les copies en miroir OpenLogic qui sont hébergés dans les centres de données Azure, puis remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo par les référentiels suivants.  Cela ajoutera également le référentiel **[openlogic]** qui inclut des packages pour l’agent Azure Linux :

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Remarque :** Le reste de ce guide suppose que vous utilisez au moins le mis en pension [openlogic], qui sera utilisé pour installer l’agent Azure Linux ci-dessous.

9.  Exécutez la commande suivante pour effacer les métadonnées yum actuel et installer des mises à jour :

        # sudo yum clean all
        # sudo yum -y update

10. Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrir « / etc./par défaut/grub » dans un éditeur de texte et modifier les `GRUB_CMDLINE_LINUX` paramètre, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Il désactive également les conventions d’appellation CentOS 7 nouveau pour les cartes réseau. En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduire la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus, qui peut être problématique sur la plus petite taille de la mémoire virtuelle.

11. Une fois que vous avez terminé d’édition « / etc./par défaut/grub » par ci-dessus, exécutez la commande suivante pour reconstruire la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

13. **Uniquement si génération de l’image à partir de VMWare, VirtualBox ou KVM :** Ajouter des modules Hyper-V dans initramfs :

    Modifier `/etc/dracut.conf`, ajouter du contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruire l’initramfs :

        # dracut –f -v

14. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Linux CentOS pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de la première fois que vous vous téléchargez le fichier .vhd vers Azure, consultez les étapes 2 et 3 dans la [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
