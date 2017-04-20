<properties
    pageTitle="Créer une copie d’un ordinateur virtuel spécialisée dans Azure | Microsoft Azure"
    description="Apprenez à créer une copie d’un ordinateur Windows spécialisées exécutant dans Azure, dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Créer une copie d’un ordinateur Windows spécialisées exécutant dans Azure 

Cet article vous explique comment utiliser l’outil AZCopy pour créer une copie de ce dernier à partir d’une machine virtuelle Windows spécialisées qui s’exécute dans Azure. Vous pouvez ensuite utiliser la copie de ce dernier pour créer un nouvel ordinateur virtuel. 

- Si à copier une machine virtuelle GRG, Découvrez [comment créer une image de machine virtuelle à partir d’une machine virtuelle le Azure GRG existant](virtual-machines-windows-capture-image.md).

- Si vous voulez télécharger un disque dur virtuel à partir d’un ordinateur local virtuel, tels que ceux créés à l’aide de Hyper-V, voir [télécharger un disque dur virtuel Windows à partir d’un ordinateur local virtuel vers Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que vous :

- Disposer d’informations sur les **comptes de stockage source et de destination**. Pour la source de machine virtuelle, vous devez les noms de compte et le conteneur de stockage. En règle générale, le nom du conteneur sera **disques durs virtuels**. Vous devez également avoir un compte de stockage de destination. Si vous n’avez pas encore, vous pouvez créer un à l’aide du portail (**Autres Services** > comptes de stockage > Ajouter) ou à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Azure [PowerShell 1.0](../powershell-install-configure.md) est (ou version ultérieure) installé.

- Téléchargé et installé l' [outil AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Libérer la machine virtuelle

Libérer la machine virtuelle, qui permet de libérer le disque dur virtuel à copier. 

- **Portail**: cliquez sur **machines virtuelles** > **myVM** > arrêter
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` libère la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**.

L' **état** de la machine virtuelle dans le portail Azure les modifications provenant de **arrêt** arrêté **(libéré)**.


## <a name="get-the-storage-account-urls"></a>Obtenir l’URL de compte de stockage

Vous devez les URL des comptes de stockage source et de destination. L’URL ressemble : `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si vous connaissez déjà le nom du compte et le conteneur de stockage, vous pouvez simplement remplacer les informations entre les crochets pour créer votre URL. 

Vous pouvez utiliser le portail Azure ou Azure Powershell pour obtenir l’URL :

- **Portail**: cliquez sur **plusieurs services de** > **comptes de stockage**  >  <storage account> votre fichier de disque dur virtuel source et les **objets BLOB** est probablement dans le conteneur de **disques durs virtuels** . Cliquez sur **Propriétés** pour le conteneur, puis copiez le texte **d’URL**. Vous devez les URL des conteneurs source et cible. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` Obtient les informations pour machine virtuelle nommé **myVM** dans le groupe de ressources **myResourceGroup**. Dans les résultats, recherchez dans la section **profil de stockage** du **Disque dur virtuel Uri**. La première partie de l’Uri est l’URL dans le conteneur et la dernière partie est le nom du système d’exploitation disque dur virtuel pour la machine virtuelle.

## <a name="get-the-storage-access-keys"></a>Obtenir les touches d’accès de stockage

Rechercher les touches d’accès pour la source et de destination comptes de stockage. Pour plus d’informations sur les touches d’accès, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

- **Portail**: cliquez sur **plusieurs services de** > **comptes de stockage**  >  <storage account> **Tous les paramètres** > **touches d’accès rapide**. Copiez la clé appelée **touche1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` Obtient la clé de stockage pour le compte de stockage **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Copiez la clé appelée **touche1**.


## <a name="copy-the-vhd"></a>Copier le disque dur virtuel 

Vous pouvez copier des fichiers entre des comptes de stockage à l’aide de AzCopy. Pour le conteneur de destination, si le conteneur spécifié n’existe pas, il sera créé pour vous. 

Pour utiliser AzCopy, ouvrez une invite de commandes sur votre ordinateur local et accédez au dossier dans lequel AzCopy est installé. Il sera semblable à *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Pour copier tous les fichiers dans un conteneur, vous utilisez le commutateur **/S** . Cela peut être utilisé pour copier le disque dur virtuel du système d’exploitation et tous les disques de données s’ils sont dans le même conteneur. Cet exemple montre comment copier tous les fichiers dans le conteneur **mysourcecontainer** dans stockage compte **mysourcestorageaccount** dans le conteneur **mydestinationcontainer** dans le compte de stockage **mydestinationstorageaccount** . Remplacez les noms des comptes de stockage et conteneurs par votre propre. Remplacer `<sourceStorageAccountKey1>` et `<destinationStorageAccountKey1>` avec vos propres clés.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si vous voulez uniquement copier un disque dur virtuel spécifique dans un conteneur de plusieurs fichiers, vous pouvez également spécifier le nom de fichier en utilisant le commutateur /Pattern. Dans cet exemple, seul le fichier nommé **myFileName.vhd** est copié.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Lorsqu’elle est terminée, vous obtenez un message qui ressemble à :

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Résolution des problèmes

- Lorsque vous utilisez AZCopy, si vous voyez l’erreur « serveur n’a pas pu authentifier la demande. Vérifiez que la valeur de l’en-tête d’autorisation est formée correctement, y compris la signature. » et que vous utilisez la touche 2 ou sur la touche de stockage secondaire, essayez d’utiliser la clé primaire ou 1ère stockage.


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez créer une nouvelle machine virtuelle en [joignant la copie de ce dernier pour une machine virtuelle comme un disque du système d’exploitation](virtual-machines-windows-create-vm-specialized.md).












