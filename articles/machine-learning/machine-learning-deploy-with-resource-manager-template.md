<properties
    pageTitle="Déploiement d’apprentissage espace de travail à l’aide d’Azure modèle Gestionnaire de ressources de l’ordinateur | Microsoft Azure"
    description="Comment déployer un espace de travail pour apprentissage automatique Azure à l’aide du Gestionnaire de ressources Azure modèle"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Déploiement d’apprentissage à l’aide du Gestionnaire de ressources Azure un espace de travail de l’ordinateur

## <a name="introduction"></a>Introduction
À l’aide d’un modèle de déploiement de gagne du temps de vous en vous proposant un moyen scalable le Gestionnaire de ressources Azure déployer interconnectés avec une validation et réessayer de mécanisme. Pour définir des espaces de travail apprentissage Azure Machine, par exemple, vous devez tout d’abord configurer un compte de stockage Azure, puis déployez votre espace de travail. Imaginons l’ajouter manuellement des centaines d’espaces de travail. Alternative plus simple consiste à utiliser un modèle Azure le Gestionnaire de ressources pour déployer un espace de travail Azure Machine formation et toutes ses dépendances. Cet article vous permet d’accéder à cette procédure pas à pas. Pour une vue d’ensemble du Gestionnaire de ressources Azure, voir [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Étape par étape : créer un espace de travail formation Machine
Nous allez créer un groupe de ressources Azure, puis déployer un nouveau compte de stockage Azure et un nouvel espace de formation Machine Azure à l’aide d’un modèle de gestionnaire de ressources. Une fois que le déploiement est terminé, nous s’imprimera des informations importantes sur les espaces de travail qui ont été créées (la clé primaire, le workspaceID et l’URL de l’espace de travail).

### <a name="create-an-azure-resource-manager-template"></a>Créer un modèle de gestionnaire de ressources Azure
Un espace de travail formation ordinateur requiert un compte de stockage Azure pour stocker le jeu de données lié.
Le modèle suivant utilise le nom du groupe de ressources pour générer le nom de compte de stockage et le nom de l’espace de travail.  Elle utilise également le nom de compte de stockage en tant que propriété lors de la création de l’espace de travail.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Enregistrer ce modèle en tant que fichier mlworkspace.json sous c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Déployer le groupe de ressources, basé sur le modèle
* PowerShell Open
* Installer des modules pour le Gestionnaire de ressources Azure et gestion des services Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Ces étapes téléchargement et installez les modules nécessaires pour effectuer les étapes restantes. Cette opération ne doit être effectuée qu’une fois dans l’environnement où vous exécutez les commandes PowerShell.   

* Authentifier vers Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Cette étape doit être répété pour chaque session. Une fois authentifié, les informations de votre abonnement doivent s’afficher.

![Compte Azure][1]

Maintenant que nous avons accès à Azure, nous pouvons créer le groupe de ressources.

* Créer un groupe de ressources

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Vérifiez que le groupe de ressources est correctement mis en service. **ProvisioningState** doit être « réussite ».
Le nom du groupe ressources est utilisé par le modèle pour générer le nom de compte de stockage. Le nom de compte de stockage doit être comprise entre 3 et 24 caractères et utiliser des nombres et les lettres minuscules uniquement.

![Groupe de ressources][2]

* À l’aide du déploiement du groupe ressources, déployer un nouvel espace de travail de formation Machine.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Une fois le déploiement est terminé, il est simple pour accéder aux propriétés de l’espace de travail que vous avez déployé. Par exemple, vous pouvez accéder le jeton de clé primaire.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Autre procédure pour récupérer les jetons d’espace de travail existant consiste à utiliser la commande appeler AzureRmResourceAction. Par exemple, vous pouvez répertorier les jetons principales et secondaires de tous les espaces de travail.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Une fois que l’espace de travail est configuré, vous pouvez également automatiser plusieurs tâches Azure Machine apprentissage Studio l’utilisation du [PowerShell Module pour apprentissage automatique Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Étapes suivantes 
* En savoir plus sur les [Modèles de gestionnaire de ressources Azure création](../resource-group-authoring-templates.md). 
* Avoir un aperçu du [Référentiel de modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates). 
* Regardez cette vidéo sur [Le Gestionnaire de ressources Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
