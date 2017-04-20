<properties
    pageTitle="Différentes façons de créer un Linux VM | Microsoft Azure"
    description="Découvrez les différentes façons pour créer une machine virtuelle Linux sur Azure, notamment des liens vers des outils et des didacticiels pour chaque méthode."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Différentes façons de créer une machine virtuelle Linux dans Azure

Vous avez la possibilité dans Azure pour créer un ordinateur de virtuel Linux (machine virtuelle) à l’aide d’outils et flux de travail à l’aise pour vous. Cet article résume ces différences et exemples pour créer vos ordinateurs virtuels Linux.


## <a name="azure-cli"></a>Azure infrastructure du langage commun 

L’infrastructure du langage commun Azure est disponible sur plates-formes via un package de npm, packages distro condition ou conteneur Docker. Vous pouvez en savoir plus sur la [façon d’installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md). Les didacticiels suivants fournissent des exemples sur l’utilisation de l’infrastructure du langage commun Azure. Lisez chaque article pour en savoir plus sur les commandes de démarrage rapide d’infrastructure du langage commun affichées :

- [Créer une VM Linux depuis l’interface Azure de développement et de test](virtual-machines-linux-quick-create-cli.md)
    - L’exemple suivant crée un VM CoreOS à l’aide d’une clé publique nommée `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Créer une VM Linux sécurisée à l’aide d’un modèle d’Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - L’exemple suivant crée une machine virtuelle à l’aide d’un modèle que qui se trouve sur GitHub :

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Créer un environnement Linux complet à l’aide de l’infrastructure du langage commun Azure](virtual-machines-linux-create-cli-complete.md)
    - Vous pouvez créer un programme d’équilibrage de charge et plusieurs ordinateurs virtuels dans un jeu de disponibilité.

- [Ajout d’un disque à une VM Linux](virtual-machines-linux-add-disk.md)
    - L’exemple suivant ajoute un disque 5 Go pour une machine virtuelle existante nommée `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portail Azure

Le [portail Azure](https://portal.azure.com) permet de vous permet de créer rapidement une machine virtuelle étant rien à installer sur votre système. Le portail Azure permet de créer la machine virtuelle :

- [Créer une VM Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md) 
- [Connectez un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Système d’exploitation et le choix d’images
Lorsque vous créez une machine virtuelle, vous choisissez une image en fonction du système d’exploitation que vous souhaitez exécuter. Azure et ses partenaires offrent des images, notamment les applications et outils préinstallés. Ou, téléchargez un de vos propres images (voir [la section suivante](#use-your-own-image)).

### <a name="azure-images"></a>Images Azure
Utiliser la `azure vm image` commandes infrastructure du langage commun pour voir ce qui est disponible par publisher, distro release et des versions.

La liste éditeurs disponibles comme suit :

```bash
azure vm image list-publishers --location WestUS
```

Liste des produits disponibles (offres) pour un éditeur donné comme suit :

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste des références SKU disponibles (versions distro) d’une offre donnée comme suit :

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Liste de toutes les images disponibles pour une version donnée suit :

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Pour plus d’exemples sur l’exploration et l’utilisation d’images disponibles, voir [Naviguer et sélectionnez machine virtuelle Azure images avec l’infrastructure du langage commun Azure](virtual-machines-linux-cli-ps-findimage.md).

La `azure vm quick-create` et `azure vm create` commandes ont des alias que vous pouvez utiliser pour accéder rapidement à le distros plus courants et leurs versions les plus récentes. L’utilisation d’alias est souvent plus rapide que la spécification de l’éditeur, offre, référence (SKU) et version chaque fois que vous créez une machine virtuelle :

| Alias     | Publisher | Offre        | RÉFÉRENCE (SKU)         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | dernière  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | dernière  |
| Debian    | credativ  | Debian       | 8           | dernière  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | dernière  |
| RHEL      | Redhat    | RHEL         | 7.2         | dernière  |
| SLES      | SLES      | SLES         | 12-SP1      | dernière  |
| UbuntuLTS | Canonique | UbuntuServer | 14.04.4-LTS | dernière  |

### <a name="use-your-own-image"></a>Utiliser votre propre image

Si vous avez besoin des personnalisations spécifiques, vous pouvez utiliser une image selon une machine virtuelle Azure existant en *capturant* cette dernière. Vous pouvez également télécharger une image créée en local. Pour plus d’informations sur les distros pris en charge et l’utilisation de vos propres images, voir les articles suivants :

- [Azure approuvée répartitions](virtual-machines-linux-endorsed-distros.md)

- [Informations pour répartitions non approuvée](virtual-machines-linux-create-upload-generic.md)

- [Comment faire pour capturer une machine virtuelle Linux en tant que gestionnaire de ressources du modèle](virtual-machines-linux-capture-image.md).
    - Démarrage rapide exemples de commandes pour capturer une machine virtuelle existante :

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Étapes suivantes

- Créer un Linux VM à partir du [portail](virtual-machines-linux-quick-create-portal.md), avec l' [infrastructure du langage commun](virtual-machines-linux-quick-create-cli.md), ou en utilisant un [modèle Azure le Gestionnaire de ressources](virtual-machines-linux-cli-deploy-templates.md).

- Après avoir créé un VM Linux, [l’ajout d’un disque de données](virtual-machines-linux-add-disk.md).

- Étapes à suivre pour [Réinitialiser un mot de passe ou SSH clés et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md)
