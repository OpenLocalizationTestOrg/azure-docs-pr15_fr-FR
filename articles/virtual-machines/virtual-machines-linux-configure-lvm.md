<properties 
    pageTitle="Configurer le Gestionnaire de volume logique sur une machine virtuelle exécutant Linux | Microsoft Azure" 
    description="Découvrez comment configurer le Gestionnaire de volume logique sur Linux dans Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurer le Gestionnaire de volume logique sur un ordinateur virtuel Linux dans Azure

Ce document vous explique comment configurer logique Volume Manager (Gestionnaire de volume logique) sur votre ordinateur virtuel Azure. Bien qu’il soit possible de configurer le Gestionnaire de volume logique sur n’importe quel disque lié à la machine virtuelle, par défaut la plupart des images cloud n’ont pas Gestionnaire de volume logique configuré sur le disque du système d’exploitation. Il s’agit pour éviter les problèmes avec les groupes de volumes en double si le disque du système d’exploitation soit jointe à une autre machine virtuelle de la même distribution et type, c'est-à-dire lors d’un scénario de récupération. Par conséquent, il est recommandé qu’ils utilisent le Gestionnaire de volume logique sur les disques de données.


## <a name="linear-vs-striped-logical-volumes"></a>Linéaire et rayées volumes logiques

Gestionnaire de volume logique peut servir à combiner un certain nombre de disques physiques en un seul volume de stockage. Par défaut Gestionnaire de volume logique crée généralement linéaires volumes logiques, ce qui signifie que le stockage physique concaténé ensemble. Dans ce cas en lecture/écriture opérations généralement uniquement recevront un seul disque. En revanche, nous pouvons également créer des volumes logiques rayées où lecture et en écriture est répartis sur plusieurs disques contenues dans le groupe de volumes (c'est-à-dire similaire à RAID0). Pour des performances optimales, qu'il est probable que vous souhaiterez répartir vos volumes logiques afin que lecture et en écriture utilise tous vos disques données attachées.

Décrit comment combiner plusieurs disques de données dans un groupe de volume unique, puis créez un volume logique dans ce document. Les étapes suivantes sont un peu généralisées pour fonctionner avec la plupart des versions. Dans la plupart des cas utilitaires et des flux de travail pour la gestion du Gestionnaire de volume logique sur Azure n’est pas fondamentalement différent des autres environnements. Comme d’habitude, Veuillez également consulter votre fournisseur Linux pour la documentation et recommandations d’utilisation du Gestionnaire de volume logique avec votre distribution particulier.


## <a name="attaching-data-disks"></a>Joindre des disques de données
Une souhaiteront démarrer avec deux ou plusieurs disques de données vide lorsque vous utilisez le Gestionnaire de volume logique. Selon vos besoins IO, vous pouvez choisir de joindre des disques qui sont stockés dans notre stockage Standard, avec jusqu'à 500 IO/ps par disque ou notre stockage Premium avec jusqu'à 5 000 IO/ps par disque. Cet article accède pas dans les détails sur la mise en service et joindre des disques de données pour une machine virtuelle Linux. Consultez la Microsoft Azure article [joindre un disque](virtual-machines-linux-add-disk.md) pour obtenir des instructions détaillées sur l’insertion d’un disque de données vide pour une machine virtuelle Linux sur Azure.

## <a name="install-the-lvm-utilities"></a>Installer les utilitaires de gestionnaire de volume logique

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS et Linux Oracle**

        # sudo yum install lvm2

- **SLES 12 et openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    Sur SLES11, vous devez également modifier /etc/sysconfig/lvm et définir `LVM_ACTIVATED_ON_DISCOVERED` « activer » :

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurer le Gestionnaire de volume logique
Dans ce guide suppose que vous avez attachée trois disques de données, ce qui nous allons faire en tant que `/dev/sdc`, `/dev/sdd` et `/dev/sde`. Notez qu’il peut s’agir pas toujours les mêmes noms de chemin d’accès de votre ordinateur virtuel. Vous pouvez exécuter '`sudo fdisk -l`' ou commande similaire à votre disques disponibles de la liste.

1. Préparer les volumes physiques :

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Créer un groupe de volumes. Dans cet exemple, nous appelons le volume groupe « données-vg01 » :

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Créer l’ou les volumes logique. La commande ci-dessous, nous crée un volume logique appelé « données-lv01 » pour couvrir le groupe volume entier, mais notez qu’il est également possible de créer plusieurs volumes logiques dans le groupe de volume.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Mettre en forme le volume logique

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Avec l’option utiliser SLES11 «-t ext3 » au lieu d’ext4. SLES11 prend uniquement en charge l’accès en lecture seule pour les systèmes de fichiers ext4.


## <a name="add-the-new-file-system-to-etcfstab"></a>Ajouter le nouveau système de fichiers à/etc/fstab

**Faire preuve de prudence :** Incorrectement modification du fichier/etc/fstab peut entraîner un système initialisé. Cas de doute, reportez-vous à la documentation de la distribution pour plus d’informations sur la façon de modifier correctement ce fichier. Il est également recommandé qu’une sauvegarde du fichier/etc/fstab est créée avant de le modifier.

1. Créer le point de montage souhaitée pour votre nouveau système de fichiers, par exemple :

        # sudo mkdir /data


2. Recherchez le chemin d’accès du volume logique

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Ouvrez/etc/fstab dans un éditeur de texte et ajouter une entrée pour le nouveau système de fichiers, par exemple :

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Ensuite, enregistrez et fermez/etc/fstab.


4. Vérifier que l’entrée/etc/fstab est correcte :

        # sudo mount -a

    Si cette commande se traduit par un message d’erreur, veuillez vérifier la syntaxe dans le fichier/etc/fstab.

    Ensuite, exécuter la `mount` commande pour garantir le système de fichiers est chargé :

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Facultatif) Paramètres de démarrage de secours dans/etc/fstab

    Nombre incluent soit la `nobootwait` ou `nofail` monter paramètres qui peuvent être ajoutés au fichier/etc/fstab. Ces paramètres permettent échecs lors du montage d’un système de fichiers particulier et permettant au système Linux continue à démarrer même si elle ne peut pas correctement monter le système de fichiers RAID. Reportez-vous à la documentation de votre distribution pour plus d’informations sur ces paramètres.

    Exemple (Ubuntu) :

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
