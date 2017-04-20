<properties
    pageTitle="Créer et télécharger une image personnalisée Linux | Microsoft Azure"
    description="Créer et télécharger un disque dur virtuel (disque dur virtuel) vers Azure avec une image Linux personnalisée à l’aide du modèle de déploiement Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Télécharger et créer un Linux VM à partir de l’image de disque personnalisé

Cet article vous explique comment charger un disque dur virtuel (disque dur virtuel) vers Azure à l’aide du modèle de déploiement Gestionnaire de ressources et créer des ordinateurs virtuels Linux à partir de cette image personnalisée. Cette fonctionnalité permet d’installer et configurer un distro Linux à vos besoins, puis utiliser ce disque dur virtuel pour créer rapidement des machines virtuelles Azure (machines virtuelles).

## <a name="quick-commands"></a>Commandes rapides
Si vous devez effectuer rapidement la tâche, les détails de la section suivante de la base de commandes pour télécharger une machine virtuelle dans Azure. Plus d’informations et le contexte de chaque étape vous pouvez trouver le reste du document, en [commençant ici](#requirements).

Vérifiez que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myimages`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUs` emplacement :

```bash
azure group create myResourceGroup --location "WestUS"
```

Créer un compte de stockage pour mettre en attente de vos disques virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Répertorier les touches d’accès pour votre compte de stockage. Prenez note des `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Créer un conteneur dans votre compte de stockage à l’aide de la clé de stockage que vous avez obtenu. L’exemple suivant crée un conteneur nommé `myimages` à l’aide de la valeur de clé de stockage de `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Enfin, téléchargez votre disque dur virtuel sur le conteneur que vous avez créé. Spécifiez le chemin d’accès local sur votre disque dur virtuel sous `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Vous pouvez désormais créer une machine virtuelle à partir de votre disque virtuel téléchargées [à l’aide d’un modèle de gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Vous pouvez également utiliser l’infrastructure du langage commun en spécifiant l’URI sur votre disque (`--image-urn`). L’exemple suivant crée un ordinateur virtuel nommé `myVM` à l’aide du disque virtuel précédemment téléchargés :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Le compte de stockage de destination doit être identique à l’endroit où vous avez téléchargé votre disque virtuel. Vous devez également spécifier ou answer vous invite à, tous les paramètres supplémentaires requises par le `azure vm create` commande tels que réseau virtuel, adresse IP publique, nom d’utilisateur et SSH clés. Vous pouvez en savoir plus sur les [paramètres d’infrastructure du langage commun le Gestionnaire de ressources disponibles](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Configuration requise
Pour effectuer les opérations suivantes, vous devez :

- **Système d’exploitation Linux installés dans un fichier .vhd** - installer une [distribution Linux Azure par l’éditeur](virtual-machines-linux-endorsed-distros.md) (ou voir [les informations de répartitions non approuvée](virtual-machines-linux-create-upload-generic.md)) sur un disque virtuel dans le format de disque dur virtuel. Il existe plusieurs outils pour créer une machine virtuelle et le disque dur virtuel :
    - Installer et configurer [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM](http://www.linux-kvm.org/page/RunningKVM), en veillant à utiliser le disque dur virtuel en tant que format de votre image. Si nécessaire, vous pouvez [convertir une image](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) à l’aide de `qemu-img convert`.
    - Vous pouvez également utiliser Hyper-V [sur Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Le nouveau format de VHDX n’est pas pris en charge dans Azure. Lorsque vous créez une machine virtuelle, spécifiez un format disque dur virtuel. Si nécessaire, vous pouvez convertir des disques VHDX à l’aide du disque dur virtuel [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou le [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) applet de commande PowerShell. En outre, Azure ne reconnaît pas télécharger dynamiques disques durs virtuels, vous devez convertir ces disques durs statiques avant de le télécharger. Vous pouvez utiliser des outils tels que [Azure utilitaires de disque dur virtuel pour accéder](https://github.com/Microsoft/azure-vhd-utils-for-go) à convertir des disques dynamiques au cours du processus de téléchargement dans Azure.

- Machines virtuelles créés à partir de votre image personnalisée doivent se trouver dans le même compte de stockage que l’image elle-même
    - Créer un compte de stockage et le conteneur pour conserver votre image personnalisée et les machines virtuelles créées
    - Après avoir créé vos ordinateurs virtuels, vous pouvez supprimer en toute sécurité de votre image

Vérifiez que vous disposez de [L’infrastructure du langage commun Azure](../xplat-cli-install.md) connecté et l’utilisation du mode directeur des ressources :

```bash
azure config mode arm
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Préparer l’image à télécharger

Azure prend en charge les différentes versions de Linux (voir [Répartitions approuvée](virtual-machines-linux-endorsed-distros.md)). Les articles suivants vous guident comment préparer les différentes répartitions Linux prises en charge sur Azure :

- **[Distribution centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Chapeau rouge Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Autres - répartitions Non approuvée](virtual-machines-linux-create-upload-generic.md)**

Consultez également les **[Notes d’Installation Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** pour obtenir des conseils plus générales relative à la préparation Linux images pour Azure.

> [AZURE.NOTE] La [plateforme Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) s’applique aux machines virtuelles exécutant Linux uniquement lorsqu’une des répartitions valablement apposées est utilisée avec les détails de la configuration comme indiqué dans la section « Versions pris en charge » dans [Linux sur répartitions Azure-Endorsed](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Groupes de ressources logiquement permettent de rassembler toutes les ressources Azure pour prendre en charge vos machines virtuelles, telles que la mise en réseau virtuelle et le stockage. En savoir plus sur les [groupes de ressources Azure ici](../azure-resource-manager/resource-group-overview.md). Avant de télécharger votre image de disque personnalisé et création de machines virtuelles, vous devez d’abord créer un groupe de ressources. 

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Créer un compte de stockage
Machines virtuelles sont stockées sous forme d’objets BLOB de page au sein d’un compte de stockage. En savoir plus sur le [stockage blob Azure ici](../storage/storage-introduction.md#blob-storage). Vous créez un compte de stockage pour votre image de disque personnalisé et machines virtuelles. Les machines virtuelles que vous créez à partir de votre image de disque personnalisé doivent se trouver dans le même compte de stockage que cette image.

L’exemple suivant crée un compte de stockage nommé `mystorageaccount` dans le groupe de ressources créé précédemment :

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Clés de compte de stockage de liste
Azure génère deux touches d’accès de 512 bits pour chaque compte de stockage. Ces touches d’accès sont utilisés lors de l’authentification pour le compte de stockage, par exemple pour effectuer des opérations d’écriture. En savoir plus sur la [Gestion des accès au stockage ici](../storage/storage-create-storage-account.md#manage-your-storage-account). Vous pouvez afficher les touches d’accès rapide avec la `azure storage account keys list` commande.

Afficher les touches d’accès rapide pour le compte de stockage que vous avez créé :

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Le résultat est semblable à :

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prenez note des `key1` car vous devrez l’utiliser pour interagir avec votre compte de stockage dans les étapes suivantes.

## <a name="create-a-storage-container"></a>Créer un conteneur de stockage
Dans la même façon que vous créez des répertoires différents pour organiser logiquement votre système de fichiers local, vous créez au sein d’un compte de stockage pour organiser des images et des disques virtuels conteneurs. Un compte de stockage peut contenir un nombre quelconque de conteneurs. 

L’exemple suivant crée un conteneur nommé `myimages`, spécifiant la touche d’accès rapide obtenu à l’étape précédente (`key1`) :

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Télécharger le disque dur virtuel
Maintenant vous pouvez effectivement télécharger votre image de disque personnalisé. Comme avec tous les disques virtuels utilisés par machines virtuelles, vous téléchargez et stockez votre image de disque personnalisé sous la forme d’un blob de page.

Spécifiez votre touche d’accès rapide, le conteneur que vous avez créé à l’étape précédente, puis sur le chemin d’accès à l’image de disque personnalisés sur votre ordinateur local :

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Créer des machines virtuelles à partir d’image personnalisée
Lorsque vous créez des machines virtuelles à partir de votre image de disque personnalisé, spécifiez l’URI à l’image de disque. Vous assurer que les correspondances de compte de stockage de destination où se trouve l’image de disque personnalisé. Vous pouvez créer votre machine virtuelle en utilisant le modèle Azure infrastructure du langage commun ou JSON Gestionnaire de ressources.


### <a name="create-a-vm-using-the-azure-cli"></a>Créer une machine virtuelle à l’aide de l’infrastructure du langage commun Azure
Vous spécifiez la `--image-urn` paramètre avec le `azure vm create` commande pour pointer vers votre image de disque personnalisé. Assurez-vous que `--storage-account-name` correspond au compte de stockage où se trouve l’image de disque personnalisé. Vous n’avez pas à utiliser le même conteneur en tant que l’image de disque personnalisés pour stocker vos ordinateurs virtuels. Veillez à créer tous les conteneurs supplémentaires dans la même façon que les étapes précédentes avant de télécharger vos images disque personnalisé.

L’exemple suivant crée un ordinateur virtuel nommé `myVM` à partir de votre image de disque personnalisé :

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Vous devez spécifier ou answer vous invite à, tous les paramètres supplémentaires requises par le `azure vm create` commande tels que réseau virtuel, adresse IP publique, nom d’utilisateur et SSH clés. En savoir plus sur les [paramètres d’infrastructure du langage commun le Gestionnaire de ressources disponibles](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Créer une machine virtuelle à l’aide d’un modèle JSON
Azure modèles Gestionnaire de ressources sont des fichiers Notation JSON (JavaScript Object) qui définissent l’environnement que vous souhaitez créer. Les modèles sont réparties pour les fournisseurs de ressources différents comme cluster ou réseau. Vous pouvez utiliser des modèles existants ou écrivez votre propre. En savoir plus sur [l’utilisation de modèles et le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md).

Dans la `Microsoft.Compute/virtualMachines` fournisseur de votre modèle, vous avez un `storageProfile` nœud qui contient les détails de configuration pour votre ordinateur virtuel. Les deux principaux paramètres à modifier sont la `image` et `vhd` URI qui pointent vers votre image de disque personnalisé et disque virtuel votre nouvel ordinateur de virtuel. La figure suivante montre un exemple de la JSON pour l’utilisation d’une image de disque personnalisé :

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Vous pouvez utiliser [ce modèle pour créer une machine virtuelle à partir d’une image personnalisée existant](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou en savoir plus sur la [Création de vos propres modèles Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md). 

Une fois que vous avez un modèle configuré, vous créez vos ordinateurs virtuels à l’aide de la `azure group deployment create` commande. Spécifier l’URI de votre modèle JSON avec la `--template-uri` paramètre :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Si vous avez un fichier JSON stocké localement sur votre ordinateur, vous pouvez utiliser la `--template-file` paramètre à la place :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez préparé et téléchargé votre disque virtuel personnalisé, vous pouvez en savoir plus sur [l’utilisation de modèles et le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md). Vous pouvez également pour [Ajouter un disque de données](virtual-machines-linux-add-disk.md) vos nouvelles machines virtuelles. Si vous avez exécuté sur vos ordinateurs virtuels dont vous avez besoin pour accéder à des applications, veillez à [Ouvrir les ports et les points de terminaison](virtual-machines-linux-nsg-quickstart.md).