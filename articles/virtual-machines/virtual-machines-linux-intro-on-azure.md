<properties
    pageTitle="Présentation de Linux dans Azure | Microsoft Azure"
    description="Découvrez comment utiliser des machines virtuelles Linux sur Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introduction à Linux sur Azure

Cette rubrique fournit une vue d’ensemble de certains aspects de l’utilisation de machines virtuelles Linux dans le cloud Azure. Déploiement d’une machine virtuelle Linux est un processus simple à l’aide d’une image à partir de la galerie.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Authentification : Noms d’utilisateur, les mots de passe et clés SSH

Lorsque vous créez une machine virtuelle Linux à l’aide du portail Azure classique, vous êtes invité à fournir un nom d’utilisateur, mot de passe ou un code public. Le choix d’un nom d’utilisateur pour le déploiement d’une machine virtuelle Linux sur Azure est couvertes par la contrainte suivante : les noms des comptes système (UID < 100) déjà présents dans la machine virtuelle ne sont pas autorisés, « root » par exemple.


 - Voir [créer une Machine virtuelle Linux en cours d’exécution](virtual-machines-linux-quick-create-cli.md)
 - Découvrez [comment utiliser SSH avec Linux sur Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Obtention des privilèges super utilisateur à l’aide`sudo`

Le compte d’utilisateur spécifié pendant le déploiement d’instance machine virtuelle sur Azure est un compte privilégié. Ce compte est configuré par l’Agent de Linux Azure puissent élever privilèges à racine (compte Super utilisateur) à l’aide du `sudo` utilitaire. Une fois connecté à l’aide de ce compte d’utilisateur, vous ne pourrez pas exécuter de commandes en tant que racine à l’aide de la syntaxe de la commande

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un shell racine à l’aide de **sudo -d**.

- Consultez [des privilèges racine à l’aide sur les machines virtuelles de Linux dans Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Configuration du pare-feu

Azure fournit un filtre de paquets entrants qui limite la connectivité à ports spécifiés dans le portail classique Azure. Par défaut, le seul port autorisé est SSH. Vous pouvez ouvrir l’accès à des ports supplémentaires sur votre machine virtuelle Linux en configurant des points de terminaison dans le portail classique Azure :

 - Voir : [comment configurer une extrémité vers une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md)

Les images Linux dans la galerie Azure n’activez pas le pare-feu *iptables* par défaut. Si vous le souhaitez, le pare-feu peut être configuré pour fournir un filtrage supplémentaire.


## <a name="hostname-changes"></a>Modifications de nom d’hôte

Lorsque vous déployez initialement une instance d’une image Linux, vous devez fournir un nom d’hôte de la machine virtuelle. Une fois que la machine virtuelle est en cours d’exécution, ce nom d’hôte est publié sur les serveurs DNS plateforme afin que plusieurs machines virtuelles connectés entre eux puisse effectuer des recherches d’adresses IP à l’aide de noms d’hôte.

Si les modifications de nom d’hôte sont souhaitées après qu’une machine virtuelle a été déployée, utilisez la commande

    # sudo hostname <newname>

L’Agent de Linux Azure inclut des fonctionnalités pour détecter automatiquement ce changement de nom et configurer la machine virtuelle permettant de conserver cette modification et publier cette modification vers les serveurs DNS plateforme de manière appropriée.

 - [Guide de l’utilisateur Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Cloud initialisation
Images **Ubuntu** et **CoreOS** utilisent cloud-initialisation sur Azure, qui fournit des fonctionnalités supplémentaires pour démarrer une machine virtuelle.

 - [Comment faire pour injection de données personnalisée](virtual-machines-windows-classic-inject-custom-data.md)
 - [Données personnalisées et Cloud-initialisation sur Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Créer des Partitions permutation Azure à l’aide de Cloud initialisation](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Comment utiliser CoreOS sur Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Capture de l’Image Machine virtuelle

Azure permet de capturer l’état d’un ordinateur virtuel existant dans une image qui peut ensuite être utilisée pour déployer des instances machine virtuelle supplémentaires. L’Agent de Linux Azure peut être utilisée pour restaurer partie de la personnalisation qui a été effectuée pendant le processus de mise en service. Vous pouvez suivre les étapes ci-dessous pour capturer une machine virtuelle en tant qu’image :

1. Exécuter **waagent-annulation** pour annuler la mise en service de personnalisation. Ou **waagent-annulation + utilisateur** vous pouvez également supprimer le compte d’utilisateur spécifié lors de l’approvisionnement et toutes les données associées.

2. Arrêter vers le bas/power la machine virtuelle.

3. Cliquez sur *Capture* dans le portail classique Azure ou utilisez les outils de Powershell ou infrastructure du langage commun pour capturer la machine virtuelle en tant qu’image.

 - Voir : [Comment faire pour capturer une Machine virtuelle Linux à utiliser en tant que modèle](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Joindre des disques

Chaque machine virtuelle temporaire, local est *disque de la ressource* joint. Étant donné que les données sur un disque de la ressource peuvent ne pas être résistants réinitialisations, il est souvent utilisé par les applications et processus qui s’exécutent de la machine virtuelle transitoires et **temporaire** espace de stockage de données. Il est également utilisé pour stocker la page ou échanger des fichiers pour le système d’exploitation.

Sous Linux, le disque de la ressource est généralement géré par l’Agent de Linux Azure et montage automatique **/mnt/resource** (ou **/mnt** sur les images Ubuntu).


>[AZURE.NOTE] Notez que le disque de la ressource est un disque **temporaire** et peut-être être supprimé et reformaté lors du redémarrage de la machine virtuelle.

Sous Linux le disque de données peut être nommé par le noyau en tant que `/dev/sdc`, et les utilisateurs devront partition, mettre en forme et monter cette ressource. Cette procédure est décrite pas à pas dans le didacticiel : [l’insertion d’un disque de données pour une Machine virtuelle](virtual-machines-linux-classic-attach-disk.md).

 - **Voir aussi :** [Configurer les logiciels RAID sur Linux](virtual-machines-linux-configure-raid.md)  &  [Configurer le Gestionnaire de volume logique sur un ordinateur virtuel Linux dans Azure](virtual-machines-linux-configure-lvm.md)

