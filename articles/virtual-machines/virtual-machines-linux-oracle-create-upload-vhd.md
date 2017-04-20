<properties
    pageTitle="Créer et télécharger un disque dur virtuel de Linux Oracle | Microsoft Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation Linux Oracle."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Préparer une machine virtuelle Linux Oracle Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Conditions préalables ##

Cet article suppose que vous avez déjà installé un système d’exploitation Linux Oracle sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour plus d’informations, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Notes d’installation Linux Oracle

- Voir également [Général Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Noyau compatible d’Oracle chapeau rouge et leurs UEK3 (Unbreakable entreprise noyau) sont tous deux pris en charge sur Hyper-V et Azure. Pour obtenir de meilleurs résultats, veillez à mettre à jour vers la dernière noyau lors de la préparation de votre disque dur virtuel de Linux Oracle.

- UEK2 d’Oracle n’est pas pris en charge sur Hyper-V et Azure qu’elle ne contienne pas les pilotes requis.

- Le format VHDX n’est pas pris en charge dans Azure, uniquement **fixe disque dur virtuel**.  Vous pouvez convertir le disque au format disque dur virtuel à l’aide du Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir.

- Lors de l’installation du système Linux il est recommandé d’utiliser des partitions standards plutôt que gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. [Gestionnaire de volume logique](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.

- NUMA n’est pas prise en charge de tailles de machine virtuelle plus grandes en raison d’un bogue dans les versions de noyau Linux sous 2.6.37. Ce problème concerne principalement répartitions à l’aide de l’en amont noyau chapeau rouge 2.6.32. Installation manuelle de l’agent Azure Linux (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Vous trouverez plus d’informations dans les étapes ci-dessous.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire.  Vous trouverez plus d’informations dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.

- Assurez-vous que la `Addons` référentiel est activé. Modifier le fichier `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), puis modifiez la ligne `enabled=0` à `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Vous devez effectuer les étapes de configuration spécifiques dans le système d’exploitation pour la machine virtuelle exécuter dans Azure.

1. Dans le volet central de Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

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

        # chkconfig network on

8. Installez python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

9.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette ouvrir « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans noyau compatible d’Oracle chapeau rouge.

    En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduire la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus, qui peut être problématique sur la plus petite taille de la mémoire virtuelle.


10. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

11. Installez l’Agent de Linux Azure en exécutant la commande suivante. La dernière version est 2.0.15.

        # sudo yum install WALinuxAgent

    Notez que l’installation du package WALinuxAgent entraîne la suppression du NetworkManager et packages NetworkManager gnome s’ils n’ont pas déjà supprimés comme décrit dans l’étape 2.

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Modifications apportées à Oracle Linux 7**

Préparation d’une machine virtuelle Oracle Linux 7 d’Azure est très similaire à Oracle Linux 6, cependant, il existe plusieurs différences importantes à noter :

 - Le noyau compatible chapeau rouge et UEK3 d’Oracle prises en charge dans Azure.  Le noyau UEK3 est recommandé.
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

5.  Modifier les règles udev pour éviter de générer des règles statiques pour les interfaces Ethernet. Les règles suivantes peuvent provoquer des problèmes lorsque clonant une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

7. Installez le package python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

8.  Exécutez la commande suivante pour effacer les métadonnées yum actuel et installer des mises à jour :

        # sudo yum clean all
        # sudo yum -y update

9.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette action ouvrir « / etc./par défaut/grub » dans un éditeur de texte et modifier les `GRUB_CMDLINE_LINUX` paramètre, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Il désactive également les conventions d’appellation OEL 7 nouveau pour les cartes réseau. En plus de ce qui précède, il est recommandé de *Supprimer* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduire la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus, qui peut être problématique sur la plus petite taille de la mémoire virtuelle.


10. Une fois que vous avez terminé d’édition « / etc./par défaut/grub » par ci-dessus, exécutez la commande suivante pour reconstruire la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage.  Il s’agit généralement de la valeur par défaut.

12. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange utilisant le disque de ressources locales est associé à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources local est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée. Après l’installation de l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Cliquez sur **Action -> Arrêter vers le bas** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.


## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre .vhd Oracle Linux pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de la première fois que vous vous téléchargez le fichier .vhd vers Azure, consultez les étapes 2 et 3 dans la [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).
