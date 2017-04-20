<properties
    pageTitle="Optimisation de votre machine virtuelle Linux sur Azure | Microsoft Azure"
    description="Découvrez des conseils d’optimisation pour vous assurer que vous avez configuré votre VM Linux pour des performances optimales sur Azure"
    keywords="machine virtuelle Linux, machine virtuelle linux, machine virtuelle ubuntu" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Optimiser votre machine virtuelle Linux sur Azure

Créer un Linux machine virtuelle () est facile d’y parvenir à partir de la ligne de commande ou à partir du portail. Ce didacticiel montre comment s’assurer que vous l’avez configuré pour optimiser ses performances sur la plate-forme Microsoft Azure. Cette rubrique utilise une machine virtuelle Server Ubuntu, mais vous pouvez également créer machine virtuelle Linux à l’aide de [vos propres images en tant que modèles](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Conditions préalables

Cette rubrique suppose que vous avez un abonnement Azure ([un abonnement d’essai gratuit](https://azure.microsoft.com/pricing/free-trial/)), [installé l’infrastructure du langage commun Azure](../xplat-cli-install.md) qui fonctionne déjà et avez déjà déployé une machine virtuelle dans votre abonnement Azure. Avant de faire quoi que ce soit avec Azure - vous s’authentifier à votre abonnement. Pour effectuer cette opération avec Azure infrastructure du langage commun, il suffit de taper `azure login` pour démarrer le processus interactif. 

## <a name="azure-os-disk"></a>Azure disque du système d’exploitation

Une fois que vous créez un Linux Vm dans Azure, il comporte deux disques associés. / dev/sda est votre disque du système d’exploitation, /dev/sdb votre disque temporaire.  N’utilisez pas le disque du système d’exploitation principal (sda/développement /) pour n’est pas le système d’exploitation telle qu’elle est optimisée pour les temps de démarrage rapide machine virtuelle et n’offre pas de bonnes performances pour vos charges de travail. Vous voulez joindre un ou plusieurs disques à votre ordinateur virtuel pour obtenir permanente et optimisée de stockage pour vos données. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Ajout de disques pour cibles taille et performances 

Selon la taille de la mémoire virtuelle, vous pouvez joindre jusqu'à 16 disques supplémentaires dans une série de A, 32 disques sur une série D et machine 64 disques sur une série G - chaque jusqu'à 1 to taille. Vous ajoutez des disques supplémentaires selon vos besoins par votre espace et la configuration requise des sorties par. Chaque disque possède un objectif de performances des sorties 500 par pour le stockage Standard et jusqu'à 5000 disque par seconde pour le stockage Premium.  Pour plus d’informations sur disques de stockage Premium, reportez-vous à [stockage Premium : stockage High Performance pour les machines virtuelles Azure](../storage/storage-premium-storage.md)

Pour obtenir la plus élevée ES/s sur disques de stockage Premium où leurs paramètres de cache ont été définies « En lecture seule » ou « Aucune », vous devez désactiver « obstacles » lors du montage le système de fichiers dans Linux. Vous n’avez pas besoin d’obstacles écritures disques de stockage Premium sauvegardée étant résistants pour ces paramètres de cache.

- Si vous utilisez **reiserFS**, désactivez les obstacles à l’aide de l’option de montage « barrière = none » (pour l’activation obstacles, utilisez « barrière = aligné à »)
- Si vous utilisez **ext3/ext4**, désactivez les obstacles à l’aide de l’option de montage « barrière = 0 » (pour l’activation obstacles, utilisez « barrière = 1 »)
- Si vous utilisez **XFS**, désactivez les obstacles à l’aide de l’option de montage « nobarrier » (pour l’activation obstacles, utilisez l’option « barrière »)

## <a name="storage-account-considerations"></a>Considérations relatives au compte de stockage

Lorsque vous créez votre Linux VM dans Azure, assurez-vous que vous joignez disques à partir de comptes de stockage résidant dans la même région en tant que votre ordinateur virtuel afin de garantir proximité et réduire la latence du réseau.  Chaque compte de stockage Standard peut contenir un maximum de 20 k sorties par et une capacité de taille de 500 To.  Cette procédure fonctionne à environ 40 disques très utilisés, notamment le disque du système d’exploitation et des disques de données que vous créez. Pour les comptes de stockage Premium, il n’existe aucune limite maximale sorties par mais il existe une limite de taille 32 To. 

Une fois en matière de gestion haute sorties par les charges de travail et que vous avez choisi Standard de stockage pour vos disques, vous devrez peut-être fractionner les disques entre plusieurs comptes de stockage pour vous assurer que vous n’avez pas atteint la limite de sorties par 20 000 pour les comptes de stockage Standard. Votre ordinateur virtuel peut contenir un mélange de disques de dans l’ensemble de stockage différents comptes et types de compte de stockage pour atteindre votre configuration optimale. 

## <a name="your-vm-temporary-drive"></a>Votre lecteur machine virtuelle temporaire

Par défaut lorsque vous créez une machine virtuelle, Azure vous offre un disque du système d’exploitation (/ développement/sda) et un disque temporaire (/ développement/sdb).  Tous les disques supplémentaires que vous ajoutez s’affichent sous /dev/sdc, /dev/sdd, /dev/sde et ainsi de suite. Toutes les données sur votre disque temporaire (/ développement/sdb) ne sont pas permanentes et peuvent être perdues si des événements spécifiques, comme la machine virtuelle redimensionnement, redéploiement, ou maintenance force un redémarrage de votre ordinateur virtuel.  La taille et le type de votre disque temporaire est liée à la taille de la mémoire virtuelle que vous avez choisi lors du déploiement. Dans le cas de la taille premium machines virtuelles (série DS, G et DS_V2) le lecteur temporaire est sauvegardé par un SSD local pour augmenter les performances de jusqu'à 48 k sorties par. 

## <a name="linux-swap-file"></a>Fichier d’échange Linux

Si votre ordinateur virtuel Azure provient d’une image Ubuntu ou CoreOS, vous pouvez utiliser CustomData pour envoyer une configuration cloud à cloud initialisation. Si vous avez [téléchargé une image Linux personnalisée](virtual-machines-linux-upload-vhd.md) qui utilise l’initialisation de cloud, vous également configurez partitions permutation à l’aide de cloud initialisation.

Sous Ubuntu Cloud Images, vous devez utiliser cloud initialisation pour configurer la partition d’échange. Consultez la page suivante Wiki Ubuntu pour plus d’informations : [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Pour les images sans prise en charge de l’initialisation de cloud, images machine virtuelle déployés à partir de la Azure Marketplace ont un Linux Agent machine virtuelle intégré avec le système d’exploitation. Cet agent permet de la machine virtuelle pour interagir avec les différents services Azure. En supposant que vous avez déployé une image standard à partir de la Azure Marketplace, vous devez procéder comme suit pour configurer correctement vos paramètres de fichier d’échange Linux :

Localiser et modifier des deux entrées dans le fichier **/etc/waagent.conf** . Ils déterminent l’existence d’un fichier d’échange dédié et la taille du fichier. Les paramètres que vous cherchez à modifier sont `ResourceDisk.EnableSwap=N` et`ResourceDisk.SwapSizeMB=0` 

Vous devez les modifier à ce qui suit :

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size en Mo à vos besoins} 

Une fois que vous avez effectué vos modifications, vous devrez redémarrez le waagent ou votre VM Linux pour refléter ces modifications.  Vous connaissez les modifications qui ont été implémentées et un fichier d’échange a été créé lorsque vous utilisez la `free` commande pour afficher l’espace disponible. L’exemple ci-dessous comporte un fichier d’échange de 512 Mo créé lors de la modification du fichier waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algorithme de planification e/s pour le stockage Premium

Avec la 2.6.18 noyau Linux, la valeur par défaut e/s planification algorithme a été modifié à partir de la date d’échéance en CFQ (complètement juste algorithme files d’attente). Pour obtenir les modèles vive e/s, il est négligeable dans les différences de performances entre CFQ et date d’échéance.  Pour les disques SSD où le modèle d’e/s disque est principalement séquentiel, passer à l’algorithme NOOP ou échéance pouvez obtenir de meilleures performances e/s.

### <a name="view-the-current-io-scheduler"></a>Afficher le planificateur d’e/s en cours

Utilisez la commande suivante :  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Vous verrez la suite de sortie, qui indique le planificateur actuel.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Modifier le périphérique actuel (/ développement/sda) des e/s algorithme de planification

Utilisez les commandes suivantes :  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Ce paramètre pour/dev/sda seul n’est pas utile. Il doit être définie sur tous les disques de données où e/s séquentielles domine le modèle e/s.  

Vous devriez voir le résultat suivant, indiquant que grub.cfg a été reconstruit avec succès et que le planificateur par défaut a été mis à jour pour NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Pour la famille de distribution Redhat, vous devez uniquement la commande suivante :   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>À l’aide de RAID logiciel pour obtenir une valeur supérieure, j’ai / opérations

Si vos charges de travail requièrent des sorties par autres que celles d’un disque, vous devez utiliser une configuration RAID logiciel de plusieurs disques. Étant donné que Azure effectue déjà résilience disque au niveau de la couche TISSU local, vous atteignez le niveau le plus élevé de performance à partir d’une configuration de répartition RAID-0.  Vous devez mettre en service et créer des disques dans l’environnement Azure et associez-les à votre VM Linux avant partition, mise en forme et les lecteurs de montage.  Vous trouverez plus d’informations sur la configuration d’une configuration RAID logiciel sur vos VM Linux dans azure dans le document **[Configuration logicielle RAID sous Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Étapes suivantes

N’oubliez pas, avec toutes les discussions d’optimisation, vous devez effectuer des tests avant et après chaque modification apportée à mesurer l’impact de la modification.  Optimisation est un processus détaillé ayant des résultats différents sur différentes machines dans votre environnement.  Ce qui fonctionne pour une configuration ne fonctionne pas pour d’autres personnes.

Certains liens utiles vers des ressources supplémentaires : 

- [: Premium High Performance de stockage pour les charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md)
- [Guide de l’utilisateur Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)
- [Optimisation des performances MySQL sur machines virtuelles Linux Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurer les logiciels RAID sur Linux](virtual-machines-linux-configure-raid.md)
