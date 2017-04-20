
<properties
   pageTitle="Créer un environnement Linux complet à l’aide de l’infrastructure du langage commun Azure | Microsoft Azure"
   description="Créer le stockage, un Linux VM, un réseau virtuel et sous-réseau, un équilibrage de charge, une carte réseau, une adresse IP publique et un groupe de sécurité réseau, à partir d’emblée à l’aide de l’infrastructure du langage commun Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Créer un environnement Linux complet à l’aide de l’infrastructure du langage commun Azure

Dans cet article, nous créons un réseau simple avec un équilibrage de charge et une paire d’ordinateurs virtuels qui sont utiles pour le développement et de l’informatique simple. Nous passez en revue le processus de commande par commande, jusqu'à ce que vous avez deux horaires et sécurisé Linux machines virtuelles auxquels vous pouvez vous connecter à partir de n’importe où sur Internet. Puis vous pouvez vous déplacer vers des réseaux et des environnements plus complexes.

Tout au long du processus, vous découvrez la hiérarchie des dépendances que le modèle de déploiement du Gestionnaire de ressources offre, et sur la quantité d’alimentation il. Une fois que vous voyez la manière dont le système est généré, vous pouvez la reconstruire beaucoup plus rapidement à l’aide de [modèles Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md). En outre, une fois que vous découvrez comment les parties de votre environnement imbrication, créer des modèles pour automatiser les devient plus facilement.

L’environnement contient :

- Deux machines virtuelles à l’intérieur d’un jeu de disponibilité.
- Équilibrage de charge avec une règle d’équilibrage de charge sur le port 80.
- Règles de groupe (NSG) sécurité réseau pour protéger votre ordinateur virtuel du trafic non désiré.

![Vue d’ensemble de l’environnement de base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Pour créer cet environnement personnalisé, vous devez la dernière [Azure infrastructure du langage commun](../xplat-cli-install.md) en mode Gestionnaire de ressources (`azure config mode arm`). Vous devez également un outil d’analyse de JSON. Cet exemple utilise [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Commandes rapides
Si vous devez effectuer rapidement la tâche, les détails de la section suivante de la base de commandes pour télécharger une machine virtuelle dans Azure. Plus d’informations et le contexte de chaque étape, vous pouvez trouver dans le reste du document, départ [ici](#detailed-walkthrough).

Vérifiez que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Incluent les noms de paramètres exemple `myResourceGroup`, `mystorageaccount`, et `myVM`.

Créer le groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `westeurope` emplacement :

```bash
azure group create -n myResourceGroup -l westeurope
```

Vérifiez que le groupe de ressources à l’aide de l’analyseur JSON :

```bash
azure group show myResourceGroup --json | jq '.'
```

Créer le compte de stockage. L’exemple suivant crée un compte de stockage nommé `mystorageaccount` (le nom de compte de stockage doit être unique, donc fournir votre propre nom unique) :

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Vérifiez que le compte de stockage à l’aide de l’analyseur JSON :

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Créer le réseau virtuel. L’exemple suivant crée un réseau virtuel nommé `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Créer un sous-réseau. L’exemple suivant crée un sous-réseau nommé `mySubnet`»

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Vérifier le réseau virtuel et un sous-réseau à l’aide de l’analyseur JSON :


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Créer une adresse IP publique. L’exemple suivant crée une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns` (le nom DNS doit être unique, donc fournir votre propre nom unique) :

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Créer l’équilibrage de charge. L’exemple suivant crée un équilibrage de charge nommé `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Créer un pool frontal IP pour l’équilibrage de charge et associer l’adresse IP publique. L’exemple suivant crée un pool frontal d’IP nommé `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Créer le pool IP principale pour l’équilibrage de charge. L’exemple suivant crée un pool IP principale nommé `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Créer des règles de la traduction d’adresse pour l’équilibrage de charge réseau entrant SSH. L’exemple suivant crée deux règles d’équilibrage de charge, `myLoadBalancerRuleSSH1` et `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Créer le site web règles NAT pour l’équilibrage de charge de trafic entrant. L’exemple suivant crée une règle d’équilibrage de charge nommée`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Créer la sonde de santé d’équilibrage de charge. L’exemple suivant crée une sonde TCP appelée `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Vérifiez l’équilibrage de charge, des pools IP et règles NAT à l’aide de l’analyseur JSON :

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Créer la première carte d’interface réseau (carte réseau). Remplacer le `#####-###-###` sections avec vos propres ID d’abonnement Azure. Votre abonnement ID est indiquée dans la sortie de `jq` lors de l’examen les ressources que vous créez. Vous pouvez également afficher votre ID de l’abonnement avec `azure account list`. 

L’exemple suivant crée une carte réseau nommée `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Créer la deuxième carte réseau. L’exemple suivant crée une carte réseau nommée `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Vérifiez que les deux cartes réseau à l’aide de l’analyseur JSON :

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Créer le groupe de sécurité réseau. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Ajouter deux règles de trafic entrant pour le groupe de sécurité réseau. L’exemple suivant crée deux règles, `myNetworkSecurityGroupRuleSSH` et `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Vérifier le groupe de sécurité réseau et les règles de trafic entrant à l’aide de l’analyseur JSON :

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Lier le groupe de sécurité réseau à deux cartes réseau :

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Créer le jeu de disponibilité. L’exemple suivant crée une disponibilité définie nommée `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Créer la première VM Linux. L’exemple suivant crée un ordinateur virtuel nommé `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Créer la deuxième VM Linux. L’exemple suivant crée un ordinateur virtuel nommé `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Utiliser l’analyseur JSON pour vous assurer que tout ce qui a été généré :

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporter votre nouvel environnement à un modèle pour ré-créer rapidement nouvelle instance :

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé
Les étapes détaillées qui suivent expliquent ce que chaque commande fait à mesure que vous créez, votre environnement. Ces concepts sont utiles lorsque vous créez vos propres environnements personnalisés pour le développement ou de production.

Vérifiez que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Incluent les noms de paramètres exemple `myResourceGroup`, `mystorageaccount`, et `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Créer des groupes de ressources et choisissez les emplacements de déploiement

Groupes de ressources Azure sont entités déploiement logique qui contiennent des informations sur la configuration et les métadonnées pour activer la gestion des déploiements ressource logique. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `westeurope` emplacement :

```bash
azure group create --name myResourceGroup --location westeurope
```

Résultat :

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Vous devez comptes de stockage pour vos disques machine virtuelle et des disques de données supplémentaires que vous voulez ajouter. Vous créez des comptes de stockage presque immédiatement une fois que vous créez des groupes de ressources.

Ici, nous utilisons la `azure storage account create` commande, en passant l’emplacement du compte, le groupe de ressources qui les contrôles et le type de prise en charge de stockage souhaité. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Résultat :

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Pour examiner notre groupe de ressources à l’aide de la `azure group show` commande, nous allons utiliser l’outil [jq](https://stedolan.github.io/jq/) avec la `--json` option Azure infrastructure du langage commun. (Vous pouvez utiliser **jsawk** ou une bibliothèque de langue que vous souhaitez analyser le JSON).

```bash
azure group show myResourceGroup --json | jq '.'
```

Résultat :

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Pour déterminer l’origine du compte de stockage à l’aide de l’infrastructure du langage commun, vous devez tout d’abord définir les noms des comptes et les clés. Remplacez le nom du compte de stockage dans l’exemple suivant avec un nom de votre choix :

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Vous pouvez afficher vos informations de stockage facilement :

```bash
azure storage container list
```

Résultat :

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et sous-réseau

Ensuite, vous allez nécessaires pour créer un réseau virtuel en cours d’exécution dans Azure et un sous-réseau dans lequel vous pouvez créer vos ordinateurs virtuels. L’exemple suivant crée un réseau virtuel nommé `myVnet` avec la `192.168.0.0/16` préfixe d’adresse :

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Résultat :

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Là encore, nous allons utiliser l’option--json de `azure group show` et **jq** pour voir comment nous construisons nos ressources. Nous ont désormais un `storageAccounts` ressource et un `virtualNetworks` ressource.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Résultat :

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Maintenant nous allons créer un sous-réseau dans le `myVnet` réseau virtuel dans lequel les ordinateurs virtuels sont déployés. Nous utilisons la `azure network vnet subnet create` commande, ainsi que les ressources que nous avons déjà créé : le `myResourceGroup` groupe de ressources et la `myVnet` réseau virtuel. Dans l’exemple suivant, nous ajoutons le sous-réseau nommé `mySubnet` avec le préfixe d’adresse sous-réseau `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Résultat :

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Étant donné que la constitue logiquement au sein du réseau virtuel, nous recherchez les informations de sous-réseau avec une commande légèrement différente. La commande nous utilisons est `azure network vnet show`, mais nous continuer examiner la sortie JSON à l’aide de **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Résultat :

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Créer une adresse IP publique (PIP)

Maintenant créer l’adresse IP publique (PIP) qui nous affecter à votre programme d’équilibrage de charge. Il permet de vous connecter à vos ordinateurs virtuels à partir d’Internet à l’aide de la `azure network public-ip create` commande. Étant donné que l’adresse par défaut est dynamique, nous créer une entrée DNS nommée dans le domaine **cloudapp.azure.com** à l’aide de la `--domain-name-label` option. L’exemple suivant crée une adresse IP publique nommée `myPublicIP` par le nom DNS du `mypublicdns`. Comme le nom DNS doit être unique, indiquez un nom de votre propre DNS unique :

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Résultat :

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

L’adresse IP est également une ressource de niveau supérieur, afin que vous puissiez voir avec `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Résultat :

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Vous pouvez examiner les autres détails de la ressource, y compris le nom de domaine complet (FQDN) de sous-domaine, à l’aide de l’ensemble `azure network public-ip show` commande. La ressource d’adresse IP publique a été affectée logiquement, mais une adresse spécifique n’a pas encore été affectée. Pour obtenir une adresse IP, vous aurez besoin d’un équilibrage de charge, nous n’avons pas encore créé.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Résultat :

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Créer un programme d’équilibrage de charge et des pools d’IP
Lorsque vous créez un équilibrage de charge, il vous permet de distribuer le trafic sur plusieurs ordinateurs virtuels. Il fournit également redondantes à votre application en exécutant plusieurs machines virtuelles qui répondent à des demandes de l’utilisateur en cas de maintenance ou charges. L’exemple suivant crée un équilibrage de charge nommé `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Résultat :

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Notre programme d’équilibrage de charge est relativement vide, nous allons créer des pools d’IP. Nous voulons créer deux pools d’IP pour notre équilibrage de charge - une pour le serveur frontal et une pour le serveur principal. Le pool frontal d’IP est visible publiquement. Il est également l’emplacement auquel nous affecter PIP que nous avons créés précédemment. Puis, nous utilisons le pool principale comme un emplacement pour les nos machines virtuelles pour vous connecter à. De cette façon, le trafic flux dans l’équilibrage de charge pour les ordinateurs virtuels.

Tout d’abord, nous allons créer notre pool frontal IP. L’exemple suivant crée un pool frontal nommé `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Résultat :

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Notez la manière dont nous avons utilisé le `--public-ip-name` commutateur pour passer dans le `myPublicIP` que nous avons créé précédemment. Attribution de l’adresse IP à l’équilibrage de charge vous permet d’atteindre vos ordinateurs virtuels via Internet.

Ensuite, nous allons créer notre deuxième pool IP, cette fois pour le trafic de notre principale. L’exemple suivant crée un pool principale nommé `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Résultat :

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Nous pouvons voir faire de notre programme d’équilibrage de charge en recherchant avec `azure network lb show` et examen de la sortie JSON :

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Résultat :

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Créer des règles NAT équilibrage de charge
Pour obtenir le trafic passant par notre programme d’équilibrage de charge, nous avons besoin de créer des règles de la traduction spécifient les actions entrantes ou sortantes adresse réseau. Vous pouvez spécifier le protocole à utiliser, puis établir une correspondance entre les ports externes ports internes comme vous le souhaitez. Pour notre environnement, nous allons créer des règles qui autorisent SSH via notre équilibrage de charge à notre machines virtuelles. Nous configurer les ports TCP 4222 et 4223 pour diriger vers le port TCP 22 sur notre machines virtuelles (ce qui nous créer ultérieurement). L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH1` pour mapper les ports TCP 4222 à la voie 22 :

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Résultat :

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Répétez cette procédure pour la deuxième règle NAT pour SSH. L’exemple suivant crée une règle nommée `myLoadBalancerRuleSSH2` pour établir une correspondance entre le port TCP 4223 port 22 :

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Nous allons également alors et créer une règle NAT pour le port TCP 80 pour le trafic web, lancement de la règle jusqu'à notre pools d’IP. Si nous sociaux la règle au pool IP, au lieu de connecter la règle à notre machines virtuelles individuellement, nous pouvons ajouter ou supprimer des machines virtuelles du pool d’IP. L’équilibrage de charge ajuste automatiquement le flux du trafic. L’exemple suivant crée une règle nommée `myLoadBalancerRuleWeb` pour mapper le port TCP 80 vers le port 80 :

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Résultat :

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Créer une sonde charge équilibrage d’intégrité

Santé sonde régulièrement des vérifications sur les ordinateurs virtuels qui se trouvent derrière notre équilibrage de charge pour vous assurer que leur fonctionnement et répondre aux requêtes, telle que définie. Dans le cas contraire, ils sont supprimés de s’assurer que les utilisateurs ne sont pas en cours dirigés vers ces signets à l’opération. Vous pouvez définir des vérifications personnalisées pour la sonde santé, ainsi que des intervalles et des valeurs de délai. Pour plus d’informations sur les investigations santé, voir [équilibrage de charge teste](../load-balancer/load-balancer-custom-probe-overview.md). L’exemple suivant crée un TCP santé exploré nommée `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Résultat :

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Ici, nous avons spécifié un intervalle de 15 secondes pour notre vérifications au niveau. Nous pouvons rater un maximum de quatre sondes (une minute) avant l’équilibrage de charge estime que l’hôte ne fonctionne plus.

## <a name="verify-the-load-balancer"></a>Vérifier l’équilibrage de charge
À présent la configuration d’équilibrage de charge est terminée. Voici les étapes que vous avez suivies :

1. Tout d’abord, vous avez créé un équilibrage de charge.
2. Ensuite, vous avez créé un pool frontal IP et affecté une adresse IP publique.
3. Ensuite, vous avez créé un groupe d’IP principale machines virtuelles pouvant se connecter à.
4. Après cela, vous avez créé des règles de NAT qui autorisent SSH pour les ordinateurs virtuels pour la gestion, ainsi que d’une règle qui autorise le port TCP 80 notre application web.
5. Enfin, vous avez ajouté une sonde d’intégrité pour vérifier régulièrement les ordinateurs virtuels. Cette sonde santé garantit que les utilisateurs n’essaient d’accéder à un ordinateur virtuel qui n’est plus fonctionne ni publiant le contenu.

Passons en revue ce que votre équilibrage de charge ressemble maintenant :

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Résultat :

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Créer une carte réseau à utiliser avec la Linux VM

Cartes réseau sont disponibles par programme parce que vous pouvez appliquer des règles à leur utilisation. Vous pouvez également avoir plusieurs. L’exemple suivant `azure network nic create` commande, se connecter à la carte réseau au pool d’IP charge principale et de l’associer à la règle NAT pour autoriser le trafic SSH.
 
Remplacer le `#####-###-###` sections avec vos propres ID d’abonnement Azure. Votre abonnement ID est indiquée dans la sortie de `jq` lors de l’examen les ressources que vous créez. Vous pouvez également afficher votre ID de l’abonnement avec `azure account list`. 

L’exemple suivant crée une carte réseau nommée `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Résultat :

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Vous pouvez afficher les détails en examinant directement la ressource. Vous examinez la ressource à l’aide de la `azure network nic show` commande :

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Résultat :

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Maintenant, nous créons la seconde carte, connexion à notre pool IP principale à nouveau. Cette règle NAT la seconde autorise le trafic SSH. L’exemple suivant crée une carte réseau nommée `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Créer un groupe de sécurité réseau et des règles

Nous créons à un groupe de sécurité réseau et les règles de trafic entrant qui régissent l’accès à la carte réseau. Un groupe de sécurité réseau peuvent être appliqué à une carte réseau ou sous-réseau. Vous définissez des règles pour contrôler le flux du trafic entrant et sortant vos ordinateurs virtuels. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Nous allons ajouter la règle de trafic entrant pour le NSG autoriser les connexions entrantes sur le port 22 (pour prendre en charge SSH). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRuleSSH` pour autoriser le port TCP 22 :

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Maintenant nous allons ajouter la règle de trafic entrant pour le NSG autoriser les connexions entrantes sur le port 80 (pour prendre en charge le trafic web). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRuleHTTP` pour autoriser le port TCP 80 :

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] La règle de trafic entrant est un filtre pour les connexions réseau entrantes. Dans cet exemple, nous lier le NSG à la carte virtual machines virtuelles, ce qui signifie que toute demande à la voie 22 est transmis à la carte réseau sur notre machine virtuelle. Cette règle entrante est sur une connexion réseau, et non sur un point de terminaison, c'est-à-dire qu’il serait sur dans les déploiements classiques. Pour ouvrir un port, vous devez laisser la `--source-port-range` défini sur «\*» (valeur par défaut) pour accepter des demandes entrantes à partir de **n’importe quel** demandant port. Ports sont généralement dynamiques.

## <a name="bind-to-the-nic"></a>Lier à la carte réseau

Lier le NSG aux cartes réseau. Nous avons besoin de vous connecter notre cartes réseau avec notre groupe de sécurité réseau. Exécutez les deux commandes, pour connecter les deux de nos cartes réseau :

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Créer un jeu de disponibilité
Disponibilité ensembles de vos ordinateurs virtuels propagation à l’aide sur les domaines de défaillance et mise à niveau. Nous allons créer une disponibilité définie pour vos ordinateurs virtuels. L’exemple suivant crée une disponibilité définie nommée `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domaines d’erreur définissent le regroupement de machines virtuelles qui partagent un commutateur de source et de réseau power courantes. Par défaut, les ordinateurs virtuels qui sont configurés dans votre jeu de disponibilité sont séparées sur jusqu'à trois domaines d’erreur. L’idée est qu’un problème matériel dans un de ces domaines défaillance n’affecte pas chaque machine virtuelle exécutant votre application. Azure répartit automatiquement machines virtuelles sur les domaines d’erreur lorsque vous les placez dans un jeu de disponibilité.

Mise à niveau domaines indiquent les groupes de machines virtuelles et le matériel physique sous-jacent qui peut être redémarré en même temps. L’ordre dans lequel les domaines mise à niveau sont redémarrés peut-être ne pas être séquentielle lors de la maintenance planifiée, mais qu’une mise à niveau est réinitialisé à la fois. Lorsque vous les placez dans un site de disponibilité, Azure répartit automatiquement vos ordinateurs virtuels sur domaines mise à niveau.

En savoir plus sur la [gestion de la disponibilité des machines virtuelles](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Créer les ordinateurs virtuels Linux

Vous avez créé les ressources de stockage et de réseau pour prendre en charge des machines virtuelles accessibles sur Internet. Maintenant nous allons créer ceux-ci machines virtuelles et banque d’informations sécurisé avec un code SSH n’ayant pas d’un mot de passe. Dans ce cas, nous allons créer une machine virtuelle en fonction de la plus récente LTS Ubuntu. Nous recherchez cette information d’image à l’aide de `azure vm image list`, comme décrit dans la [recherche d’images de machine virtuelle Azure](virtual-machines-linux-cli-ps-findimage.md).

Nous avons sélectionné une image à l’aide de la commande `azure vm image list westeurope canonical | grep LTS`. Dans ce cas, nous utilisons `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Pour que le dernier champ, nous transmettre `latest` pour permettre aux à l’avenir nous accéder toujours la version la plus récente. (La chaîne nous utilisons est `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Cette étape suivante consiste familier à quiconque a déjà créé un ssh rsa privée et paire de clés sur Linux ou Mac à l’aide de **ssh keygen-t rsa-b 2048**. Si vous n’avez pas de n’importe quelle paire de clé de certificat votre `~/.ssh` répertoire, vous pouvez créer les :

- Automatiquement, à l’aide de la `azure vm create --generate-ssh-keys` option.
- Manuellement, en utilisant [les instructions pour les créer vous-même](virtual-machines-linux-mac-create-ssh-keys.md).

Vous pouvez également utiliser la `--admin-password` méthode pour authentifier vos connexions SSH après la création de la machine virtuelle. Cette méthode est généralement moins sûre.

Nous créer la machine virtuelle en mettant tous nos ressources et des informations avec les `azure vm create` commande :

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Résultat :

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Vous pouvez vous connecter à votre ordinateur virtuel immédiatement à l’aide de vos clés SSH par défaut. Assurez-vous que vous spécifiez le port approprié dans la mesure où nous allons passant par l’équilibrage de charge. (Pour notre première machine virtuelle, nous configurer la règle NAT pour transférer port 4222 à notre machine virtuelle) :

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Résultat :

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Continuez et créer votre deuxième machine virtuelle de la même manière :

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Et vous pouvez désormais utiliser la `azure vm show myResourceGroup myVM1` commande pour examiner ce que vous avez créé. À ce stade, vous exécutez vos ordinateurs virtuels Ubuntu derrière un équilibrage de charge dans Azure que vous pouvez vous connecter à uniquement avec votre paire de clés SSH (parce que les mots de passe sont désactivés). Vous pouvez installer nginx ou httpd, déployer une application web et voir le trafic flux via l’équilibrage de charge à la fois à des ordinateurs virtuels.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Résultat :

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exporter l’environnement en tant que modèle
À présent que vous avez créé cet environnement, que se passe-t-il si vous souhaitez pour créer un environnement de développement supplémentaires avec les mêmes paramètres, ou un environnement de production correspondant à ? Gestionnaire de ressources utilise des modèles JSON qui définissent tous les paramètres pour votre environnement. Vous développez des environnements entières en faisant référence à ce modèle JSON. Vous pouvez [créer des modèles JSON manuellement](../resource-group-authoring-templates.md) ou exporter un environnement existant pour créer le modèle JSON pour vous :

```bash
azure group export --name myResourceGroup
```

Cette commande crée le `myResourceGroup.json` fichier dans votre répertoire de travail en cours. Lorsque vous créez un environnement de ce modèle, vous êtes invité à tous les noms de ressources, y compris les noms pour l’équilibrage de charge, des interfaces réseau ou machines virtuelles. Vous pouvez remplir ces noms dans votre fichier de modèle en ajoutant le `-p` ou `--includeParameterDefaultValue` paramètre à la `azure group export` commande présentée précédemment. Modifier votre modèle JSON pour spécifier les noms des ressources, ou [créer un fichier parameters.json](../resource-group-authoring-templates.md#parameters) qui spécifie les noms des ressources.

Pour créer un environnement à partir du modèle :

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Vous souhaiterez peut-être en savoir [plus sur le déploiement à partir de modèles](../resource-group-template-deploy-cli.md). Découvrez comment mettre à jour les environnements par incréments, utilisez le fichier de paramètres et accéder aux modèles à partir d’un emplacement de stockage unique.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à commencer à travailler avec plusieurs composants réseau et machines virtuelles. Vous pouvez utiliser cet environnement d’exemple pour générer votre application en utilisant les composants principaux introduites ici.
