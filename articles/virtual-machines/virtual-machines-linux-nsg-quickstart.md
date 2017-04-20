<properties
   pageTitle="Ouvrez les ports et les points de terminaison à une VM Linux | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre VM Linux à l’aide du modèle de déploiement de gestionnaire ressource Azure et l’infrastructure du langage commun Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Ouverture de ports et les points de terminaison à une VM Linux dans Azure
Vous ouvrez un port, ou que vous créez un point de terminaison, pour une machine virtuelle () dans Azure en créant un filtre de réseau sur un sous-réseau ou une interface de réseau machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, sur un groupe de sécurité réseau associés à la ressource qui reçoit le trafic. Nous allons utiliser un exemple courant du trafic web sur le port 80.

## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité réseau et les règles, vous devez [L’infrastructure du langage commun Azure](../xplat-cli-install.md) installé et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `myNetworkSecurityGroup`, et `myVnet`.

Créer votre groupe de sécurité réseau, entrer vos propres noms et l’emplacement approprié. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup` dans les `WestUS` emplacement :

```
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Ajouter une règle pour autoriser le trafic HTTP à votre serveur Web (ou ajuster à votre propre situation, telles que la connectivité SSH access ou base de données). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRule` pour autoriser le trafic TCP sur le port 80 :

```
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Associer le groupe de sécurité réseau interface de réseau de votre ordinateur virtuel (carte réseau). L’exemple suivant associe une carte existante nommée `myNic` avec le groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Par ailleurs, vous pouvez associer votre groupe de sécurité réseau avec un sous-réseau virtuel plutôt qu’à l’interface réseau sur un ordinateur virtuel unique. L’exemple suivant associe un sous-réseau existant nommé `mySubnet` dans les `myVnet` réseau virtuel avec le groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité réseau
Les commandes rapides ici permettent d’être opérationnel avec le trafic vers votre ordinateur virtuel. Groupes de sécurité réseau fournissent des nombreuses nouvelles fonctionnalités et précision pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité réseau et les règles et ici](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Vous pouvez définir des groupes de sécurité réseau et les règles et dans le cadre de modèles Azure le Gestionnaire de ressources. En savoir plus sur la [Création de groupes de sécurité réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser transfert de port pour mapper un port externe unique à un port interne sur votre ordinateur virtuel, utilisez un programme d’équilibrage de charge et les règles de la traduction d’adresses réseau (NAT). Par exemple, vous souhaiterez peut-être exposer le port TCP 8080 avec l’extérieur et que le trafic dirigé vers le port TCP 80 sur un ordinateur virtuel. Vous pouvez en savoir plus sur la [Création d’un équilibrage de charge via Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création des environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [Qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Azure vue d’ensemble du Gestionnaire de ressources pour les programmes d’équilibrage de charge](../load-balancer2    /load-balancer-arm.md)