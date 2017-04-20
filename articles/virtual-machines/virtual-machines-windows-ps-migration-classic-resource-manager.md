<properties
    pageTitle="Migrer vers le Gestionnaire de ressources avec PowerShell | Microsoft Azure"
    description="Cet article décrit la migration plateforme prise en charge des ressources IaaS de classique au Gestionnaire de ressources Azure à l’aide des commandes PowerShell Azure"
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
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de PowerShell Azure

Ces étapes vous montrent comment utiliser les commandes PowerShell Azure pour migrer infrastructure comme une ressource de service (IaaS) à partir du modèle de déploiement classique dans le modèle de déploiement d’Azure le Gestionnaire de ressources. 

Si vous le souhaitez, vous pouvez également déplacer des ressources à l’aide de l' [Interface de ligne de commande Azure (Azure infrastructure du langage commun)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

- Pour l’arrière-plan sur des scénarios de migration pris en charge, voir [migration plateforme prise en charge de ressources IaaS classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md). 
- Pour des instructions détaillées et la procédure de migration, voir [techniques détaillées sur plate-forme prise en charge la migration de classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## <a name="step-1-plan-for-migration"></a>Étape 1 : Planifier la migration

Voici quelques recommandations que nous vous recommandons lorsque vous évaluez migration IaaS des ressources à partir de classique au Gestionnaire de ressources :

- Lire les [configurations et fonctionnalités pris en charge et non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md). Si vous avez machines virtuelles qui utilisent des configurations non prises en charge ou fonctionnalités, nous vous recommandons d’attendre la prise en charge de fonctionnalité/configuration à annoncé. Par ailleurs, si elle ne répond à vos besoins, supprimez cette fonctionnalité ou retirer cette configuration permettant la migration.
-   Si vous avez automatisée scripts que déploient votre infrastructure et vos applications aujourd'hui, essayez de créer une configuration de test similaire à l’aide de ces scripts pour la migration. Par ailleurs, vous pouvez configurer les environnements exemple à l’aide du portail Azure.

>[AZURE.IMPORTANT]Passerelles réseau virtuel (-sites, Azure ExpressRoute, passerelle d’application, point-à-site) ne sont actuellement pas pris en charge pour la migration de classique au Gestionnaire de ressources. Pour migrer un réseau virtuel classique avec une passerelle, supprimez la passerelle avant d’exécuter une opération de validation pour déplacer le réseau (vous pouvez exécuter l’étape préparer sans supprimer la passerelle). Une fois la migration terminée, vous reconnecter la passerelle dans le Gestionnaire de ressources Azure.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Étape 2 : Installer la dernière version de PowerShell Azure

Il existe deux options principales d’installation Azure PowerShell : [Galerie PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). WebPI reçoit les mises à jour mensuelles. Galerie de PowerShell reçoit les mises à jour en permanence. Cet article est basé sur Azure PowerShell version2.1.0.

Pour obtenir des instructions d’installation, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>Étape 3 : Définir votre abonnement et inscrire pour la migration

Tout d’abord, démarrez une invite PowerShell. Pour la migration, vous devez configurer votre environnement pour les deux classique et Gestionnaire de ressources.

Connectez-vous à votre compte pour le modèle de gestionnaire de ressources.

```powershell
    Login-AzureRmAccount
```

Obtenir les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définir votre abonnement Azure pour la session active. Cet exemple définit le nom de l’abonnement par défaut à **Mon abonnement Azure**. Remplacez le nom de l’abonnement exemple par votre propre. 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] L’inscription est une étape unique, mais vous devez le faire une fois avant d’essayer de migration. Sans l’enregistrer, vous voyez le message d’erreur suivant : 

>   *BadRequest : Abonnement n’est pas enregistré pour la migration.* 

Inscrire avec le fournisseur de ressources de migration à l’aide de la commande suivante :

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Patientez cinq minutes pour l’enregistrement terminer. Vous pouvez vérifier l’état de l’approbation à l’aide de la commande suivante :

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vérifiez que RegistrationState est `Registered` avant de poursuivre. 

Connectez-vous à votre compte pour le modèle classique.

```powershell
    Add-AzureAccount
```

Obtenir les abonnements disponibles à l’aide de la commande suivante :

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définir votre abonnement Azure pour la session active. Cet exemple montre l’abonnement par défaut à **Mon abonnement Azure**. Remplacez le nom de l’abonnement exemple par votre propre. 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Étape 4 : Vérifiez que vous disposez de suffisamment cœurs Machine virtuelle de gestionnaire de ressources Azure dans la zone Azure de votre déploiement actuel ou VNET

Vous pouvez utiliser la commande PowerShell suivante pour vérifier le nombre actuel de cœurs que dans le Gestionnaire de ressources Azure. Pour en savoir plus sur les quotas de base, voir [le Gestionnaire de ressources Azure et limites](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager). 

Cet exemple vérifie la disponibilité de la région **États-Unis Ouest** . Remplacez le nom de la région exemple par votre propre. 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Étape 5 : Exécuter les commandes pour migrer vos ressources IaaS

>[AZURE.NOTE] Toutes les opérations décrites ici sont idempotents. Si vous rencontrez un problème différent une fonctionnalité non prise en charge ou une erreur de configuration, nous vous recommandons de relancer la préparer, l’annulation ou validation opération. La plateforme essaie ensuite l’action à nouveau.

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Migrer les machines virtuelles dans un service cloud (et non dans un réseau virtuel)

Obtenir la liste des services cloud à l’aide de la commande suivante, puis puis sélectionnez le service en nuage que vous voulez migrer. Si les ordinateurs virtuels dans le service cloud sont dans un réseau virtuel ou s’ils ont des rôles web ou collaborateur, la commande renvoie un message d’erreur.

```powershell
    Get-AzureService | ft Servicename
```

Obtenir le nom de déploiement pour le service cloud. Dans cet exemple, le nom du service est **Mon Service**. Remplacez l’exemple de nom de service par votre propre nom de service. 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Préparer les machines virtuelles dans le service cloud pour la migration. Vous avez deux possibilités pour choisir parmi.

* **Option 1. Migrer les ordinateurs virtuels à un réseau virtuel créé plateforme**

    Valider tout d’abord, si vous pouvez migrer le service cloud à l’aide des commandes suivantes :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et les erreurs qui bloquent la migration. Si la validation est réussie, vous pouvez déplacer sur à l’étape de **préparation** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **Option 2. Migrer à un réseau virtuel existant dans le modèle de déploiement du Gestionnaire de ressources**

    Cet exemple définit le nom du groupe ressource **myResourceGroup**, le nom du réseau virtuel pour **myVirtualNetwork** et le nom du sous-réseau pour **mySubNet**. Remplacez les noms dans l’exemple par les noms des ressources de votre propre.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Valider tout d’abord, si vous pouvez migrer le service cloud à l’aide de la commande suivante :

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    La commande ci-dessus affiche les avertissements et les erreurs qui bloquent la migration. Si la validation est réussie, vous pouvez passer à l’étape de préparer suivant :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

Une fois l’opération de préparation établie avec une des options précédentes, demander l’état de migration d’ordinateurs virtuels. Assurez-vous qu’ils sont dans le `Prepared` état.

Cet exemple définit le nom de la machine virtuelle sur **myVM**. Remplacez le nom de l’exemple avec votre propre nom de la machine virtuelle.

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

Vérifiez la configuration pour les ressources préparés à l’aide de PowerShell ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>Migrer les machines virtuelles dans un réseau virtuel

Pour migrer des machines virtuelles dans un réseau virtuel, vous déplacer le réseau. Machines virtuelles automatiquement migrées avec le réseau. Choisissez le réseau virtuel que vous voulez migrer. 

Cet exemple définit le nom du réseau virtuel sur **myVnet**. Remplacez le nom de réseau virtuel exemple avec votre propre. 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] Si le réseau virtuel contient web ou rôles collaborateur ou machines virtuelles avec des configurations non prises en charge, vous obtenez un message d’erreur de validation.

Valider tout d’abord, si vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

La commande ci-dessus affiche les avertissements et les erreurs qui bloquent la migration. Si la validation est réussie, vous pouvez passer à l’étape de préparer suivant :

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Vérifiez la configuration pour les ordinateurs virtuels préparés à l’aide de PowerShell Azure ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>Migrer un compte de stockage

Une fois que vous avez terminé les machines virtuelles la migration, nous vous recommandons de vous migrez les comptes de stockage.

Préparer chaque compte de stockage pour la migration à l’aide de la commande suivante. Dans cet exemple, le nom de compte de stockage est **myStorageAccount**. Remplacez le nom de l’exemple avec le nom de votre compte de stockage. 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Vérifiez la configuration du compte de stockage préparé à l’aide de PowerShell Azure ou le portail Azure. Si vous n’êtes pas prêt pour la migration et que vous voulez revenir à l’ancien état, utilisez la commande suivante :

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Si la configuration préparée de test vous convient, vous pouvez déplacer vers l’avant et valider les ressources à l’aide de la commande suivante :

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la migration, voir [migration plateforme prise en charge de ressources IaaS classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md).
- Pour migrer les ressources réseau supplémentaires au Gestionnaire de ressources à l’aide de PowerShell Azure, suivez les étapes similaires avec [Déplacement AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx), [Déplacer AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)et [Déplacer AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx).
- Pour les scripts open source que vous pouvez utiliser pour déplacer des ressources Azure de classique au Gestionnaire de ressources, voir [Outils de la Communauté pour la migration vers le Gestionnaire de ressources Azure migration](virtual-machines-windows-migration-scripts.md)
