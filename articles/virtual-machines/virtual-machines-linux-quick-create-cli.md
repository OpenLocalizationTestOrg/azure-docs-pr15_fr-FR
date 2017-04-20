<properties
   pageTitle="Créer une VM Linux sur Azure à l’aide de l’infrastructure du langage commun | Microsoft Azure"
   description="Créer une VM Linux sur Azure à l’aide de l’infrastructure du langage commun."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Créer une VM Linux sur Azure à l’aide de l’infrastructure du langage commun

Cet article vous explique comment déployer rapidement une machine virtuelle de Linux (machine virtuelle) sur Azure à l’aide de la `azure vm quick-create` commande dans l’interface de ligne Azure (commande). La `quick-create` commande déploie une machine virtuelle à l’intérieur d’une infrastructure basique, sécurisée que vous pouvez utiliser pour prototype ou tester rapidement un concept. L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide `azure login`.

- le mode de gestionnaire de ressources Azure Azure infrastructure du langage commun _doit être placé dans_ `azure config mode arm`.

Vous pouvez également rapidement déployer un Linux VM à l’aide du [portail Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Commandes rapides

L’exemple suivant montre comment déployer un CoreOS VM et joindre votre clé SSH (Secure Shell) (que les arguments peuvent être différents) :

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

La procédure suivante comporte une VM UbuntuLTS est déployé, étape par étape, avec des explications du fonctionnement chaque étape.

## <a name="vm-quick-create-aliases"></a>Machine virtuelle création rapide alias

Une méthode rapide pour choisir une distribution consiste à utiliser les alias Azure infrastructure du langage commun mappées à la distribution du système d’exploitation plus courante. Le tableau suivant répertorie les alias (à partir d’Azure infrastructure du langage commun version 0.10). Tous les déploiements qui utilisent `quick-create` par défaut pour les ordinateurs virtuels qui sont sauvegardées par le stockage de lecteur SSD (SSD), qui offre un accès disque plus rapidement mise en service et High performance. (Ces alias de représentent une petite partie des répartitions disponibles dans Azure. Rechercher des images plus sur le marché Azure en [recherchant une image dans PowerShell](virtual-machines-linux-cli-ps-findimage.md), [sur le web](https://azure.microsoft.com/marketplace/virtual-machines/)ou [Téléchargez votre propre image personnalisée](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publisher | Offre        | RÉFÉRENCE (SKU)         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | dernière  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | dernière  |
| Debian    | credativ  | Debian       | 8           | dernière  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | dernière  |
| RHEL      | Chapeau rouge    | RHEL         | 7.2         | dernière  |
| UbuntuLTS | Canonique | Serveur Ubuntu | 14.04.4-LTS | dernière  |

Les sections suivantes utiliser la `UbuntuLTS` alias pour que l’option **ImageURN** (`-Q`) pour déployer un serveur LTS Ubuntu 14.04.4.

Précédent `quick-create` exemple entouré uniquement la `-M` indicateur pour identifier la clé publique SSH à télécharger lors de la désactivation des mots de passe SSH, afin d’être averti pour les arguments suivants :

- nom de groupe de ressources (n’importe quelle chaîne est généralement parfaite pour votre groupe de ressources Azure première)
- Nom de la machine virtuelle
- emplacement (`westus` ou `westeurope` bonne par défaut)
- Linux (afin d’indiquer quel système d’exploitation que vous voulez Azure)
- nom d’utilisateur

L’exemple suivant indique toutes les valeurs et qu’aucune invite n’est requis. Dans la mesure où vous avez un `~/.ssh/id_rsa.pub` comme un ssh rsa format fichier de clé publique, elle fonctionne comme est :

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

La sortie doit ressembler au bloc de sortie suivant :

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Se connecter à la nouvelle machine virtuelle

Connectez-vous à votre ordinateur virtuel à l’aide de l’adresse IP répertorié dans la sortie. Vous pouvez également utiliser le nom de domaine complet (FQDN) qui apparaît :

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Le processus de connexion doit ressembler au bloc de sortie suivant :

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Étapes suivantes

La `azure vm quick-create` commande consiste à déployer rapidement une machine virtuelle afin de pouvoir se connecter à un shell bash et commencer à travailler. Toutefois, à l’aide de `vm quick-create` ne vous donne un contrôle total, ni qu’il ne vous permettent de créer un environnement plus complexe.  Pour déployer un VM Linux qui est personnalisé pour votre infrastructure, vous pouvez suivre un des articles suivants :

- [Utiliser un modèle Azure le Gestionnaire de ressources pour créer un déploiement spécifique](virtual-machines-linux-cli-deploy-templates.md)
- [Créer votre propre environnement personnalisé pour une VM Linux à l’aide des commandes de l’infrastructure du langage commun Azure directement](virtual-machines-linux-create-cli-complete.md)
- [Créer un SSH sécurisé Linux machine virtuelle sur Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Vous pouvez également [utiliser le `docker-machine` Azure pilote avec différentes commandes pour créer rapidement un VM Linux en tant qu’un hôte docker](virtual-machines-linux-docker-machine.md).
