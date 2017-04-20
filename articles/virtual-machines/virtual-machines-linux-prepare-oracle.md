<properties
pageTitle="Préparer une Machine virtuelle de Linux Oracle Azure | Microsoft Azure"
description="Configuration étape par étape d’une machine virtuelle Oracle exécutant Linux dans Microsoft Azure."
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Préparer une machine virtuelle Linux Oracle Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Préparer une machine virtuelle Linux Oracle 6.4 + Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [Préparer une machine virtuelle Linux Oracle 7.0 + Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez déjà installé un système d’exploitation Linux Oracle sur un disque dur virtuel. Il existe plusieurs outils pour créer des fichiers .vhd, par exemple une solution de virtualisation telles que Hyper-V. Pour plus d’informations, voir [installer Hyper-V et créer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d’installation Linux Oracle**

- Noyau compatible d’Oracle chapeau rouge et ses UEK3 (Unbreakable entreprise noyau) sont tous deux pris en charge sur Hyper-V et Azure. Pour obtenir de meilleurs résultats, veillez à mettre à jour vers la dernière noyau lors de la préparation de votre disque dur virtuel de Linux Oracle.

- UEK2 d’Oracle n’est pas pris en charge sur Hyper-V et Azure qu’elle ne contienne pas les pilotes requis.

- Le nouveau format de VHDX n’est pas pris en charge dans Azure. Vous pouvez convertir le disque au format disque dur virtuel à l’aide de Gestionnaire Hyper-V ou l’applet de commande de disque dur virtuel convertir.

- Lorsque vous installez le système Linux, nous vous recommandons d’utiliser des partitions standard plutôt que le Gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. Gestionnaire de volume logique ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.

- NUMA n’est pas prise en charge de tailles de machine virtuelle plus grandes en raison d’un bogue dans les versions de noyau Linux sous 2.6.37. Ce problème concerne principalement répartitions qui utilisent l’en amont noyau chapeau rouge 2.6.32. Installation manuelle de l’agent Azure Linux (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Vous trouverez plus d’informations dans les étapes ci-dessous.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L’agent Linux peut être configuré pour créer un fichier d’échange sur le disque ressource temporaire. Vous trouverez plus d’informations dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.

- Assurez-vous que la `Addons` référentiel est activé. Cliquez sur Modifier le fichier `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) et modifiez la ligne `enabled=0` à `enabled=1` sous **[ol6_addons]** ou **[ol7_addons]** dans ce fichier.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Vous devez effectuer les étapes de configuration spécifiques dans le système d’exploitation pour la machine virtuelle exécuter dans Azure.

1. Dans le volet central de Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **se connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Désinstaller NetworkManager en exécutant la commande suivante :

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE] Si le package n’est pas déjà installé, cette commande échoue avec un message d’erreur. Il s’agit normalement.

4. Créez un fichier nommé **réseau** dans/etc/sysconfig/répertoire qui contient le texte suivant :

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  Créez un fichier nommé **ifcfg eth0** dans les /etc/sysconfig/network-scripts / répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  Déplacez (ou supprimez) règles udev pour éviter de générer des règles statiques de l’interface Ethernet. Ces règles poser des problèmes lorsque vous dupliquez une machine virtuelle dans Azure ou Hyper-v :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # chkconfig network on

8.  Installez python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

9.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans noyau compatible d’Oracle chapeau rouge.

    En plus de ce qui précède, il est recommandé que vous *supprimez* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique sur la plus petite taille de la mémoire virtuelle.

10.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut.

11.  Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # <a name="sudo-yum-install-walinuxagent"></a>installation d’yum sudo WALinuxAgent

    Notez que l’installation du package WALinuxAgent entraîne la suppression du NetworkManager et packages NetworkManager gnome s’ils n’ont pas déjà supprimés comme décrit dans l’étape 2.

12.  Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources locales est un disque *temporaire* , et il peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 ## Remarque : ce définie sur tout ce que vous voulez qu’il soit.

13.  Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - annulation
        # <a name="export-histsize0"></a>Exporter HISTSIZE = 0
        # <a name="logout"></a>déconnexion

14.  Cliquez sur **Action -\> arrêter** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.

## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Modifications apportées à Oracle Linux 7**

Préparation d’une machine virtuelle Oracle Linux 7 d’Azure est très similaire au processus pour Oracle Linux 6. Toutefois, il existe plusieurs différences importantes à noter :

-   Le noyau compatible chapeau rouge et UEK3 d’Oracle prises en charge dans Azure. Nous vous recommandons le noyau UEK3.

-   Le package NetworkManager n’est plus est en conflit avec l’agent Azure Linux. Ce package est installé par défaut, et il est recommandé qu’il n’est pas supprimé.

-   GRUB2 est maintenant utilisé comme le chargeur de démarrage par défaut, la procédure de modification des paramètres de noyau a changé (voir ci-dessous).

-   XFS est désormais le système de fichiers par défaut. Le système de fichiers ext4 peut toujours être utilisé si vous le souhaitez.

**Étapes de configuration**

1.  Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.  Cliquez sur **se connecter** pour ouvrir une fenêtre de la console pour la machine virtuelle.

3.  Créez un fichier nommé **réseau** dans/etc/sysconfig/répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Créez un fichier nommé **ifcfg eth0** dans les /etc/sysconfig/network-scripts / répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  Déplacez (ou supprimez) règles udev pour éviter de générer des règles statiques de l’interface Ethernet. Ces règles poser des problèmes lorsque vous dupliquez une machine virtuelle dans Microsoft Azure ou Hyper-V.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

7.  Installez le package python pyasn1 en exécutant la commande suivante :

        # sudo yum install python-pyasn1

8.  Exécutez la commande suivante pour effacer les métadonnées yum actuel et installer des mises à jour :

        # sudo yum clean all
        # sudo yum -y update

9.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrir « / etc./par défaut/grub » dans un éditeur de texte et modifier le GRUB\_ligne de commande\_paramètre LINUX, par exemple :

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    Cela garantit également tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. En plus de ce qui précède, il est recommandé que vous *supprimez* les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    La `crashkernel` option peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique sur la plus petite taille de la mémoire virtuelle.

10.  Une fois que vous avez terminé d’édition « / etc./par défaut/grub », exécutez la commande suivante pour reconstruire la configuration grub :

        # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2 mkconfig - o /boot/grub2/grub.cfg

11.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut.

12.  Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # <a name="sudo-yum-install-walinuxagent"></a>installation d’yum sudo WALinuxAgent

13.  Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle après la mise en service sur Azure. Notez que le disque de ressources locales est un disque *temporaire* , et il peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Remarque : ce définie sur tout ce que vous voulez qu’il soit.

14.  Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-force - annulation
        # <a name="export-histsize0"></a>Exporter HISTSIZE = 0
        # <a name="logout"></a>déconnexion

15.  Cliquez sur **Action -\> arrêter** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.
