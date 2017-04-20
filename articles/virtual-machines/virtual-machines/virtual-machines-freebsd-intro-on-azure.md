<properties
   pageTitle="Présentation de FreeBSD sur Azure | Microsoft Azure"
   description="Découvrez comment utiliser des machines virtuelles FreeBSD sur Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Présentation de FreeBSD sur Azure
Cette rubrique fournit une vue d’ensemble de l’exécution d’une machine virtuelle FreeBSD dans Azure.

## <a name="overview"></a>Vue d’ensemble
FreeBSD pour Microsoft Azure est un système d’exploitation expérimentés utilisé pour les serveurs modernes power, postes de travail et incorporées plateformes. L’image FreeBSD 10.3 est fourni par Microsoft Corporation et est disponible dans Azure. Il est basé sur la version 10.3 FreeBSD et Azure Agent d’invité machine virtuelle [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) est installé. L’agent est responsable de la communication entre le FreeBSD VM et le tissu Azure pour les opérations, telles que la mise en service de la machine virtuelle lors de la première utilisation (nom d’utilisateur, mot de passe, nom d’hôte, etc.) et l’activation de fonctionnalités pour les extensions machine virtuelle sélectives.
En ce qui concerne les versions futures de FreeBSD, la stratégie consiste à rester à jour et de rendre les dernières versions dès qu’elles sont disponibles par l’équipe d’ingénierie FreeBSD version. Les prochaines sont [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Déploiement d’une machine virtuelle FreeBSD
Déploiement d’une machine virtuelle FreeBSD est un processus simple à l’aide d’une image à partir de la [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extensions de machine virtuelle pour FreeBSD
Voici des extensions machine virtuelle prises en charge dans FreeBSD.

### <a name="vmaccess"></a>VMAccess

L’extension [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pouvez :

- Réinitialiser le mot de passe de l’utilisateur sudo d’origine.
- Créer un nouvel utilisateur sudo avec le mot de passe.
- Définir la clé publique hôte avec la touche donnée.
- Réinitialiser la clé publique hôte fournie au cours de la machine virtuelle mise en service si la touche hôte n’est fournie.
- Ouvrir le port SSH (22) et restaurer la sshd_config si reset_ssh est défini sur true.
- Supprimer l’utilisateur existant.
- Vérifier les disques.
- Réparer un disque ajouté.

### <a name="customscript"></a>CustomScript

L’extension [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pouvez :

- Si fourni, télécharger les scripts personnalisés de stockage Azure ou stockage public externe (par exemple, GitHub).
- Exécuter le script de point d’entrée.
- Prend en charge les commandes en ligne.
- Convertir le saut de ligne style Windows dans des scripts Python et shell automatiquement.
- Supprimer nomenclature dans shell et scripts Python automatiquement.
- Protection des données sensibles dans CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>D’authentification : les noms d’utilisateur, les mots de passe et clés SSH
Lorsque vous créez une machine virtuelle FreeBSD à l’aide du portail Azure, vous devez fournir un nom d’utilisateur, mot de passe ou clé publique SSH.
Noms d’utilisateur pour le déploiement d’une machine virtuelle FreeBSD sur Azure ne doivent pas correspondent aux noms des comptes système (UID < 100) déjà présentes dans la machine virtuelle (« racine », par exemple).
Pour l’instant, seule la RSA SSH clé est pris en charge. Une clé SSH multiligne doit commencer par « Situés à début SSH2 clé publique situés à » et se terminent par « Situés à fin SSH2 clé publique situés à ».

## <a name="obtaining-superuser-privileges"></a>Obtention des privilèges super utilisateur
Le compte d’utilisateur spécifié pendant le déploiement d’instance machine virtuelle sur Azure est un compte privilégié. Le package de sudo a été installé dans l’image FreeBSD publié.
Une fois que vous êtes connecté à ce compte d’utilisateur, vous pouvez exécuter des commandes en tant que racine à l’aide de la syntaxe de la commande.

    # sudo <COMMAND>

Vous pouvez éventuellement obtenir un shell racine en utilisant sudo -d.

## <a name="next-steps"></a>Étapes suivantes
- Accédez à [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) pour créer un FreeBSD VM.
- Si vous voulez optimiser vos propres FreeBSD Azure, reportez-vous à [créer et télécharger un disque dur virtuel FreeBSD vers Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
