<properties
    pageTitle="Créer une image de la machine virtuelle à partir d’une machine virtuelle Azure | Microsoft Azure"
    description="Apprenez à créer une image de machine virtuelle GRG à partir d’un ordinateur de virtuel Azure existante créée dans le modèle de déploiement du Gestionnaire de ressources"
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
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle pour une machine virtuelle

Lorsque vous créez une machine virtuelle dans Azure à l’aide du portail ou PowerShell, un modèle de gestionnaire de ressources est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources dans un groupe de ressources. Pour une machine virtuelle, cela signifie que les conteneurs de modèles tout ce qui est créé à l’appui de la machine virtuelle dans ce groupe de ressources, y compris les ressources réseau.

## <a name="download-the-template-using-the-portal"></a>Télécharger le modèle à l’aide du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu concentrateur, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
5. Sélectionnez **script d’Automation**.
6. Sélectionnez **Télécharger** et enregistrez le fichier .zip sur votre ordinateur local.
7. Ouvrez le fichier .zip et extraire les fichiers dans un dossier. Le fichier .zip contiendra :
    
    - Deploy.ps1
    - Deploy.sh 
    - DEPLOYER.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

Le fichier .json se trouve le modèle.
    
## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell

Vous pouvez également télécharger le fichier de modèle .json à l’aide de l’applet de commande [Exporter AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) . Vous pouvez utiliser la `-path` paramètre pour fournir le nom de fichier et le chemin du fichier .json. Cet exemple montre comment télécharger le modèle pour le groupe de ressources nommé **myResourceGroup** dans le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le déploiement des ressources à l’aide de modèles, voir [Présentation du modèle Gestionnaire de ressources](../resource-manager-template-walkthrough.md).