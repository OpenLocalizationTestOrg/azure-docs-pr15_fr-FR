 <properties
   pageTitle="Azure et Linux | Microsoft Azure"
   description="Décrit les services Azure calculer, le stockage et mise en réseau avec des machines virtuelles Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/14/2016"
   ms.author="v-livech"/>

# <a name="azure-and-linux"></a>Azure et Linux

Microsoft Azure est un ensemble croissant d’intégré public, y compris les bases de données analytique, Machines virtuelles, mobiles, mise en réseau, stockage, des services en nuage et web — idéal pour l’hébergement de vos solutions.  Microsoft Azure fournit une plate-forme informatique scalable qui vous permet de payer uniquement ce que vous utilisez, lorsque vous le souhaitez - sans avoir à investir dans local matériel.  Azure est prêt lorsque vous êtes à évoluer vos solutions et absence à quelle que soit l’échelle que vous avez besoin pour répondre aux besoins de vos clients.

Si vous êtes familiarisé avec les différentes fonctionnalités d’Amazon AWS, vous pouvez examiner vs Azure AWS [document de mappage définition](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).


## <a name="regions"></a>Régions
Ressources de Microsoft Azure sont réparties sur plusieurs régions géographiques dans le monde entier.  Une région » » représente plusieurs centres de données dans une seule zone géographique.  À partir de 1 janvier 2016, cela inclut : 8 en Amérique, 2 en Europe, 6 dans Asie-Pacifique, 2 en Chine continent et 3 en Inde.  Si vous souhaitez une liste complète de toutes les régions Azure, nous tenir à jour qu'une liste existante et que vous venez annoncé régions.

- [Régions Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilité
Afin que votre déploiement bénéficier de notre contrat de niveau de Service machine virtuelle 99.95, vous devez déployer deux ou plusieurs ordinateurs virtuels exécutant votre charge de travail à l’intérieur d’une disponibilité définies. Ainsi, vos ordinateurs virtuels sont réparties sur plusieurs domaines d’erreur dans nos centres de données ainsi que déployées sur hosts avec windows de maintenance. Le total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explique la disponibilité garantie d’Azure dans son ensemble.

## <a name="azure-virtual-machines--instances"></a>Machines virtuelles Azure & Instances
Microsoft Azure prend en charge l’exécution d’un certain nombre de Linux courantes fournies et gérées par un nombre de partenaires.  Vous trouverez des répartitions tels que Red chapeau Enterprise CentOS, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD et plus encore dans la Azure Marketplace. Nous travaillons activement avec différentes Communautés Linux pour ajouter davantage de types à la liste [Qu'azure approuvée Linux Distros](virtual-machines-linux-endorsed-distros.md) .

Si votre distro Linux préféré de choix n’est pas actuellement présente dans la galerie, vous pouvez « mettre votre propre Linux » machine virtuelle en [créant et télécharger un disque dur virtuel Linux dans Azure](virtual-machines-linux-create-upload-generic.md).

Machines virtuelles Azure vous autorise à déployer un large éventail de solutions informatiques d’une manière agile. Vous pouvez déployer pratiquement n’importe quelle charge de travail et n’importe quelle langue sur pratiquement n’importe quel système d’exploitation - Windows, Linux, ou personnalisé créé à partir de l’une de nos croissant liste des partenaires. Ne voyez toujours pas ce que vous recherchez ?  Ne vous inquiétez pas : vous pouvez également importer vos propres images d’en local.

## <a name="vm-sizes"></a>Machine virtuelle tailles
Lorsque vous déployez une machine virtuelle dans Azure, vous allez sélectionner une taille de mémoire virtuelle au sein d’une de nos séries de tailles qui convient à votre charge de travail. La taille affecte également la capacité de traitement power, mémoire et le stockage de la machine virtuelle. Vous êtes facturé en fonction de la durée de la machine virtuelle est en cours d’exécution et ses ressources allouées par d’autres programmes. Une liste complète des [tailles de Machines virtuelles](virtual-machines-linux-sizes.md).

Voici quelques principes de base pour la sélection d’une taille de la mémoire virtuelle depuis un de nos séries (A, D, DS, G et GS).

* Machines virtuelles A série sont notre valeur prix d’entrée de gamme machines virtuelles pour les scénarios de développement/Test et légère charges de travail. Ils sont largement disponibles dans toutes les régions et peuvent se connecter et utiliser toutes les ressources standards disponibles aux machines virtuelles.
* Une série tailles (A8 : A11) sont configurations intensive cluster spécial convient aux applications de cluster High performance computing.
* Machines virtuelles série D sont conçues pour exécuter des applications qui exigent la puissance de calcul supérieure et les performances de disque temporaire. Machines virtuelles série D prévoient le disque temporaire processeurs plus rapides, un taux de mémoire-à-core plus élevé et un lecteur SSD (SSD).
* Série Dv2, est la version la plus récente de notre série D, doté d’une UC plus puissante. L’UC Dv2 série est d’environ 35 % plus rapidement que l’UC de série D. Il est basé sur la dernière génération 2,4 GHz Intel Xeon® E5-2673 v3 processeur (Haskell), avec la technologie 2.0 Intel Turbo Ampli, permet d’accéder jusqu'à 3,2 GHz. La série Dv2 a les mêmes configurations de mémoire et de disque que la série D.
* Machines virtuelles série G offrent le plus de mémoire et exécutent sur des hôtes ayant familles de processeurs Intel Xeon E5 V3.

Remarque : Série DS et machines virtuelles GS série ont accès au stockage Premium : notre SSD sauvegardée High performance faible latence de stockage pour les charges de travail intensives e/s. Stockage Premium est disponible dans certaines zones. Pour plus d’informations, voir :

- [: Premium High performance de stockage pour les charges de travail Azure machine virtuelle](../storage/storage-premium-storage.md)

## <a name="automation"></a>Automatisation
Pour obtenir une culture DevOps appropriée, toute infrastructure doit être code.  Lorsque tout l’infrastructure réside dans le code, qu'il peut être facilement recréé (Phoenix serveurs).  Azure fonctionne avec toutes les principale automatisation des outils, tels que Ansible, Chef, SaltStack et Marionnette.  Azure est également équipé de ses propres outils pour l’automatisation :

- [Modèles Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

- [VMAccess Azure](virtual-machines-linux-using-vmaccess-extension.md)

Prise en charge de [l’initialisation de cloud](http://cloud-init.io/) est déployer Azure sur la plupart des Linux Distros qui prennent en charge.  Actuellement Ubuntu machines virtuelles de canoniques sont déployées avec cloud-initialisation activé par défaut.  RedHats RHEL CentOS et Fedora prise en charge cloud initialisation, cependant les images Azure gérées par RedHat n’ont pas installé cloud-initialisation.  Pour utiliser cloud initialisation sur une système d’exploitation de la famille RedHat, vous devez créer une image personnalisée avec cloud-initialisation installé.

- [À l’aide de cloud initialisation sur machines virtuelles Linux Azure](virtual-machines-linux-using-cloud-init.md)

## <a name="quotas"></a>Quotas
Chaque abonnement Azure a des limites de quota par défaut en place pourrait avoir un impact sur le déploiement d’un grand nombre d’ordinateurs virtuels pour votre projet. La limite actuelle sur une base par abonnement est 20 machines virtuelles par région.  Limites de quota peuvent apparaître en déposant une demande d’assistance demandant une augmentation de la limite.  Pour plus d’informations sur les limites de quota :

- [Limites de Service d’abonnement Azure](../azure-subscription-service-limits.md)


## <a name="partners"></a>Partenaires

Microsoft travaille en étroite collaboration avec nos partenaires afin des images disponibles sont mises à jour et optimisés pour un runtime Azure.  Pour plus d’informations sur les partenaires vérifier leurs pages marketplace ci-dessous.

- [Linux sur répartitions approuvée Azure](virtual-machines-linux-endorsed-distros.md)

Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)

Canonique - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)

Debian - [Azure Marketplace - 8 Debian « Jessica »](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)

FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)

CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)

RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)

Bitnami - [Bibliothèque Bitnami pour Azure](https://azure.bitnami.com/)

Mésosphère - [Azure Marketplace - mésosphère DC/OS sur Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)

Docker - [Azure Marketplace - Service conteneur Azure avec essaim Docker](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)

Jenkins - [Azure Marketplace - plateforme CloudBees Jenkins](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)


## <a name="getting-setup-on-azure"></a>Recevoir le programme d’installation sur Azure
Pour commencer à utiliser Azure que vous avez besoin d’un compte Azure, l’infrastructure du langage commun Azure installé et une paire de clés privées et SSH.

## <a name="sign-up-for-an-account"></a>S’inscrire à un compte
Utilisez le Cloud Azure la première étape consiste à s’inscrire à un compte Azure.  Accédez à la page [D’abonnement à un compte Azure](https://azure.microsoft.com/pricing/free-trial/) pour commencer.

## <a name="install-the-cli"></a>Installer l’infrastructure du langage commun
Avec votre nouveau compte Azure, vous pouvez commencer immédiatement à l’aide du portail Azure, qui est un panneau d’administration basée sur le web.  Pour gérer le Cloud Azure via la ligne de commande, vous installez la `azure-cli`.  Installer l' [Infrastructure du langage commun Azure ](../xplat-cli-install.md)sur votre poste de travail Mac ou Linux.

## <a name="create-an-ssh-key-pair"></a>Créer une paire de clés SSH
À présent que vous avez un compte Azure, le portail web Azure et l’infrastructure du langage commun Azure.  L’étape suivante consiste à créer une paire de clés SSH servant à SSH dans Linux sans utiliser un mot de passe.  [Créer SSH clés sur Linux et Mac](virtual-machines-linux-mac-create-ssh-keys.md) pour activer des connexions sans mot de passe et une meilleure sécurité.

## <a name="getting-started-with-linux-on-microsoft-azure"></a>Prise en main Linux sur Microsoft Azure
Avec la configuration de votre compte Azure, Azure infrastructure du langage commun installé et clés SSH créées, vous êtes maintenant prêt à commencer à créer une infrastructure dans le Cloud Azure.  La première tâche consiste à créer quelques machines virtuelles.

## <a name="create-a-vm-using-the-cli"></a>Créer une machine virtuelle à l’aide de l’infrastructure du langage commun
Créer une VM Linux à l’aide de l’infrastructure du langage commun est une méthode rapide pour déployer une machine virtuelle sans quitter le terminal que vous utilisez.  Tout ce que vous pouvez spécifier sur le portail web est disponible via un indicateur de ligne de commande ou un commutateur.  

- [Créer une VM Linux à l’aide de l’infrastructure du langage commun](virtual-machines-linux-quick-create-cli.md)

## <a name="create-a-vm-in-the-portal"></a>Créer une machine virtuelle dans le portail
Création d’un Linux VM dans le portail web Azure consiste à facilement pointez et cliquez sur énumère les différentes options pour accéder à un déploiement.  Au lieu d’utiliser des indicateurs de ligne de commande ou commutateurs, vous êtes en mesure d’afficher une présentation web piles de différentes options et paramètres.  Tous les éléments disponibles via l’interface de ligne de commande sont également disponible dans le portail.

- [Créer une VM Linux à l’aide du portail](virtual-machines-linux-quick-create-portal.md)

## <a name="login-using-ssh-without-a-password"></a>Connexion à l’aide de SSH sans mot de passe
La machine virtuelle est en cours d’exécution sur Azure et vous êtes prêt à se connecter.  À l’aide de mots de passe pour vous connecter via le protocole SSH est non sécurisé et beaucoup de temps.  L’utilisation de touches SSH est la méthode la plus sûre et également le plus rapide pour vous connecter.  Lorsque vous créez Linux VM via le portail ou de l’infrastructure du langage commun, vous avez deux possibilités d’authentification.  Si vous choisissez un mot de passe pour SSH, Azure configure la machine virtuelle pour autoriser les connexions via les mots de passe.  Si vous décidez d’utiliser une clé publique SSH, Azure configure la machine virtuelle pour autoriser uniquement les connexions via SSH clés et désactive les connexions de mot de passe. Pour sécuriser votre Linux VM en autorisant uniquement des connexions à des clés SSH, utilisez l’option de clé publique SSH lors de la création de machine virtuelle dans le portail ou infrastructure du langage commun.

- [Désactiver les mots de passe SSH sur votre Linux VM en configurant SSHD](virtual-machines-linux-mac-disable-ssh-password-usage.md)

## <a name="related-azure-components"></a>Composants Azure associés

## <a name="storage"></a>Espace de stockage

- [Introduction au stockage de Microsoft Azure](../storage/storage-introduction.md)

- [Ajout d’un disque à une VM Linux à l’aide de l’infrastructure du langage azure-commun](virtual-machines-linux-add-disk.md)

- [L’insertion d’un disque de données à une VM Linux dans le portail Azure](virtual-machines-linux-attach-disk-portal.md)

## <a name="networking"></a>Mise en réseau

- [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

- [Adresses IP dans Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)

- [Ouverture de ports à une VM Linux dans Azure](virtual-machines-linux-nsg-quickstart.md)

- [Créer un nom de domaine complet dans le portail Azure](virtual-machines-linux-portal-create-fqdn.md)


## <a name="containers"></a>Conteneurs

- [Machines virtuelles et des conteneurs dans Azure](virtual-machines-linux-containers.md)

- [Présentation de Service de conteneur Azure](../container-service/container-service-intro.md)

- [Déployer un cluster de Service de conteneur Azure](../container-service/container-service-deployment.md)

## <a name="next-steps"></a>Étapes suivantes

Vous obtenez une vue d’ensemble de Linux sur Azure.  L’étape suivante consiste à l’eau et créer plusieurs machines virtuelles.

- [Créer une VM Linux sur Azure à l’aide du portail](virtual-machines-linux-quick-create-portal.md)

- [Créer une VM Linux sur Azure à l’aide de l’infrastructure du langage commun](virtual-machines-linux-quick-create-cli.md)
