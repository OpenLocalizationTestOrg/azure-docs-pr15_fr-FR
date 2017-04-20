<properties
    pageTitle="Créer une copie de votre machine virtuelle Linux Azure | Microsoft Azure"
    description="Apprenez à créer une copie de votre machine virtuelle Linux Azure dans le modèle de déploiement du Gestionnaire de ressources"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Créer une copie d’une machine virtuelle Linux s’exécutant sur Azure


Cet article vous explique comment créer une copie de votre Azure machine virtuelle () Linux à l’aide du modèle de déploiement Gestionnaire de ressources en cours d’exécution. Tout d’abord vous copiez sur le système d’exploitation et disques de données vers un nouveau conteneur, puis configurez les ressources de réseau et créez le nouvel ordinateur virtuel.

Vous pouvez également [télécharger et créer une machine virtuelle à partir de l’image de disque personnalisé](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Avant de commencer

Vous assurer que les conditions préalables suivantes avant de commencer la procédure :

- Vous avez le [Azure infrastructure du langage commun] (… / xplat-infrastructure du langage commun-install.md) téléchargées et installées sur votre ordinateur. 

- Vous devez également des informations sur votre ordinateur existant Azure Linux virtuel :

| Informations sur la mémoire virtuelle source | Où la trouver |
|------------|-----------------|
| Nom de la machine virtuelle | `azure vm list` |
| Nom du groupe de ressources | `azure vm list` |
| Emplacement | `azure vm list` |
| Nom de compte de stockage | `azure storage account list -g <resourceGroup>` |
| Nom du conteneur | `azure storage container list -a <sourcestorageaccountname>` |
| Nom du fichier source VM VHD | `azure storage blob list --container <containerName>` |



- Vous devez effectuer certains choix sur votre nouvelle machine virtuelle :   <br> -Nom du conteneur   <br> Nom de la machine virtuelle-   <br> Taille de mémoire virtuelle-   <br> nom - vNet   <br> -Nom du sous-réseau   <br> Nom - IP   <br> Nom de la carte réseau-
    

## <a name="login-and-set-your-subscription"></a>Connexion et définir votre abonnement

1. Connectez-vous à l’infrastructure du langage commun.
        
        azure login

2. Vérifiez que vous êtes en mode Gestionnaire de ressources.
    
        azure config mode arm

3. Définir l’abonnement correcte. Vous pouvez utiliser « liste compte azure » pour afficher tous vos abonnements.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Arrêter la machine virtuelle 

Arrêter et libérer la mémoire virtuelle source. Vous pouvez utiliser « machine virtuelle azure liste » pour obtenir une liste de tous les ordinateurs virtuels dans votre abonnement et leurs ressources les noms de groupe.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copier le disque dur virtuel


Vous pouvez copier le disque dur virtuel à partir du stockage source à la destination à l’aide du `azure storage blob copy start`. Dans cet exemple, nous allons copier le disque dur virtuel sur le même compte de stockage, mais un autre conteneur.

Pour copier le disque dur virtuel vers un autre conteneur dans le même compte de stockage, tapez :

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurer le réseau virtuel pour votre nouvelle machine virtuelle

Configurer un réseau virtuel et la carte réseau de votre nouvelle machine virtuelle. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Créer la nouvelle machine virtuelle 

Vous pouvez désormais créer une machine virtuelle à partir de votre disque virtuel téléchargées [à l’aide d’un modèle de gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou via l’infrastructure du langage commun en spécifiant l’URI sur votre disque copiée en tapant :

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment utiliser Azure infrastructure du langage commun pour gérer votre nouvelle machine virtuelle, voir [Azure infrastructure du langage commun des commandes pour le Gestionnaire de ressources Azure](azure-cli-arm-commands.md).
