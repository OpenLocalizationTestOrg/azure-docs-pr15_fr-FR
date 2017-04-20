<properties
    pageTitle="Utilisation de cloud initialisation pour personnaliser une Linux VM lors de la création | Microsoft Azure"
    description="Utilisation de cloud initialisation pour personnaliser une Linux VM lors de la création."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Utilisation de cloud initialisation pour personnaliser une Linux VM lors de la création

Cet article vous explique comment créer un script d’initialisation de cloud pour définir le nom d’hôte, packages de mise à jour installée et gérer les comptes d’utilisateur.  Les scripts cloud initialisation sont appelées lors de la création de la machine virtuelle à partir d’Azure infrastructure du langage commun.  L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide `azure login`.

- le mode de gestionnaire de ressources Azure Azure infrastructure du langage commun _doit être placé dans_ `azure config mode arm`.

## <a name="quick-commands"></a>Commandes rapides

Créer un script cloud init.txt qui définit le nom d’hôte, met à jour tous les packages et ajoute un utilisateur sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Créer un groupe de ressources pour lancer des machines virtuelles dans.

```bash
azure group create myResourceGroup westus
```

Créer une VM Linux à l’aide de cloud initialisation pour configurer lors du démarrage.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

### <a name="introduction"></a>Introduction

Lorsque vous lancez un nouveau VM Linux, vous obtenez une norme Linux VM sans rien personnalisée ou prêt à vos besoins. [Cloud initialisation](https://cloudinit.readthedocs.org) est un moyen standard d’injection de paramètres de script ou de configuration dans cette Linux VM qu’il démarre pour la première fois.

Sous Azure, il existe une différentes manières pour apporter des modifications sur un Linux VM qu’il est déployé ou démarré.

- Injection de scripts à l’aide de cloud initialisation.
- Injection de scripts à l’aide de l' [VMAccess Extension](virtual-machines-linux-using-vmaccess-extension.md)Azure.
- Un modèle Azure à l’aide de cloud initialisation.
- Un modèle Azure à l’aide de [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Faire des scripts à tout moment après le démarrage :

- SSH pour exécuter directement des commandes
- Injection de scripts à l’aide de le [VMAccess Extension](virtual-machines-linux-using-vmaccess-extension.md)d' Azure, impérative ou dans un modèle d’Azure
- Outils de gestion de configuration comme Ansible, Salt, Chef et Marionnette.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilité cloud initialisation sur machine virtuelle Azure création rapide les alias d’image :

| Alias     | Publisher | Offre        | RÉFÉRENCE (SKU)         | Version | cloud initialisation |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | CentOS       | 7.2         | dernière  | aucune         |
| CoreOS    | CoreOS    | CoreOS       | Stable      | dernière  | Oui        |
| Debian    | credativ  | Debian       | 8           | dernière  | aucune         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | dernière  | aucune         |
| RHEL      | Redhat    | RHEL         | 7.2         | dernière  | aucune         |
| UbuntuLTS | Canonique | UbuntuServer | 14.04.4-LTS | dernière  | Oui        |

Microsoft travaille actuellement avec nos partenaires pour obtenir cloud-initialisation inclus et d’utilisation d’images qu’ils accordent à Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Ajout d’un script cloud initialisation à la création de machine virtuelle avec l’infrastructure du langage commun Azure

Pour lancer un script cloud initialisation lorsque vous créez une machine virtuelle dans Azure, spécifiez le fichier cloud initialisation à l’aide de l’infrastructure du langage commun Azure `--custom-data` basculer.

Créer un groupe de ressources pour lancer des machines virtuelles dans.

```bash
azure group create myResourceGroup westus
```

Créer une VM Linux à l’aide de cloud initialisation pour configurer lors du démarrage.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Création d’un script cloud initialisation pour définir le nom d’hôte d’un Linux VM

Un des paramètres de la plus simple et les plus importantes pour n’importe quel Linux VM serait le nom d’hôte. Nous pouvons facilement définir à l’aide de cloud initialisation avec ce script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Exemple de script cloud initialisation nommé `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Lors du démarrage initial de la machine virtuelle, ce script cloud initialisation définit le nom d’hôte sur `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Connexion et vérifiez que le nom d’hôte de la nouvelle machine virtuelle.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Création d’un script cloud initialisation pour mettre à jour Linux

Pour la sécurité, vous voulez que votre VM Ubuntu pour mettre à jour au premier démarrage.  À l’aide de cloud-initialisation que nous pouvons y parvenir avec le script de suivi, en fonction de la distribution Linux que vous utilisez.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Exemple de script cloud initialisation `cloud_config_apt_upgrade.txt` pour la famille Debian

```bash
#cloud-config
apt_upgrade: true
```

Une fois que Linux a démarré, tous les packages installés sont mis à jour `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Connexion et vérifiez que tous les packages sont mis à jour.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Création d’un script cloud initialisation pour ajouter un utilisateur à Linux

Parmi les premières tâches sur n’importe quel nouveau VM Linux consiste à ajouter un utilisateur à votre intention ou à éviter l’utilisation de `root`. Meilleures pratiques pour la sécurité et de facilité d’utilisation sont SSH clés et ils sont ajoutés à la `~/.ssh/authorized_keys` fichier avec ce script cloud initialisation.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Exemple de script cloud initialisation `cloud_config_add_users.txt` pour la famille Debian

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Une fois que Linux a démarré, tous les utilisateurs répertoriés sont créés et ajoutés au groupe sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Connexion et vérifiez que l’utilisateur nouvellement créé.

```bash
ssh myVM
cat /etc/group
```

Sortie

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Étapes suivantes

Cloud initialisation devient un moyen standard de modifier votre VM Linux au démarrage. Azure est également extensions machine virtuelle, qui vous permettent de modifier votre LinuxVM au démarrage ou en cours d’exécution. Par exemple, vous pouvez utiliser la VMAccessExtension Azure pour restaurer les informations SSH ou utilisateur pendant l’exécution de la machine virtuelle. Avec cloud-initialisation, vous devez redémarrer pour réinitialiser le mot de passe.

[Les fonctionnalités et extensions de machine virtuelle](virtual-machines-linux-extensions-features.md)

[Gérer les utilisateurs, SSH et orthographe ou réparer des disques sur machines virtuelles du Linux Azure à l’aide de le VMAccess Extension](virtual-machines-linux-using-vmaccess-extension.md)
