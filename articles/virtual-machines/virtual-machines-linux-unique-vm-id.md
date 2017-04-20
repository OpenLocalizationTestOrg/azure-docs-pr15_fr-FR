<properties
   pageTitle="Accéder aux ID machine virtuelle"
   description="Décrit l’accès à et l’utilisation de numéro d’identification Unique machine virtuelle Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Accès et utilisation nº Unique machine virtuelle Azure

Nº unique machine virtuelle Azure constitue un identificateur 128 bits codé et stockées dans SMBIOS tous les Azure IaaS machines virtuelles et actuellement peut être lus à l’aide des commandes de BIOS plateforme.

Nº unique machine virtuelle Azure est une propriété en lecture seule. Azure ID machine virtuelle Unique ne sera pas modifiée lors d’arrêt redémarrage (soit planifiée pour non planifiée), début/fin supprimer l’allocation, rétablissement du service ou restaurer en place. Toutefois, si la machine virtuelle est un instantané et copiées pour créer une nouvelle instance, Azure machine virtuelle identifiant est configuré.

> [AZURE.NOTE] Si vous disposez ancien machines virtuelles créées et en cours d’exécution dans la mesure où cette nouvelle fonctionnalité avez transférée (18 septembre 2014), veuillez redémarrer votre ordinateur virtuel d’obtenir automatiquement un Azure unique identifiant.


Pour accéder à Azure machine virtuelle nº Unique à partir de la machine virtuelle :


## <a name="create-a-vm"></a>Créer une machine virtuelle
 

Pour plus d’informations, voir [créer une Machine virtuelle](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Se connecter à la machine virtuelle
 

Pour plus d’informations, voir [SSH de Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>Nº Unique requête machine virtuelle

Commande (exemples d’utilisation **Ubuntu**) :

    sudo dmidecode | grep UUID
    
Exemple de résultats attendus :

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
En raison de gros-boutiste bit le classement, l’ID machine virtuelle Unique réel dans ce cas seront :

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Azure ID unique machine virtuelle peut être utilisé dans différents scénarios si la machine virtuelle s’exécute sur Azure ou en local et peut vous aider vos spécifications de suivi des licences, création de rapports ou général que vous n’ayez sur vos déploiements IaaS Azure. De nombreux fournisseurs de logiciels indépendants création d’applications et les certifier sur Azure peuvent nécessiter pour identifier une machine virtuelle Azure tout au long de son cycle de vie et pour savoir si la machine virtuelle s’exécute sur Azure, en local ou sur d’autres fournisseurs cloud. Cet identificateur de plateforme par exemple peut aider à détecter si le logiciel est une licence appropriée ou à pour faire correspondre toutes les données à sa source telle machine virtuelle pour aider sous définissant les mesures de droite pour la plate-forme appropriée pour effectuer le suivi et faire correspondre les mesures entre autres utilisations.
