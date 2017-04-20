<properties
   pageTitle="Créer une VM Linux avec plusieurs cartes réseau | Microsoft Azure"
   description="Apprenez à créer une VM Linux avec plusieurs cartes réseau liée à l’aide de l’infrastructure du langage commun Azure ou le Gestionnaire de ressources de modèles."
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
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Création d’un Linux VM avec plusieurs cartes réseau
Vous pouvez créer une machine virtuelle () dans Azure qui possède plusieurs interfaces réseau virtuel (cartes réseau) est jointes à. Un scénario courant serait d’avoir différents sous-réseaux pour la connectivité frontale et principale, ou sur un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides permettant de créer une machine virtuelle avec plusieurs cartes réseau est jointe à. Pour plus d’informations, notamment comment créer plusieurs cartes réseau dans vos propres scripts Bash, en savoir plus sur le [déploiement des machines virtuelles plusieurs cartes](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Différentes [tailles machine virtuelle](virtual-machines-linux-sizes.md) prise en charge d’un nombre variable de cartes réseau, donc dimensionner votre machine virtuelle en conséquence.

>[AZURE.WARNING] Vous devez joindre plusieurs cartes réseau lorsque vous créez une machine virtuelle, vous ne pouvez pas ajouter des cartes réseau pour une machine virtuelle existante. Vous pouvez [créer une machine virtuelle basée sur les disques virtuels d’origine](virtual-machines-linux-copy-vm.md) et créer plusieurs cartes réseau lorsque vous déployez la machine virtuelle.

## <a name="quick-commands"></a>Commandes rapides
Vérifiez que vous disposez de l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myVM`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure group create myResourceGroup -l WestUS
```

Créer un compte de stockage pour mettre en attente de vos ordinateurs virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Créer un réseau virtuel pour connecter vos ordinateurs virtuels à. L’exemple suivant crée un réseau virtuel nommé `myVnet` avec un préfixe d’adresse `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Créez deux sous-réseaux réseau virtuel - un pour le trafic frontal et un pour le trafic principale. L’exemple suivant crée un des deux sous-réseaux nommés `mySubnetFrontEnd` et `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Créez deux cartes réseau, joindre une carte réseau au sous-réseau frontal et une carte réseau au sous-réseau principale. L’exemple suivant crée deux cartes réseau, nommé `myNic1` et `myNic2`et les associe à vos sous-réseaux :

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Enfin, créez votre machine virtuelle, en joignant les deux cartes réseau que vous avez créé précédemment. L’exemple suivant crée un ordinateur virtuel nommé `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Création de plusieurs cartes réseau à l’aide d’infrastructure du langage commun Azure
Si vous avez précédemment créé une machine virtuelle à l’aide de l’infrastructure du langage commun Azure, les commandes rapides doivent être familiers. Le processus est la même façon pour créer une carte réseau ou plusieurs cartes réseau. Vous trouverez plus d’informations sur le [déploiement de plusieurs cartes réseau à l’aide de l’infrastructure du langage commun Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), y compris les scripts pour le processus de boucle pour créer toutes les cartes réseau.

L’exemple suivant crée deux cartes réseau, nommé `myNic1` et `myNic2`, avec une carte réseau se connecter à chaque sous-réseau :

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

En règle générale vous également créez un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou [l’équilibrage de charge](../load-balancer/load-balancer-overview.md) pour aider à gérer et distribuer le trafic sur vos ordinateurs virtuels. Là encore, les commandes sont identiques lorsque vous travaillez avec plusieurs cartes réseau. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Lier vos cartes réseau au groupe de sécurité réseau à l’aide `azure network nic set`. L’exemple suivant lie `myNic1` et `myNic2` avec `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Lorsque vous créez la machine virtuelle, vous spécifiez maintenant plusieurs cartes réseau. Au lieu de cela à l’aide de `--nic-name` pour fournir une seule carte réseau, à la place d’utiliser `--nic-names` et fournir une liste des cartes réseau séparées par des virgules. Vous devez également prendre en charge lorsque vous sélectionnez la taille de la mémoire virtuelle. Il existe des limites pour le nombre total de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles de machine virtuelle Linux](virtual-machines-linux-sizes.md). L’exemple suivant montre comment spécifier plusieurs cartes réseau, puis sur une taille de mémoire virtuelle qui prend en charge à l’aide de plusieurs cartes réseau (`Standard_DS2_v2`) :

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Création de plusieurs cartes réseau à l’aide de modèles de gestionnaire de ressources
Azure modèles Gestionnaire de ressources permet de définir votre environnement déclaratives fichiers JSON. Vous pouvez lire une [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). Gestionnaire de ressources modèles permettent de créer plusieurs instances d’une ressource au cours du déploiement, telles que la création de plusieurs cartes réseau. *Copie* vous permet de spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [Création de plusieurs instances à l’aide de *copie*](../resource-group-create-multiple.md). 

Vous pouvez également utiliser un `copyIndex()` puis ajouter un nombre à un nom de la ressource, qui permet de créer `myNic1`, `myNic2`, etc.. La figure suivante montre un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez lire un exemple complet de la [Création de plusieurs cartes réseau à l’aide de modèles de gestionnaire de ressources](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Veillez à examiner les [tailles machine virtuelle Linux](virtual-machines-linux-sizes.md) lorsque vous tentez de créer une machine virtuelle avec plusieurs cartes réseau. Soyez attentif au nombre maximal de cartes réseau prend en charge la taille de chaque machine virtuelle. 

N’oubliez pas que vous ne pouvez pas ajouter des cartes réseau pour une machine virtuelle existante, vous devez créer toutes les cartes réseau lorsque vous déployez la machine virtuelle. Lorsque vous planifiez vos déploiements pour vous assurer que vous disposez de tous les la connectivité réseau requis dès le début intégrées.