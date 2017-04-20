<properties
    pageTitle="Déployer des modèles avec PowerShell dans Azure pile | Microsoft Azure"
    description="Découvrez comment déployer une machine virtuelle à l’aide d’un modèle de gestionnaire de ressources et PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Déployer des modèles dans la pile Azure à l’aide de PowerShell

Utiliser PowerShell pour déployer des modèles Azure le Gestionnaire de ressources sur le contrôle du concept pile Azure.  Gestionnaire de ressources modèles déployer et mise en service de toutes les ressources pour votre application dans une seule opération coordonnée.

## <a name="run-azurerm-powershell-cmdlets"></a>Exécuter les applets de commande AzureRM PowerShell

Dans cet exemple, vous exécutez un script pour déployer une machine virtuelle sur Azure pile du contact à l’aide d’un modèle de gestionnaire de ressources.  Avant de commencer, vérifiez que vous avez [installé et configuré PowerShell](azure-stack-connect-powershell.md)  

Le disque dur virtuel utilisé dans cet exemple de modèle est une image de marketplace par défaut (Windows Server 2012-R2-centre de données).

1.  Accédez à <http://aka.ms/AzureStackGitHub>, recherchez le modèle **101-simple-windows-machine virtuelle** , puis enregistrez-le dans l’emplacement suivant : c :\\modèles\\azuredeploy-101-simple-windows-vm.json.

2.  Dans PowerShell, exécutez le script de déploiement suivant. Remplacez le *nom d’utilisateur* et *mot de passe* par votre nom d’utilisateur et mot de passe. Sous utilisations suivantes, incrémenter la valeur pour le paramètre *$myNum* pour éviter d’écraser votre déploiement.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Ouvrez le portail pile Azure, cliquez sur **Parcourir**, cliquez sur **machines virtuelles**et consultez pour votre nouvelle machine virtuelle (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Exemple de vidéo : déploiement machine virtuelle hybride

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
