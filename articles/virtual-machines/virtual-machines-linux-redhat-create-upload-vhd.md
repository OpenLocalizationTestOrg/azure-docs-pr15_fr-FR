<properties
    pageTitle="Créer et télécharger un dur rouge chapeau Enterprise Linux à utiliser dans Azure"
    description="Découvrez comment créer et télécharger un Azure disque dur virtuel (disque dur virtuel) qui contient un système d’exploitation Red chapeau Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Préparer une machine virtuelle rouge chapeau Azure

Dans cet article, vous allez apprendre à préparer une machine virtuelle Red chapeau Enterprise Linux (RHEL) à utiliser dans Azure. Versions de RHEL qui sont décrites dans cet article sont 6,7, 7.1 et 7.2. Hyperviseurs de préparation qui sont décrites dans cet article sont Hyper-V, basés sur noyau Machine virtuelle (KVM) et VMware. Pour plus d’informations sur les conditions d’éligibilité requises pour participer au programme d’accès par le Cloud du rouge chapeau de, voir [site Web de rouge chapeau accès par le Cloud](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) et [RHEL en cours d’exécution sur Azure](https://access.redhat.com/articles/1989673).

[Préparer une machine virtuelle RHEL 6,7 à partir du Gestionnaire Hyper-V](#rhel67hyperv)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir du Gestionnaire Hyper-V](#rhel7xhyperv)

[Préparer une machine virtuelle RHEL 6,7 à partir de KVM](#rhel67kvm)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir de KVM](#rhel7xkvm)

[Préparer une machine virtuelle RHEL 6,7 VMware](#rhel67vmware)

[Préparer une machine virtuelle RHEL 7.1/7.2 VMware](#rhel7xvmware)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir d’un fichier](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle chapeau rouge à partir du Gestionnaire Hyper-V
### <a name="prerequisites"></a>Conditions préalables
Cette section suppose que vous avez déjà installé une image RHEL (à partir d’un fichier ISO que vous avez obtenu à partir du site Web de chapeau rouge) sur un disque dur virtuel (disque dur virtuel). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V pour installer une image de système d’exploitation, voir [installer le rôle Hyper-V et configurer une Machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d’installation RHEL**

- Voir également [Général Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Le nouveau format de VHDX n’est pas pris en charge dans Azure. Vous pouvez convertir le disque au format de disque dur virtuel à l’aide de Gestionnaire Hyper-V ou l’applet de commande PowerShell **disque dur virtuel convertir** .

- Disques durs virtuels doivent être créés comme « fixe »--dynamiques disques durs virtuels ne sont pas pris en charge.

- Lorsque vous installez le système Linux, nous vous recommandons d’utiliser des partitions standard plutôt que le Gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. Gestionnaire de volume logique ou [RAID](virtual-machines-linux-configure-raid.md) peut être utilisé sur disques de données si vous préférez.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux pour créer un fichier d’échange sur le disque ressource temporaire. Plus d’informations sur Ceci ne sont disponibles dans les étapes ci-dessous.

- Tous les disques durs virtuels doivent avoir tailles qui sont un multiple de 1 Mo.

- Lorsque vous utilisez **qemu img** pour convertir des images de disque au format de disque dur virtuel, notez qu’il existe un problème connu dans les versions qemu img 2.2.1 ou version ultérieure. Ce problème se traduit par un disque dur virtuel pas correctement mis en forme. Le problème est destiné à être corrigés dans une version à venir de qemu img. Pour l’instant, nous vous conseillons d’utiliser qemu-img version2.2.0 ou une version antérieure.

### <a id="rhel67hyperv"> </a>Préparer une machine virtuelle RHEL 6,7 à partir du Gestionnaire Hyper-V###


1.  Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.  Cliquez sur **se connecter** pour ouvrir une fenêtre de la console pour la machine virtuelle.

3.  Désinstaller NetworkManager en exécutant la commande suivante :

        # sudo rpm -e --nodeps NetworkManager

    Notez que si le package n’est pas déjà installé, cette commande échoue avec un message d’erreur. Il s’agit normalement.

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

6.  Déplacez (ou supprimez) règles udev pour éviter de générer des règles statiques de l’interface Ethernet. Ces règles poser des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

8.  Enregistrez votre abonnement chapeau rouge pour activer l’installation de packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans la version du noyau utilisé par RHEL 6.

    Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.

    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

11. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut. Modifiez /etc/ssh/sshd_config pour inclure la ligne suivante :

        ClientAliveInterval 180

12. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Notez que l’installation du package WALinuxAgent entraîne la suppression du NetworkManager et packages NetworkManager gnome s’ils n’ont pas déjà supprimés comme décrit dans l’étape 2.

13. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.
L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans /etc/waagent.conf correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Annuler l’inscription de l’abonnement (si nécessaire) en exécutant la commande suivante :

        # sudo subscription-manager unregister

15. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Cliquez sur **Action > Arrêter** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.
 

### <a id="rhel7xhyperv"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir du Gestionnaire Hyper-V###

1.  Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.  Cliquez sur **se connecter** pour ouvrir une fenêtre de la console pour la machine virtuelle.

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

5.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6.  Enregistrez votre abonnement chapeau rouge pour activer l’installation de packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez `/etc/default/grub` dans un éditeur de texte et modifier le paramètre **GRUB_CMDLINE_LINUX** . Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.
    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

8.  Une fois que vous avez terminé d’édition `/etc/default/grub`, exécutez la commande suivante pour reconstruire la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut. Modifier `/etc/ssh/sshd_config` pour inclure la ligne suivante :

        ClientAliveInterval 180

10. Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation. L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans `/etc/waagent.conf` correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Si vous souhaitez annuler l’inscription de l’abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

14. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Cliquez sur **Action > Arrêter** dans le Gestionnaire Hyper-V. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Préparer une machine virtuelle rouge chapeau de KVM

### <a id="rhel67kvm"> </a>Préparer une machine virtuelle RHEL 6,7 à partir de KVM###


1.  Télécharger l’image KVM de 6,7 RHEL site Web de rouge chapeau.

2.  Définir un mot de passe racine.

    Générer un mot de passe chiffré, puis copiez le résultat de la commande :

        # openssl passwd -1 changeme

    Définir un mot de passe racine avec guestfish :

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modifier le deuxième champ de l’utilisateur racine de « ! » le mot de passe chiffré.

3.  Créer une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**et définir le modèle d’appareil interface réseau virtuel à **virtio**. Démarrez l’ordinateur virtuel, puis connectez-vous en tant que racine.

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

6.  Déplacer (ou supprimer) les règles udev pour éviter de générer des règles statiques de l’interface Ethernet. Ces règles poser des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # chkconfig network on

8.  Enregistrez votre abonnement chapeau rouge pour activer l’installation de packages à partir du référentiel RHEL en exécutant la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans la version du noyau utilisé par RHEL 6.

    Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.
    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

10. Ajouter des modules Hyper-V dans initramfs :  

    Modifier `/etc/dracut.conf` et ajouter du contenu : add_drivers += « hv_vmbus hv_netvsc hv_storvsc »

    Reconstruire initramfs : # dracut – f - v

11. Désinstaller cloud initialisation :

        # yum remove cloud-init

12. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage :

        # chkconfig sshd on

    Modifiez /etc/ssh/sshd_config afin d’inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

        # service sshd restart

13. Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent
        # chkconfig waagent on

15. L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans **/etc/waagent.conf** correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annuler l’inscription de l’abonnement (si nécessaire) en exécutant la commande suivante :

        # subscription-manager unregister

17. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Arrêter la machine virtuelle dans KVM.

19. Convertir l’image qcow2 au format de disque dur virtuel.
    Tout d’abord convertir l’image au format brut :

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Assurez-vous que la taille de l’image brute est alignée avec 1 Mo. Dans le cas contraire, arrondir la taille de police pour aligner avec 1 Mo : # Mo = $((1024*1024)) taille # = $(qemu img info -f brut--sortie json « rhel-6.7.raw » | \ gawk ' correspond à ($0, / « taille virtuelle » : ([0-9] +), /, val) {imprimer val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convertir le disque brut sur un disque dur virtuel taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir de KVM###


1.  Télécharger l’image KVM de RHEL 7.1 (ou 7.2) depuis le site Web chapeau rouge. Nous allons utiliser RHEL 7.1 comme l’exemple ici.

2.  Définir un mot de passe racine.

    Générer un mot de passe chiffré, puis copiez le résultat de la commande :

        # openssl passwd -1 changeme

    Définir un mot de passe racine avec guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modifier le deuxième champ de l’utilisateur racine de « ! » le mot de passe chiffré.

3.  Créer une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**et définir le modèle d’appareil interface réseau virtuel à **virtio**. Démarrez l’ordinateur virtuel, puis connectez-vous en tant que racine.

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

6.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # chkconfig network on

7.  Enregistrez votre abonnement chapeau rouge pour permettre à l’installation des packages à partir du référentiel RHEL en exécutant la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez `/etc/default/grub` dans un éditeur de texte et modifier le paramètre **GRUB_CMDLINE_LINUX** . Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.
    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

9.  Une fois que vous avez terminé d’édition `/etc/default/grub`, exécutez la commande suivante pour reconstruire la configuration grub :

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Ajouter des modules Hyper-V dans initramfs :

    Modifier `/etc/dracut.conf` et ajouter du contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruire initramfs :

        # dracut –f -v

11. Désinstaller cloud initialisation :

        # yum remove cloud-init

12. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage :

        # systemctl enable sshd

    Modifiez /etc/ssh/sshd_config afin d’inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

        systemctl restart sshd

13. Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent

    Activer le service waagent :

        # systemctl enable waagent.service

15. Ne créez pas d’espace d’échange sur le disque du système d’exploitation. L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans `/etc/waagent.conf` correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Annuler l’inscription de l’abonnement (si nécessaire) en exécutant la commande suivante :

        # subscription-manager unregister

17. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Arrêter la machine virtuelle dans KVM.

19. Convertir l’image qcow2 au format de disque dur virtuel.

    Tout d’abord convertir l’image au format brut :

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Assurez-vous que la taille de l’image brute est alignée avec 1 Mo. Dans le cas contraire, arrondir la taille de police pour aligner avec 1 Mo :

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convertir le disque brut sur un disque dur virtuel taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Préparer une machine virtuelle rouge chapeau VMware
### <a name="prerequisites"></a>Conditions préalables
Cette section suppose que vous avez déjà installé une machine virtuelle RHEL dans VMware. Pour plus d’informations sur l’installation d’un système d’exploitation dans VMware, voir [Guide d’Installation de système d’exploitation invité VMware](http://partnerweb.vmware.com/GOSIG/home.html).

- Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser partitions standard plutôt que le Gestionnaire de volume logique (souvent la valeur par défaut pour la plupart des installations). Cela sera éviter les conflits de noms de gestionnaire de volume logique avec machines virtuelles clonés, particulièrement si un disque du système d’exploitation jamais doit être attaché à un autre ordinateur virtuel pour résoudre les problèmes. Gestionnaire de volume logique ou RAID peut être utilisé sur disques de données si vous préférez.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux pour créer un fichier d’échange sur le disque ressource temporaire. Vous trouverez plus d’informations dans les étapes ci-dessous.

- Lorsque vous créez le disque dur virtuel, sélectionnez **disque virtuel Store dans un seul fichier**.



### <a id="rhel67vmware"> </a>Préparer une machine virtuelle RHEL 6,7 VMware###

1.  Désinstaller NetworkManager en exécutant la commande suivante :

         # sudo rpm -e --nodeps NetworkManager

    Notez que si le package n’est pas déjà installé, cette commande échoue avec un message d’erreur. Il s’agit normalement.

2.  Créez un fichier nommé **réseau** dans/etc/sysconfig/répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Créez un fichier nommé **ifcfg eth0** dans les /etc/sysconfig/network-scripts / répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Déplacer (ou supprimer) les règles udev pour éviter de générer des règles statiques de l’interface Ethernet. Ces règles poser des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-v :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6.  Enregistrez votre abonnement chapeau rouge pour activer l’installation de packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez « / boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut inclut les paramètres suivants :

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Cela va désactiver NUMA en raison d’un bogue dans la version du noyau utilisé par RHEL 6.
    Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.
    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

9.  Ajouter des modules Hyper-V dans initramfs :

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut. Modifier `/etc/ssh/sshd_config` pour inclure la ligne suivante :

        ClientAliveInterval 180

11. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. Ne créez pas d’espace d’échange sur le disque du système d’exploitation :

    L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans `/etc/waagent.conf` correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Annuler l’inscription de l’abonnement (si nécessaire) en exécutant la commande suivante :

        # sudo subscription-manager unregister

14. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Arrêtez la machine virtuelle et convertir le fichier VMDK en fichier .vhd.

    Tout d’abord convertir l’image au format brut :

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Assurez-vous que la taille de l’image brute est alignée avec 1 Mo. Dans le cas contraire, arrondir la taille de police pour aligner avec 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convertir le disque brut sur un disque dur virtuel taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 VMware###

1.  Créez un fichier nommé **réseau** dans/etc/sysconfig/répertoire qui contient le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Créez un fichier nommé **ifcfg eth0** dans les /etc/sysconfig/network-scripts / répertoire qui contient le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Assurez-vous que le service de réseau démarre au démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

4.  Enregistrez votre abonnement chapeau rouge pour activer l’installation de packages à partir du référentiel RHEL en exécutant la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Modifiez la ligne de démarrage noyau dans votre configuration grub pour inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez `/etc/default/grub` dans un éditeur de texte et modifier le paramètre **GRUB_CMDLINE_LINUX** . Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Cela garantit également que tous les messages de la console sont envoyées à la première voie série, qui peut aider à Azure prise en charge avec débogage des problèmes. Outre l’action ci-dessus, nous vous conseillons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Graphique et silencieux démarrage ne sont pas utiles dans un environnement cloud devons tous les journaux soient envoyées au port série.
    L’option crashkernel peut être gauche configuré si vous le souhaitez, mais notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle par 128 Mo ou plus. Cela peut être problématique de plus petite taille de la mémoire virtuelle.

6.  Une fois que vous avez terminé d’édition `/etc/default/grub`, exécutez la commande suivante pour reconstruire la configuration grub :

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Ajouter des modules Hyper-V dans initramfs :

    Modifier `/etc/dracut.conf`, ajouter du contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Reconstruire initramfs :

        # dracut –f -v

8.  Vérifiez que le serveur SSH est installé et configuré pour démarrer lors du démarrage. Il s’agit généralement de la valeur par défaut. Modifier `/etc/ssh/sshd_config` pour inclure la ligne suivante :

        ClientAliveInterval 180

9.  Le package WALinuxAgent `WALinuxAgent-<version>` a été appuyé au référentiel suppléments chapeau rouge. Activer le référentiel suppléments en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Installer l’Agent de Linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. Ne créez pas d’espace d’échange sur le disque du système d’exploitation. L’Agent de Linux Azure peut configurer automatiquement l’espace d’échange à partir du disque local ressource associée à la machine virtuelle une fois que la machine virtuelle est configurée sur Azure. Notez que le disque de ressources local est un disque temporaire et peut être vidé lorsque la machine virtuelle est annulée. Après avoir installé l’Agent de Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans `/etc/waagent.conf` correctement :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Si vous souhaitez annuler l’inscription de l’abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

13. Exécutez les commandes suivantes pour deprovision la machine virtuelle et le préparer pour la mise en service sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Arrêtez la machine virtuelle et convertir le fichier VMDK au format de disque dur virtuel.

    Tout d’abord convertir l’image au format brut :

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Assurez-vous que la taille de l’image brute est alignée avec 1 Mo. Dans le cas contraire, arrondir la taille de police pour aligner avec 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convertir le disque brut sur un disque dur virtuel taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Préparer une machine virtuelle chapeau rouge à partir d’un fichier ISO à l’aide d’un fichier de démarrer automatiquement


### <a id="rhel7xkickstart"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir d’un fichier###


1.  Créer un fichier avec le contenu ci-dessous et enregistrez le fichier. Pour plus d’informations sur l’installation de démarrer, consultez le [Guide d’Installation de démarrer](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Placez le fichier dans un emplacement accessible à partir du système de l’installation.

3.  Dans le Gestionnaire Hyper-V, créez un nouvel ordinateur virtuel. Dans la page **connecter un disque dur virtuel** , sélectionnez **joindre un disque dur virtuel plus tard**, puis exécutez l’Assistant nouveau Machine virtuelle.

4.  Ouvrir les paramètres de mémoire virtuelle :

    un.  Joindre un nouveau disque dur virtuel de la machine virtuelle. Veillez à sélectionner **Format disque dur virtuel** et une **Taille fixe**.

    b.  Joindre l’installation ISO dans le lecteur DVD.

    c.  Définir cela pour démarrer à partir d’un CD.

5.  Démarrer la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur l' **onglet** pour configurer les options de démarrage.

6.  Entrée `inst.ks=<the location of the kickstart file>` à la fin des options de démarrage, appuyez sur **entrée**.

7.  Attendez que l’installation se termine. Lorsqu’elle est terminée, la machine virtuelle se ferme automatiquement. Votre VHD Linux est maintenant prêt à être téléchargé vers Azure.

## <a name="known-issues"></a>Problèmes connus
Il existe des problèmes connus lorsque vous utilisez 7.1 RHEL dans Hyper-V et Azure.

### <a name="disk-io-freeze"></a>Figer des e/s disque

Ce problème peut se produire pendant les activités d’e/s disque stockage fréquents avec RHEL 7.1 dans Hyper-V et Azure.   

Taux de reproduire le problème :

Ce problème est intermittent. Toutefois, il se produit plus fréquemment lors des fréquents opérations e/s dans Hyper-V et Azure.   


[AZURE.NOTE] Ce problème connu a déjà été traité par chapeau rouge. Pour installer les solutions associées, exécutez la commande suivante :

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Le pilote Hyper-V n’a pas pu être inscrites dans le disque RAM initial lorsque vous utilisez un hyperviseur Hyper-V

Dans certains cas, programmes d’installation Linux ne peuvent pas inclure les pilotes pour Hyper-V dans le disque RAM initial (initrd ou initramfs), sauf si elle détecte qu’il s’exécute dans un environnement Hyper-V.

Lorsque vous utilisez un système de virtualisation différentes (c'est-à-dire Virtualbox, Xen, etc.) pour préparer votre image Linux, vous devrez peut-être reconstruire initrd pour vous assurer qu’au moins les modules noyau hv_vmbus et hv_storvsc sont disponibles sur le disque RAM initial. Il s’agit au moins un problème connu sur les systèmes en fonction de la distribution chapeau rouge en amont.

Pour résoudre ce problème, vous devez ajouter les modules Hyper-V dans initramfs et reconstruire :

Modifier `/etc/dracut.conf` et ajouter du contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Reconstruire initramfs :

        # dracut –f -v

Pour plus d’informations, voir les informations sur la [reconstruction initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Red chapeau Enterprise Linux pour créer de nouvelles machines virtuelles dans Azure. S’il s’agit de la première fois que vous vous téléchargez le fichier .vhd vers Azure, consultez les étapes 2 et 3 dans la [Création et le téléchargement d’un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Pour plus d’informations sur les hyperviseurs certifiés sont habilités à exécuter Red chapeau Enterprise Linux, consultez [le site Web chapeau rouge](https://access.redhat.com/certified-hypervisors).
