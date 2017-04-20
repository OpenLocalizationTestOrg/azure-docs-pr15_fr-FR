<properties 
   pageTitle="Comment définir une adresse IP privée statique en mode classique ausing l’infrastructure du langage commun | Microsoft Azure"
   description="Présentation des adresses IP privée statique (DIP) et comment les gérer en mode classique à l’aide de l’infrastructure du langage commun"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Comment définir une adresse IP privée statique (classique) dans Azure infrastructure du langage commun

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [gérer une adresse IP statique privée dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-static-private-ip-arm-cli.md).

Les exemples de commandes Azure infrastructure du langage commun ci-dessous vous attendez un environnement simple déjà créé. Si vous souhaitez exécuter les commandes qu’ils s’affichent dans ce document, commencez par créer l’environnement de test décrite dans [créer une vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Comment spécifier une adresse IP privée statique lorsque vous créez une machine virtuelle
Pour créer un nouvel ordinateur virtuel nommé *DNS01* dans un service en nuage nommé *TestService* en fonction du scénario ci-dessus, procédez comme suit :

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
1. Exécutez la commande **service azure créer** pour créer le service cloud.

        azure service create TestService --location uscentral

    Résultat attendu :

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Exécutez la commande **azure créer machine virtuelle** pour créer la machine virtuelle. Avez-vous remarqué la valeur d’une adresse IP privée statique. La liste visible après la sortie explique les paramètres utilisés.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Résultat attendu :

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (ou--emplacement)**. Région Azure où la machine virtuelle est créée. Pour notre scénario, *centralus*.
    - **-n (ou - nom de la machine virtuelle--)**. Nom de la machine virtuelle à créer.
    - **w (ou--nom de réseau virtuel)**. Nom de la VNet où la machine virtuelle est créée. 
    - **-S (ou--statique ip)**. Privé adresse IP de la machine virtuelle.
    - **TestService**. Nom du service cloud où la machine virtuelle est créée.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Image utilisée pour créer la machine virtuelle.
    - **administrateur**. Administrateur local pour la machine virtuelle Windows.
    - **AdminP@ssw0rd**. Mot de passe administrateur local pour la machine virtuelle Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Comment récupérer des informations statiques d’adresses IP privées pour une machine virtuelle
Pour afficher les informations d’adresse IP privées statiques de la machine virtuelle créée avec le script ci-dessus, exécutez la commande suivante Azure infrastructure du langage commun et observez la valeur pour *StaticIP réseau*:

    azure vm static-ip show DNS01

Résultat attendu :

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Comment supprimer une adresse IP privée d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajouté à la machine virtuelle dans le script ci-dessus, exécutez la commande Azure infrastructure du langage commun suivante :
    
    azure vm static-ip remove DNS01

Résultat attendu :

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Comment ajouter une adresse IP privée statique à un ordinateur virtuel existant
Pour ajouter un mappage statique privé adresse IP de la machine virtuelle créée en utilisant le script ci-dessus, exécutez commande suivante :

    azure vm static-ip set DNS01 192.168.1.101

Résultat attendu :

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les adresses [IP publique réservées](virtual-networks-reserved-public-ip.md) .
- Obtenir des informations sur les adresses [IP public (ILPIP) au niveau de l’instance](virtual-networks-instance-level-public-ip.md) .
- Consultez les [API REST de IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx).
