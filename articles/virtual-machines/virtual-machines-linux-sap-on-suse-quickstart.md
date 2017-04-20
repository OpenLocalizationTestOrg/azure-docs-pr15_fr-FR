<properties
   pageTitle="Test NetWeaver SAP sur Microsoft Azure SUSE Linux machines virtuelles | Microsoft Azure"
   description="Test NetWeaver SAP sur Microsoft Azure SUSE Linux machines virtuelles"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>En cours d’exécution NetWeaver SAP sur Microsoft Azure SUSE Linux machines virtuelles


Cet article décrit les différents éléments à prendre en compte lorsque vous exécutez SAP NetWeaver sur machines virtuelles Microsoft Azure SUSE Linux (machines virtuelles). À partir de mai 19 2016 SAP NetWeaver est embauche pris en charge sur machines virtuelles de Linux SUSE sur Azure. Tous les détails concernant les versions de Linux, versions du noyau SAP et ainsi de suite, vous pouvez trouver dans SAP Note 1928533 « Applications SAP sur Azure : produits pris en charge et les types de machine virtuelle Azure ».
Documentation supplémentaire sur SAP sur machines virtuelles Linux sont accessibles ici : [SAP à l’aide sur les machines virtuelles Linux (machines virtuelles)](virtual-machines-linux-sap-get-started.md).


Les informations suivantes devraient vous aider à éviter certains pièges potentiels.

## <a name="suse-images-on-azure-for-running-sap"></a>Images SUSE sur Azure pour l’exécution de SAP

Pour exécuter SAP NetWeaver sur Azure, utilisez uniquement SUSE Linux Enterprise Server SLES 12 (SPx) - Voir également la note SAP 1928533. Une image SUSE particulière est sur le marché Azure (« SLES 11 SP3 pour la licence d’accès client SAP »), mais cela n’est pas prévu pour l’utilisation générale. N’utilisez pas cette image car celle-ci est réservée pour la solution [SAP Cloud matériel bibliothèque](https://cal.sap.com/) .  

Vous devez utiliser le Gestionnaire de ressources Azure pour tous les nouveaux tests et installations sur Azure. Pour rechercher des images SUSE SLES et les versions à l’aide de PowerShell Azure ou l’interface de ligne Azure (commande), utilisez les commandes suivantes. Vous pouvez ensuite utiliser la sortie, par exemple, pour définir l’image du système d’exploitation dans un modèle de JSON pour le déploiement d’une nouvelle machine virtuelle SUSE Linux.
Ces commandes PowerShell sont valides pour Azure PowerShell version 1.0.1 et versions ultérieures.

* Recherchez éditeurs existants, y compris SUSE :

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Recherchez les offres existantes de SUSE :

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Recherchez les offres SUSE SLES :

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Recherchez une version spécifique d’un SKU SLES :

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Installation de WALinuxAgent dans une machine virtuelle SUSE

L’agent de nommé WALinuxAgent fait partie des images SLES sur le marché Azure. Pour plus d’informations sur l’installation manuellement (par exemple, lorsque vous téléchargez un disque dur virtuel SLES OS (disque dur virtuel) locales), voir :

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtuel-machines-linux-approuvée-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP « Amélioration de la surveillance »

SAP « Amélioration de la surveillance » n’est obligatoire requis pour exécuter SAP sur Azure. Vérifiez les détails dans SAP Notez 2191498 « SAP sur Linux avec Azure : amélioré Monitoring ».

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Connexion de données Azure disques à une machine virtuelle Linux Azure

Vous ne devez jamais disques de données Azure montage pour une machine virtuelle Linux Azure à l’aide de l’ID du périphérique. À la place, utilisez l’identificateur unique universel (UUID). Soyez prudent lorsque vous utilisez des outils graphiques aux disques de données Azure montage, par exemple. Vérifiez les entrées dans/etc/fstab.

Le problème de l’ID de l’appareil est qu’il peut changer, et puis la machine virtuelle Azure peut se bloquer dans le processus de démarrage. Pour atténuer le problème, vous pouvez ajouter le paramètre nofail dans/etc/fstab. Mais, en veillant à n’avec nofail car applications peuvent utiliser le point de montage comme avant et peuvent écrire dans le système de fichiers racine en cas d’un disque de données Azure externes n’a pas été installé lors du démarrage.

La seule exception à montage via UUID est en joignant un disque du système d’exploitation pour résoudre ce problème, comme décrit dans la section suivante.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Résolution des problèmes d’un VM SUSE qui n’est pas plus accessible

Il peut arriver où une VM SUSE sur Azure se bloque dans le processus de démarrage (par exemple, avec une erreur liée à montage disques). Vous pouvez vérifier ce problème à l’aide de la fonctionnalité diagnostics démarrage pour Machines virtuelles Azure v2 dans le portail Azure. Pour plus d’informations, voir [diagnostics démarrage] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Pour résoudre le problème consiste à joindre le disque du système d’exploitation à partir de la machine virtuelle endommagée à un autre SUSE VM sur Azure. Puis apportez les modifications appropriées, tels que/etc/fstab la modification ou suppression des règles d’udev réseau, comme décrit dans la section suivante.

Il est important de prendre en compte. Déploiement de plusieurs SUSE machines virtuelles à partir de la même image Azure Marketplace (par exemple, SLES 11 Service Pack 4) provoque le disque du système d’exploitation à toujours monter par le même UUID. Par conséquent, à l’aide de l’UUID pour joindre un disque du système d’exploitation à partir d’un autre ordinateur virtuel qui a été déployé à l’aide de la même image Azure Marketplace entraînera deux UUID identiques. Ceci pose problème et signifie que la machine virtuelle destinée à la résolution des problèmes en fait démarre à partir du disque du système d’exploitation attaché et endommagé au lieu de celui d’origine.

Il existe deux façons d’éviter cela :

* Utiliser une autre image Azure Marketplace pour la résolution des problèmes machine virtuelle (par exemple, SLES 11 SPx au lieu de SLES 12).
* Ne joindre le disque du système d’exploitation endommagé à partir d’un autre ordinateur virtuel à l’aide de UUID--utilisation autre chose.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Télécharger une machine virtuelle SUSE locales vers Azure

Pour obtenir une description de la procédure pour télécharger un VM SUSE locales vers Azure, voir [préparer une machine virtuelle SLES ou openSUSE pour Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Si vous voulez télécharger une machine virtuelle sans l’étape annulation à la fin (par exemple, pour conserver une installation SAP existante, ainsi que le nom d’hôte), vérifiez les points suivants :

* Vérifiez que le disque du système d’exploitation est installé à l’aide de UUID et pas l’ID du périphérique. Modification et UUID seulement dans/etc/fstab n’est pas suffisamment pour le disque du système d’exploitation. En outre, n’oubliez pas d’adapter le chargeur de démarrage par YaST ou en modifiant /boot/grub/menu.lst.
* Si vous utilisez le format VHDX pour le disque du système d’exploitation SUSE et convertissez en disque dur virtuel pour le téléchargement vers Azure, il est probable que le périphérique réseau passent d’eth0 et eth1. Pour éviter les problèmes lorsque vous êtes démarrage sur Azure ultérieurement, reconvertir en eth0 comme décrit dans [résolution eth0 dans cloné SLES 11 VMware] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

En plus de ce qui est décrit dans l’article, nous vous conseillons de supprimer ceci :

   /lib/udev/rules.d/75-persistent-NET-Generator.Rules

Vous pouvez également installer l’Agent de Linux Azure (waagent) pour vous aider à éviter des problèmes potentiels, dans la mesure où il n’existe pas plusieurs cartes réseau.

## <a name="deploying-a-suse-vm-on-azure"></a>Déploiement d’une machine virtuelle SUSE sur Azure

Vous devez créer de nouvelles SUSE machines virtuelles à l’aide de fichiers de modèle JSON dans le nouveau modèle Azure le Gestionnaire de ressources. Une fois le fichier de modèle JSON est créé, vous pouvez déployer la machine virtuelle à l’aide de la commande suivante infrastructure du langage commun comme alternative à PowerShell :

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Pour plus d’informations sur les fichiers de modèle JSON, voir [modèles de création de gestionnaire de ressources Azure] (ressource-groupe-création-templates.md) et [modèles de démarrage rapide Azure] (https://azure.microsoft.com/documentation/templates/).

Pour plus d’informations sur l’infrastructure du langage commun et le Gestionnaire de ressources Azure, voir [utiliser l’infrastructure du langage commun Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure] (xplat-infrastructure du langage commun-azure-ressource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Clé de licence et le matériel SAP

Pour obtenir la certification SAP Azure officielle, un nouveau mécanisme a été introduit pour calculer la clé matérielle SAP qui est utilisée pour la licence SAP. Le noyau SAP devait s’adapter aux recours à cette. Les versions du noyau SAP anciennes pour Linux ne comprennent pas cette modification de code. Par conséquent, dans certains cas (par exemple, machine virtuelle Azure redimensionnement), modification de la clé de matériel SAP et la licence SAP a été ne soit plus valide. Ce problème est résolu dans les dernières noyaux Linux SAP. Pour plus d’informations, consultez note SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Package de sapconf SUSE / membres responsables autorisés réglé

SUSE fournit un package appelé « sapconf » qui gère un jeu de paramètres spécifiques à SAP. Pour plus d’informations sur le rôle de ce package et comment installer et utiliser, reportez-vous à [à l’aide de sapconf pour préparer une SUSE Linux Enterprise Server pour exécuter les systèmes SAP] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) et [Nouveautés sapconf ou comment préparer un SUSE Linux Enterprise Server pour l’exécution des systèmes SAP ?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

En attendant, il existe un nouvel outil qui remplace sapconf - réglé-adm. Une trouverez plus d’informations sur cet outil suivant les deux liens ci-dessous.

Documentation SLES sur membres responsables autorisés réglé profil sap-hana sont accessibles [ici](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Systèmes de réglage pour les charges de travail SAP avec réglé membres responsables autorisés - sont accessibles [ici](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) dans chapitre 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>Dans les installations SAP distribuées de partage NFS

Si vous disposez d’une installation distribuée--par exemple, où vous voulez installer la base de données et les serveurs d’applications SAP dans machines virtuelles séparées--vous pouvez partager le répertoire /sapmnt via un réseau NFS (File System). Si vous rencontrez des problèmes avec les étapes d’installation après avoir créé le partage NFS pour /sapmnt, vérifiez si « no_root_squash » est défini pour le partage.

## <a name="logical-volumes"></a>Volumes logiques

Dans le passé une au besoin un grand volume logique sur plusieurs disques de données Azure (par exemple, pour la base de données SAP), il a été recommandé d’utiliser mdadm comme gestionnaire de volume logique n’a pas été entièrement validé encore sur Azure. Pour apprendre à configurer RAID Linux sur Azure à l’aide de mdadm, voir [configurer des logiciels RAID sur Linux](virtual-machines-linux-configure-raid.md). Dans l’intervalle à partir du début de mai 2016 également Gestionnaire de volume logique est entièrement prises en charge sur Azure et peut être utilisé comme alternative à mdadm. Pour plus d’informations sur le Gestionnaire de volume logique sur Azure consultez [Configurer Gestionnaire de volume logique sur un ordinateur virtuel Linux dans Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Référentiel SUSE Azure

Si vous avez un problème avec l’accès au référentiel Azure SUSE standard, vous pouvez utiliser une commande simple pour le réinitialiser. Cela peut se produire si vous créez une image du système d’exploitation privée dans une zone Azure, puis copiez l’image à une zone différente dans laquelle vous souhaitez déployer nouvelles machines virtuelles basés sur cette image du système d’exploitation privée. Il suffit d’exécuter la commande suivante à l’intérieur de la machine virtuelle :

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Bureau GNOME

Si vous voulez utiliser l’application de bureau Gnome pour installer un système de démonstration SAP complet à l’intérieur d’une machine virtuelle unique, y compris une interface utilisateur SAP, navigateur et SAP management console--utilisent cette option pour l’installer sur les images SLES Azure :

   Pour SLES 11 :

   ```
   zypper in -t pattern gnome
   ```

   Pour SLES 12 :

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Prise en charge SAP pour Oracle sous Linux dans le cloud

Il existe une restriction de prise en charge d’Oracle sous Linux dans les environnements virtualisés. Bien qu’il s’agit pas d’une rubrique Azure spécifique, il est important de comprendre. SAP ne prend pas en charge Oracle sur SUSE ou chapeau rouge dans un nuage public tel que Azure. Pour traiter ce sujet, contactez Oracle directement.
