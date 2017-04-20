<properties 
    pageTitle="Guide de l’utilisateur Linux Agent | Microsoft Azure" 
    description="Découvrez comment installer et configurer Linux Agent (waagent) pour gérer l’interaction de votre machine virtuelle avec contrôleur de tissu Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guide de l’utilisateur Azure Linux Agent

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduction

L’Agent de Linux Microsoft Azure (waagent) gère Linux & FreeBSD mise en service et interaction machine virtuelle avec contrôleur de tissu Azure. Il fournit les fonctionnalités suivantes pour Linux et FreeBSD IaaS déploiements :

> [AZURE.NOTE] Consultez l’agent Azure Linux [Lisezmoi](https://github.com/Azure/WALinuxAgent/blob/master/README.md) pour plus d’informations.

* **Image de la mise en service**
  - Création d’un compte d’utilisateur
  - Configuration des types d’authentifications SSH
  - Déploiement des clés publiques SSH et des paires de clés
  - Définir le nom d’hôte
  - Publier le nom d’hôte sur la plateforme DNS
  - Création de rapports empreinte clé SSH hôte vers la plateforme
  - Gestion des ressources de disque
  - Mise en forme et le disque de ressources de montage
  - Configuration de l’espace de permutation

* **Mise en réseau**
  - Gère les itinéraires pour améliorer la compatibilité avec les serveurs DHCP plateforme
  - Garantit la stabilité du nom de l’interface réseau

* **Noyau**
  - Configure virtuel NUMA (désactiver pour noyau < 2.6.37)
  - Consomme entropie Hyper-V pour/dev/Random
  - Configure des délais d’expiration SCSI pour le périphérique racine (qui pourrait être distant)

* **Diagnostics**
  - Redirection de la console sur le port série

* **Déploiements SCVMM**
    - Détecte et amorce l’agent VMM pour Linux lors de l’exécution dans un environnement de System Center Machine virtuelle Manager 2012 R2

* **Extension de la mémoire virtuelle**
    - Injection de composant créé par Microsoft et partenaires dans Linux VM (IaaS) pour activer le logiciel et configuration automatisation
    - Implémentation de référence Extension machine virtuelle sur [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Communication
Le flux d’informations à partir de la plateforme à l’agent se produit via les deux canaux :

* Un démarrage associés DVD pour les déploiements IaaS. Ce DVD comprend un fichier de configuration compatibles OVF qui inclut toutes les informations de mise en service autre que les paires de clés SSH réel.
* Un point de terminaison TCP exposant une API REST permet d’obtenir le déploiement et la configuration de la topologie.


## <a name="requirements"></a>Configuration requise
Les systèmes suivants ont été testés et connus pour fonctionner avec l’Agent de Linux Azure :

> [AZURE.NOTE] Cette liste peut-être différer de la liste officielle des systèmes pris en charge sur la plate-forme Microsoft Azure, comme indiqué ici : [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Chapeau rouge Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Autres systèmes pris en charge :

* FreeBSD 10 + (Azure Linux Agent v2.0.10 +)

L’agent Linux dépend de certains packages système pour fonctionner correctement :

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilitaires de système de fichiers : sfdisk, fdisk, mkfs, séparées
* Outils de mot de passe : chpasswd, sudo
* Outils de traitement de texte : sed, grep
* Outils de réseau : itinéraire ip
* Noyau prend en charge pour le montage de systèmes de fichiers UDF.

## <a name="installation"></a>Installation
Installation à l’aide d’un t/mn ou un package DEB référentiel de package de votre distribution est la méthode préférée de l’installation et la mise à niveau de l’Agent de Linux Azure. Tous les [approuvée fournisseurs de distribution](virtual-machines-linux-endorsed-distros.md) intégrer le package de l’agent Azure Linux dans leurs référentiels et les images.

Consultez la documentation dans [Azure Linux Agent mis en pension sur Github](https://github.com/Azure/WALinuxAgent) pour les options d’installation avancées, telles que l’installation à partir de la source ou vers des emplacements personnalisés ou doivent être respectées.


## <a name="command-line-options"></a>Options de ligne de commande

### <a name="flags"></a>Indicateurs

- détaillée : augmenter le niveau de détail de commande spécifiée
- forcer : ignorer la confirmation interactive pour certaines commandes

### <a name="commands"></a>Commandes

- aide : répertorie les commandes prises en charge et les indicateurs.

- annulation : tenter de nettoyer le système et adapté à la nouvelle mise en service. Cette opération supprimé les éléments suivants :
 * Toutes les clés hôte SSH (si Provisioning.RegenerateSshHostKeyPair est « y » dans le fichier de configuration)
 * Configuration du serveur de noms dans /etc/resolv.conf
 * Mot de passe depuis/etc/shadow (si Provisioning.DeleteRootPassword est « y » dans le fichier de configuration)
 * Location de client DHCP mis en cache
 * Nom d’hôte réinitialisations localhost.localdomain


> [AZURE.WARNING] Annulation ne garantit pas que l’image est désactivée de toutes les informations sensibles et appropriée pour le redistribuer.


- annulation + utilisateur : effectue toutes les options sous - annulation (ci-dessus) et également supprime le compte d’utilisateur généré dernière (obtenu à partir de /var/lib/waagent) et les données associées. Ce paramètre est lors de la désactivation des comptes une image qui a été précédemment mise en service sur Azure pour qu’il peut être capturé et réutilisée.

- version : affiche la version de waagent

- serialconsole : Configure GRUB pour marquer ttyS0 (le premier port série) en tant que la console de démarrage. Ainsi que les journaux de démarrage noyau sont envoyés au port série et rendus disponibles pour le débogage.

- processus : exécuter waagent en tant qu’un processus pour gérer l’interaction avec la plateforme. Cet argument n’est spécifié pour waagent dans le script d’initialisation waagent.

- Démarrer : exécuter waagent en tant qu’un processus en arrière-plan


## <a name="configuration"></a>Configuration

Un fichier de configuration (/ etc/waagent.conf) contrôle les actions de waagent. Voici un exemple de fichier de configuration :

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

Les différentes options de configuration sont décrites en détail ci-dessous. Options de configuration sont des trois types ; Valeur booléenne, chaîne ou entier. Les options de configuration booléenne peuvent être spécifiées comme « y » ou « n ». Mot-clé « Aucun » peut être utilisé pour certaines chaîne type configuration entrées comme indiqué ci-dessous.

**Provisioning.Enabled :**  
Type : booléenne  
Par défaut : y

Cela permet à l’utilisateur Activer ou désactiver la fonctionnalité de mise en service de l’agent. Les valeurs valides sont « y » ou « n ». Si la mise en service est désactivée, touches hôte et utilisateur SSH dans l’image sont conservés et n’importe quelle configuration spécifiée dans le Azure API de mise en service est ignorée.

> [AZURE.NOTE] La `Provisioning.Enabled` paramètre par défaut est « n » sur les Images de Cloud Ubuntu qui utilisent cloud initialisation de mise en service.

  
**Provisioning.DeleteRootPassword :**  
Type : booléenne  
Par défaut : n

Si le jeu, le mot de passe racine dans le fichier/etc/shadow est permanente pendant le processus de mise en service.


**Provisioning.RegenerateSshHostKeyPair :**  
Type : booléenne  
Par défaut : y

Si le jeu, tous les SSH hôte paires de clés (ecdsa, dsa et rsa) est supprimée pendant le processus de mise en service de/etc/ssh /. Et une seule paire de clés novateur est générée.

Le type de chiffrement pour la nouvelle paire de clés est configurable par l’entrée Provisioning.SshHostKeyPairType. Veuillez noter que certains répartitions créera de nouveau les paires de clés SSH pour tous les types de chiffrement manquants lorsque le processus SSH redémarre (par exemple, après un redémarrage).


**Provisioning.SshHostKeyPairType :**  
Type : chaîne  
Par défaut : rsa

Cela peut être défini à un type d’algorithme de chiffrement est pris en charge par le processus SSH sur l’ordinateur virtuel. Les valeurs généralement pris en charge sont « rsa », « dsa » et « ecdsa ». Notez que « putty.exe » sur Windows ne reconnaît pas « ecdsa ». Par conséquent, si vous prévoyez d’utiliser putty.exe sur Windows pour vous connecter à un déploiement Linux, utilisez « rsa » ou « dsa ».


**Provisioning.MonitorHostName :**  
Type : booléenne  
Par défaut : y

Si la valeur, waagent analysera la machine virtuelle Linux pour les modifications de nom d’hôte (tel que retourné par la commande « hostname ») et mettre à jour automatiquement la configuration réseau dans l’image pour refléter la modification. Pour distribuer le changement de nom vers les serveurs DNS, mise en réseau sera redémarré dans la machine virtuelle. Cela provoquera en bref perte de la connectivité Internet.


**Provisioning.DecodeCustomData**  
Type : booléenne  
Par défaut : n

Si la valeur, waagent sera coder CustomData d’en base 64.


**Provisioning.ExecuteCustomData**  
Type : booléenne  
Par défaut : n

Si la valeur, waagent exécutera CustomData après la mise en service.


**Provisioning.PasswordCryptId**  
Type : chaîne  
Par défaut : 6

Algorithme utilisé par crypt lors de la génération hachage de mot de passe.  
 1 - MD5  
 2 - Blowfish  
 5 - ÇA-256  
 6 - ÇA-512  


**Provisioning.PasswordCryptSaltLength**  
Type : chaîne  
Par défaut : 10

Longueur de salt aléatoire utilisé lors de la génération hachage de mot de passe.


**ResourceDisk.Format :**  
Type : booléenne  
Par défaut : y

Si la valeur, le disque de ressources fourni par la plate-forme est mis en forme et monter par waagent si le type de système de fichiers demandé par l’utilisateur dans « ResourceDisk.Filesystem » n’est pas « ntfs ». Une partition unique de type Linux (83) seront disponible sur le disque. Notez que cette partition ne sera pas formatée si elle peut être installée.


**ResourceDisk.Filesystem :**  
Type : chaîne  
Par défaut : ext4

Spécifie le type de système de fichiers pour le disque de ressources. Valeurs prises en charge varient selon la distribution Linux. Si la chaîne est X, puis mkfs. X doit être présent sur l’image Linux. Images SLES 11 optez généralement « ext3 ». Images FreeBSD doivent utiliser « ufs2 » ci-dessous.


**ResourceDisk.MountPoint :**  
Type : chaîne  
Par défaut : / mnt/ressource 

Spécifie le chemin d’accès à partir duquel le disque ressource est installé. Notez que le disque de la ressource est un disque *temporaire* et peut être vidé lorsque la machine virtuelle est annulée.


**ResourceDisk.MountOptions**  
Type : chaîne  
Par défaut : aucun

Spécifie les options de montage disque à passer à la commande + o montage. Voici la liste de valeurs, séparées par des virgules. « nodev, nosuid ». Voir mount(8) pour plus d’informations.


**ResourceDisk.EnableSwap :**  
Type : booléenne  
Par défaut : n

Si la valeur, un fichier d’échange (/ fichier d’échange) est créé sur le disque de ressources et ajouté à l’espace d’échange système.


**ResourceDisk.SwapSizeMB :**  
Type : entier  
Par défaut : 0

La taille de fichier d’échange en mégaoctets.


**Logs.Verbose :**  
Type : booléenne  
Par défaut : n

Si le jeu, commentaires du journal est augmentée. Waagent ouvre une session /var/log/waagent.log et exploite la fonctionnalité logrotate système pour faire pivoter les journaux.


**SYSTÈME D’EXPLOITATION. EnableRDMA**  
Type : booléenne  
Par défaut : n

Si la valeur, l’agent tentera d’installer, puis charger un pilote de noyau RDMA qui correspond à la version du microprogramme sur le matériel sous-jacent.


**SYSTÈME D’EXPLOITATION. RootDeviceScsiTimeout :**  
Type : entier  
Par défaut : 300

Cela configure le délai d’attente SCSI en secondes sur les lecteurs de disques et les données du système d’exploitation. Si ce n’est pas définie, le système par défaut sont utilisées.


**SYSTÈME D’EXPLOITATION. OpensslPath :**  
Type : chaîne  
Par défaut : aucun

Cela peut servir à spécifier un autre chemin d’accès pour openssl binaire à utiliser pour les opérations de chiffrement.


**HttpProxy.Host, HttpProxy.Port**  
Type : chaîne  
Par défaut : aucun

Si la valeur, l’agent utilisera ce serveur proxy pour accéder à internet. 


## <a name="ubuntu-cloud-images"></a>Images Ubuntu Cloud

Notez que les Images Cloud Ubuntu tirer parti des [cloud initialisation](https://launchpad.net/ubuntu/+source/cloud-init) pour effectuer de nombreuses tâches de configuration dans le cas contraire gérés par l’Agent de Linux Azure.  Veuillez noter les différences suivantes :


- **Provisioning.Enabled** par défaut est « n » sur les Images de Cloud Ubuntu qui cloud initialisation permet d’effectuer les tâches de mise en service.

- Paramètres de configuration suivants n’ont aucun effet sur les Images de Cloud Ubuntu qui cloud initialisation permet de gérer le disque de ressources et échanger espace :

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consultez les ressources suivantes pour configurer le point de montage ressource disque et échanger espace sur Ubuntu Cloud Images au cours de la mise en service :

 - [Ubuntu Wiki : Configurer les Partitions permutation](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Injection de données personnalisée dans une Machine virtuelle Azure](virtual-machines-windows-classic-inject-custom-data.md)

