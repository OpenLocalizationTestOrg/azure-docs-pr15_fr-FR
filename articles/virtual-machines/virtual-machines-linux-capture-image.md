<properties
    pageTitle="Capturer une VM Linux à utiliser en tant que modèle | Microsoft Azure"
    description="Découvrez comment capturer et généralisez une image d’une basé sur Linux Azure machine virtuelle () créée avec le modèle de déploiement d’Azure le Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturer une machine virtuelle Linux s’exécutant sur Azure

Suivez les étapes décrites dans cet article pour généraliser et capturer votre machine virtuelle de Linux Azure (machine virtuelle) dans le modèle de déploiement du Gestionnaire de ressources. Lorsque vous généralisez la machine virtuelle, vous supprimez les informations de compte personnel et préparez la machine virtuelle à utiliser en tant qu’image. Vous puis capturez une image de disque dur virtuel GRG (disque dur virtuel) pour le système d’exploitation, disques durs virtuels pour disques de données joints et un [modèle de gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) pour les nouveaux déploiements machine virtuelle. 

Pour créer des machines virtuelles à l’aide de l’image, configurer des ressources réseau pour chaque nouvelle machine virtuelle et utiliser le modèle (fichier JavaScript Object Notation ou JSON,) pour déployer des images capturées disque dur virtuel. De cette façon, vous pouvez répliquer une machine virtuelle avec sa configuration logicielle actuelle, similaire à celle que vous utilisez des images dans la Azure Marketplace.

>[AZURE.TIP]Si vous souhaitez créer une copie de votre VM Linux existant avec son état spécialisée pour la sauvegarde ou débogage, voir [créer une copie d’une machine virtuelle Linux s’exécutant sur Azure](virtual-machines-linux-copy-vm.md). Et si vous voulez télécharger un VHD Linux à partir d’un ordinateur virtuel en local, voir [télécharger et créer un Linux VM à partir de l’image de disque personnalisé](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Avant de commencer

Vous assurer que les conditions préalables suivantes :

* **Machine virtuelle azure créée dans le modèle de déploiement du Gestionnaire de ressources** - si vous n’avez pas créé un Linux VM, vous pouvez utiliser le [portail](virtual-machines-linux-quick-create-portal.md), [l’Infrastructure du langage commun Azure](virtual-machines-linux-quick-create-cli.md)ou les [modèles de gestionnaire de ressources](virtual-machines-linux-cli-deploy-templates.md). 

    Configurez la machine virtuelle selon vos besoins. Par exemple, [Ajouter des données disques](virtual-machines-linux-add-disk.md), appliquer des mises à jour et installer des applications. 
* **Azure infrastructure du langage commun** - installer [Azure infrastructure du langage commun](../xplat-cli-install.md) sur un ordinateur local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Étape 1 : Supprimer l’agent Linux Azure

Tout d’abord, exécutez la commande **waagent** avec le paramètre **d’annulation** sur la VM Linux. Cette commande supprime les fichiers et les données pour rendre la machine virtuelle prêt de GENERALISATION. Pour plus d’informations, voir le [guide de l’utilisateur Azure Linux Agent](virtual-machines-linux-agent-user-guide.md).

1. Se connecter à votre VM Linux à l’aide d’un clientSSH.

2. Dans la fenêtre SSH, tapez la commande suivante :

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Exécuter uniquement cette commande sur un ordinateur virtuel que vous souhaitez capturer en tant qu’image. Il ne garantit pas que l’image est désactivée de toutes les informations sensibles ou est approprié pour le redistribuer.

3. Tapez **y** pour continuer. Vous pouvez ajouter la **-forcer** paramètre afin d’éviter cette étape de confirmation.

4. Une fois la commande terminée, tapez **Quitter**. Cette étape ferme la clientSSH.

    
## <a name="step-2-capture-the-vm"></a>Étape 2 : Capturer la machine virtuelle

Utilisez l’infrastructure du langage commun Azure pour généralisez et capturer la machine virtuelle. Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre incluent **myResourceGroup**, **myVnet**et **myVM**.

5. Depuis votre ordinateur local, ouvrez l’infrastructure du langage commun Azure et [Connectez-vous à votre abonnement Azure](../xplat-cli-connect.md). 

6. Vérifiez que vous êtes en mode Gestionnaire de ressources.

    ```
    azure config mode arm
    ```

7. Arrêtez la machine virtuelle que vous avez annulé déjà à l’aide de la commande suivante :

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Généraliser la machine virtuelle avec la commande suivante :

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Maintenant exécutez la commande **machine virtuelle azure capturer** , qui capture la machine virtuelle. Dans l’exemple suivant, les disques durs virtuels image sont capturés dont le nom commence par **MyVHDNamePrefix**et l’option **-t** spécifie un chemin d’accès au modèle **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Ces fichiers image créés par défaut dans le même compte de stockage utilisée la machine virtuelle d’origine. Utiliser le *même compte de stockage* pour stocker les disques durs virtuels pour les nouvelles machines virtuelles que vous créez à partir de l’image. 

6. Pour rechercher l’emplacement d’une image capturée, ouvrez le modèle JSON dans un éditeur de texte. Dans la **storageProfile**, recherchez l' **uri** de l' **image** que se trouvant dans le conteneur **système** . Par exemple, l’URI de l’image de disque du système d’exploitation est similaire à`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Étape 3 : Créer une machine virtuelle à partir de l’image capturée
Maintenant utiliser l’image avec un modèle pour créer une VM Linux. Ces étapes montrent comment utiliser l’infrastructure du langage commun Azure et le modèle de fichier JSON que vous capturé pour créer la machine virtuelle dans un réseau virtuel.

### <a name="create-network-resources"></a>Créer des ressources de réseau

Pour utiliser le modèle, vous devez tout d’abord configurer un réseau virtuel et carte réseau de votre nouvelle machine virtuelle. Nous vous recommandons de que vous créez un groupe de ressources pour ces ressources à l’emplacement où se trouve votre image machine virtuelle. Exécuter des commandes similaires aux noms suivants, en substituant pour vos ressources et un emplacement Azure approprié (« centralus » dans ces commandes) :

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Obtenir l’Id de la carte réseau

Pour déployer une machine virtuelle à partir de l’image à l’aide de la JSON que vous avez enregistrés lors de la capture, vous devez l’Id de la carte réseau. Obtenir en exécutant la commande suivante :

    azure network nic show MyResourceGroup1 myNIC

L' **Id** dans le résultat est semblable à`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Créer une machine virtuelle
Maintenant exécutez la commande suivante pour créer votre machine virtuelle à partir de l’image capturée machine virtuelle. Utilisez le paramètre **-f** pour spécifier le chemin d’accès au fichier JSON modèle que vous avez enregistrés.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Dans le résultat de la commande, vous êtes invité à fournir un nouveau nom de la machine virtuelle, le nom d’utilisateur et mot de passe et l’Id de la carte réseau que vous avez créé précédemment.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

L’exemple suivant montre ce que vous voyez pour un déploiement réussi :

    + Paramètres et les configurations de modèle lors de l’initialisation
    + Création d’une info déploiement : créé xxxxxxx de déploiement de modèle
    + En attente de déploiement effectuer des données : DeploymentName : données MyDeployment : ResourceGroupName : données MyResourceGroup1 : ProvisioningState : a réussi des données : horodatage : données xxxxxxx : Mode : données incrémentielles : nom du Type de valeur


    data:    ------------------  ------------  -------------------------------------

    données : vmName chaîne myNewVM


    données : vmSize Standard_D1 de chaîne


    données : adminUserName chaîne myAdminuser


    données : adminPassword SecureString non défini


    données : networkInterfaceId informations /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic de chaîne : déploiement d’un groupe Créer commande OK

### <a name="verify-the-deployment"></a>Vérifier le déploiement

Maintenant SSH à la machine virtuelle que vous avez créé pour vérifier le déploiement et utilisez la nouvelle machine virtuelle. Pour vous connecter via le protocole SSH, accédez à l’adresse IP de la machine virtuelle que vous avez créé en exécutant la commande suivante :

    azure network public-ip show MyResourceGroup1 myIP

L’adresse IP est répertorié dans la sortie de commande. Par défaut, vous vous connectez à la VM Linux parSSH sur le port 22.

## <a name="create-additional-vms"></a>Créer des ordinateurs virtuels supplémentaires
Utiliser l’image capturée et le modèle pour déployer des machines virtuelles supplémentaires la procédure décrite dans la section précédente. Autres options pour créer des machines virtuelles à partir de l’image incluent à l’aide d’un modèle de démarrage rapide ou de l’exécution de la commande **créer machine virtuelle azure** .

### <a name="use-the-captured-template"></a>Utiliser le modèle capturé

Pour utiliser l’image capturée et le modèle, procédez comme suit (détaillé dans la section précédente) :

* Assurez-vous que votre image machine virtuelle se trouve dans le même compte de stockage qui héberge dur de votre ordinateur virtuel.
* Copiez le fichier JSON modèle et spécifiez un nom unique pour le disque du système d’exploitation du disque dur virtuel de la nouvelle machine virtuelle (ou des disques durs virtuels). Par exemple, dans le **storageProfile**, sous **disque dur virtuel**, dans **uri**, spécifiez un nom unique pour le disque dur virtuel, similaire à **osDisk**`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Créer une carte réseau dans la même ou un autre réseau virtuel.
* Utilisez le fichier JSON modèle modifié pour créer un déploiement dans le groupe de ressources dans lequel vous configurez le réseau virtuel.

### <a name="use-a-quickstart-template"></a>Utiliser un modèle de démarrage rapide

Si vous souhaitez que le réseau configuré automatiquement lorsque vous créez une machine virtuelle à partir de l’image, vous pouvez spécifier ces ressources dans un modèle. Par exemple, voir le [modèle 101 machine virtuelle d’utilisateur image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) à partir de GitHub. Ce modèle crée une machine virtuelle à partir de votre image personnalisée et le réseau virtuel nécessaire, adresse IP publique et ressources de carte réseau. Pour une procédure pas à pas de l’aide du modèle dans le portail Azure, Découvrez [comment créer une machine virtuelle à partir d’une image personnalisée à l’aide d’un modèle de gestionnaire de ressources](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Utiliser la machine virtuelle azure Créer commande

En règle générale, il est plus facile à utiliser un modèle de gestionnaire de ressources pour créer une machine virtuelle à partir de l’image. Toutefois, vous pouvez créer de la machine virtuelle _impérative_ à l’aide de la commande **créer machine virtuelle azure** avec **-Q** (**--image urn**) paramètre. Si vous utilisez cette méthode, vous passez également le paramètre **-d** (**--os-disque-dur**) pour spécifier l’emplacement du fichier .vhd du système d’exploitation pour la nouvelle machine virtuelle. Ce fichier doit être placé dans le conteneur de disques durs virtuels du compte de stockage où se trouve le fichier d’image disque dur virtuel. La commande copie le disque dur virtuel pour la nouvelle machine virtuelle automatiquement dans le conteneur de **disques durs virtuels** .

Avant d’exécuter **machine virtuelle azure créer** avec l’image, procédez comme suit :

1.  Créer un groupe de ressources, ou identifier un groupe de ressources existant pour le déploiement.

2.  Créer une ressource d’adresse IP publique et une ressource de carte réseau pour la nouvelle machine virtuelle. Pour créer un réseau virtuel, adresse IP publique et carte réseau à l’aide de l’infrastructure du langage commun, voir plus haut dans cet article. (**machine virtuelle azure créer** pouvez également créer une carte réseau, mais vous devez passer des paramètres supplémentaires pour un réseau virtuel et sous-réseau).


Puis exécuter une commande qui passe MU pour le nouveau fichier de disque dur virtuel du système d’exploitation et de l’image existante. Dans cet exemple, une taille de machine virtuelle Standard_A1 est créé dans la région des États-Unis Extrême-Orient.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Pour les options de commande supplémentaires, exécutez `azure help vm create`.

## <a name="next-steps"></a>Étapes suivantes

Pour gérer vos ordinateurs virtuels avec l’infrastructure du langage commun, consultez les tâches dans [déployer et gérer des machines virtuelles à l’aide de modèles Azure le Gestionnaire de ressources et l’infrastructure du langage commun Azure](virtual-machines-linux-cli-deploy-templates.md).
