<properties 
    pageTitle="Azure PowerShell avec le Gestionnaire de ressources | Microsoft Azure" 
    description="Introduction à l’utilisation de PowerShell Azure à déployer plusieurs ressources comme un groupe de ressources à Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Portail](azure-portal/resource-group-portal.md) 
- [Azure infrastructure du langage commun](xplat-cli-azure-resource-manager.md)
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [API REST](resource-manager-rest-api.md)


Le Gestionnaire de ressources Azure met en œuvre une approche moderne pour le contrôle du cycle de vie de ressources Azure. Au lieu de création et la gestion des ressources individuelles, vous commencez par imaginant pour une solution complète, par exemple un blog, une galerie de photos, un portail SharePoint ou un site wiki. Un modèle--une représentation déclarative de la solution--vous permet de définir un groupe de ressources qui contient toutes les ressources que nécessaires pour prendre en charge de la solution. Ensuite, vous déployez et gérez ce groupe de ressources comme une unité logique. 

Dans ce didacticiel, vous allez apprendre à utiliser PowerShell Azure avec le Gestionnaire de ressources Azure. Il vous guide dans le processus de déploiement d’une solution et l’utilisation de cette solution. Vous allez utiliser PowerShell Azure et un modèle de gestionnaire de ressources pour déployer :

- SQL server – pour héberger la base de données
- Base de données SQL - pour stocker les données
- Règles de pare-feu - pour permettre à l’application web pour se connecter à la base de données
- Plan de Service d’application - permettant de définir les fonctionnalités et le coût de l’application web
- Site Web - pour l’exécution de l’application web
- Configuration de la Web - pour le stockage de la chaîne de connexion à la base de données 
- Règles d’alerte - pour surveiller les performances et les erreurs
- Analyse de l’application - pour les paramètres d’échelle automatique

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez :

- Un compte Azure
  + Vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F): pouvez-vous crédits vous pouvez utiliser pour essayer de services Azure payants et même après leur utilisation jusqu'à vous pouvez conserver le compte et libérer de l’utilisation des services Azure, tels que des sites Web. Votre carte de crédit est jamais facturée, sauf si vous explicitement de modifiez vos paramètres et lui demander de vous être facturés.
  
  + Vous pouvez [Activer les avantages d’abonné MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN votre abonnement fournit les crédits chaque mois que vous pouvez utiliser pour les services Azure payants.
- Azure PowerShell 1.0. Pour plus d’informations sur cette version et apprendre à installer, voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md).

Ce didacticiel est conçu pour les débutants PowerShell, mais elle suppose que vous comprenez les concepts de base, telles que les modules, les applets de commande et les sessions.

## <a name="get-help-for-cmdlets"></a>Obtenir de l’aide pour les applets de commande

Pour obtenir une aide détaillée sur une applet de commande que vous voyez dans ce didacticiel, utilisez l’applet de commande Get-Help. 

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide de l’applet de commande Get-AzureRmResource, tapez :

    Get-Help Get-AzureRmResource -Detailed

Pour obtenir une liste des applets de commande dans le module de ressources avec un résumé de l’aide, tapez : 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Le résultat ressemblera à l’extrait suivant :

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Pour obtenir une aide complète pour une applet de commande, tapez une commande avec la mise en forme :

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Connectez-vous à votre compte Azure

Avant de travailler sur votre solution, vous devez vous connecter à votre compte.

Pour se connecter à votre compte Azure, utilisez l’applet de commande **Add-AzureRmAccount** .

    Add-AzureRmAccount

L’applet de commande vous demande les informations d’identification de connexion pour votre compte Azure. Après la connexion, il télécharge vos paramètres de compte afin qu’elles soient disponibles à Azure PowerShell. 

Les paramètres du compte, afin que vous devez actualiser les occasionnellement à expiration. Pour actualiser les paramètres du compte, exécutez à nouveau **Ajouter AzureRmAccount** . 

>[AZURE.NOTE] Les modules du Gestionnaire de ressources nécessite ajouter AzureRmAccount. Un fichier de paramètres de publication n’est pas suffisant.     

Si vous avez plusieurs abonnements, fournissent l’id de l’abonnement que vous souhaitez utiliser pour le déploiement avec l’applet de commande **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de déployer les ressources à votre abonnement, vous devez créer un groupe de ressources contenant les ressources. 

Pour créer un groupe de ressources, utilisez l’applet de commande **New-AzureRmResourceGroup** .

La commande utilise le paramètre de **nom** pour spécifier un nom pour le groupe de ressources et le paramètre **d’emplacement** indiquer son emplacement. En fonction de ce que nous avons découvert dans la section précédente, nous allons utiliser « US ouest » à l’emplacement.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
Le résultat sera semblable à :

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Votre groupe de ressources a été créé.

## <a name="deploy-your-solution"></a>Déployez votre solution

Cette rubrique n’affiche pas comment créer un modèle ou discuter la structure du modèle. Pour obtenir ces informations, voir [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md) et [Présentation du modèle Gestionnaire de ressources](resource-manager-template-walkthrough.md). Vous déployez le modèle de [mise en service une application Web avec une base de données SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) prédéfini à partir de [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/).

Vous avez votre groupe de ressources et vous disposez de votre modèle, afin que vous êtes maintenant prêt à déployer l’infrastructure défini dans votre modèle au groupe de ressources. Déployer des ressources à l’aide de l’applet de commande **New-AzureRmResourceGroupDeployment** . Le modèle spécifie le nombre de valeurs par défaut, que nous utiliserons afin que vous n’avez pas besoin fournir des valeurs pour ces paramètres. La syntaxe de la base ressemble à :

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Vous indiquez le groupe de ressources et l’emplacement du modèle. Si votre modèle est un fichier local, vous utilisez le paramètre **- TemplateFile** et spécifiez le chemin d’accès au modèle. Vous pouvez définir la **-Mode** paramètre **incrémentiel** ou **complète**. Par défaut, le Gestionnaire de ressources effectue une mise à jour incrémentiel au cours du déploiement ; Par conséquent, il n’est pas indispensable pour définir **-Mode** auxquelles vous voulez **incrémentiel**. Pour mieux comprendre les différences entre ces modes de déploiement, voir [déployer une application avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Paramètres de modèle dynamique

Si vous êtes habitué à PowerShell, vous savez que vous pouvez passer en revue les paramètres disponibles pour une applet de commande en tapant un signe moins (-) et en appuyant sur la touche TAB. Cette fonctionnalité même fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous tapez le nom du modèle, l’applet de commande extrait le modèle, l’analyse et ajoute dynamiquement les paramètres de modèle à la commande. Cela facilite spécifier les valeurs de paramètre de modèle.

Lorsque vous entrez la commande, vous êtes invité au paramètre obligatoire manquant, **administratorLoginPassword**. Puis, lorsque vous tapez le mot de passe, la valeur de chaîne sécurisée est masquée. Cette stratégie élimine le risque de fournir un mot de passe au format texte brut.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si le modèle inclut un paramètre avec un nom qui correspond à l’un des paramètres de la commande pour déployer le modèle (par exemple, y compris un paramètre nommé **ResourceGroupName** dans votre modèle qui est le même que le paramètre **ResourceGroupName** dans l’applet de commande [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) ), vous devrez fournir une valeur pour un paramètre avec le suffixe **modèle** (par exemple, **ResourceGroupNameFromTemplate**). En règle générale, vous devez éviter cette confusion par nommer ne pas les paramètres du même nom en tant que paramètres utilisés pour les opérations de déploiement.

La commande s’exécute et retourne des messages comme les ressources sont créées. Pour finir, vous obtenez le résultat de votre déploiement.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

En quelques étapes, nous avons créé et déployé les ressources nécessaires pour un site Web complexe. 

### <a name="log-debug-information"></a>Informations de débogage journal

Lorsque vous déployez un modèle, vous pouvez vous connecter en spécifiant le paramètre **- DeploymentDebugLogLevel** lors de l’exécution de **Nouveau AzureRmResourceGroupDeployment**plus d’informations sur la demande et réponse. Ces informations peuvent vous aider à résoudre les erreurs de déploiement. La valeur par défaut est **Aucun** ce qui signifie qu’aucune demande ou contenu de la réponse n’est connecté. Vous pouvez spécifier la journalisation le contenu à partir de la demande, réponse ou les deux.  Pour plus d’informations sur la résolution des problèmes de déploiement et la journalisation des informations de débogage, voir [déploiements de groupe de ressources de résolution des problèmes avec Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). L’exemple suivant enregistre le contenu de la demande et le contenu de la réponse pour le déploiement.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Lorsque vous définissez le paramètre DeploymentDebugLogLevel, avec soin le type d’informations que vous passez dans pendant le déploiement. En enregistrant les informations relatives à la demande ou une réponse, vous pouvez potentiellement exposer des données sensibles sont extraite via les opérations de déploiement. 


## <a name="get-information-about-your-resource-groups"></a>Obtenir des informations sur vos groupes de ressources

Après avoir créé un groupe de ressources, vous pouvez utiliser les applets de commande dans le module Gestionnaire de ressources pour gérer vos groupes de ressources.

- Pour obtenir un groupe de ressources dans votre abonnement, utilisez l’applet de commande **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Qui renvoie les informations suivantes :

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Si vous ne spécifiez pas un nom de groupe de ressources, l’applet de commande renvoie tous les groupes de ressources dans votre abonnement.

- Pour obtenir les ressources dans le groupe de ressources, utilisez l’applet de commande **Rechercher AzureRmResource** et son paramètre **ResourceGroupNameContains** . Sans paramètres, rechercher AzureRmResource Obtient toutes les ressources dans votre abonnement Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Qui renvoie une liste de ressources formatée comme :
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- Vous pouvez utiliser les balises logiquement organiser les ressources dans votre abonnement, et de récupérer des ressources avec les applets de commande **Rechercher AzureRmResource** et **Rechercher AzureRmResourceGroup** .

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Ajouter à un groupe de ressources

Pour ajouter une ressource au groupe de ressources, vous pouvez utiliser l’applet de commande **New-AzureRmResource** . Toutefois, l’ajout d’une ressource de cette manière peut-être provoquer confusion future parce que la nouvelle ressource n’existe pas dans votre modèle. Si vous avez ré-déployé l’ancien modèle, vous le feriez déployez une solution incomplète. Si vous déployez souvent, vous trouverez plus simple et plus fiable pour ajouter la nouvelle ressource à votre modèle et déployez de nouveau.

## <a name="move-a-resource"></a>Déplacer une ressource

Vous pouvez déplacer des ressources existantes à un nouveau groupe de ressources. Pour obtenir des exemples, voir [Déplacer des ressources pour le nouveau groupe de ressources ou d’abonnement](resource-group-move-resources.md).

## <a name="export-template"></a>Modèle d’exportation

Pour un groupe de ressources existant (déployé via PowerShell ou de l’une des autres méthodes tels que le portail), vous pouvez afficher le modèle de gestionnaire de ressources du groupe de ressources. Exporter le modèle offre deux avantages :

1. Vous pouvez facilement automatiser les déploiements à venir de la solution comme de l’infrastructure est définie dans le modèle.

2. Vous pouvez vous familiariser avec la syntaxe de modèle en recherchant à la Notation JSON (JavaScript Object) qui représente votre solution.

Via PowerShell, vous pouvez générer un modèle qui représente l’état actuel de votre groupe de ressources ou récupérer le modèle qui a été utilisé pour un déploiement particulier.

Exporter le modèle pour un groupe de ressources est utile lorsque vous avez apporté des modifications à un groupe de ressources et que vous avez besoin de récupérer la représentation JSON de son état actuel. Toutefois, le modèle généré contient uniquement un nombre minimal de paramètres et aucune variable. La plupart des valeurs dans le modèle est codé en dur. Avant de déployer le modèle généré, vous souhaiterez peut-être plusieurs valeurs convertir des paramètres afin que vous pouvez personnaliser le déploiement pour différents environnements.

Exporter le modèle pour un déploiement particulier est utile lorsque vous avez besoin de consulter le modèle réel qui a été utilisé pour déployer des ressources. Le modèle doit inclure tous les paramètres et les variables définies pour le déploiement d’origine. Toutefois, si une personne de votre organisation a apporté des modifications au groupe de ressources en dehors de ce qui est défini dans le modèle, ce modèle représente pas l’état actuel du groupe de ressources.

> [AZURE.NOTE] La fonctionnalité de modèle d’exportation est dans l’aperçu, et pas tous les types de ressource pris en charge l’exportation d’un modèle. Lorsque vous tentez d’exporter un modèle, vous pouvez voir un message d’erreur indiquant que certaines ressources n’ont pas été exportés. Si nécessaire, vous pouvez définir manuellement ces ressources dans votre modèle après son téléchargement.

###<a name="export-template-from-resource-group"></a>Exporter un modèle à partir de groupe de ressources

Pour afficher le modèle pour un groupe de ressources, exécutez l’applet de commande **Exporter AzureRmResourceGroup** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Télécharger le modèle de déploiement

Pour télécharger le modèle utilisé pour un déploiement particulier, exécutez l’applet de commande **Enregistrer AzureRmResourceGroupDeploymentTemplate** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Supprimer des ressources ou groupe de ressources

- Pour supprimer une ressource du groupe de ressources, utilisez l’applet de commande **Supprimer AzureRmResource** . Cette applet de commande supprime la ressource, mais ne supprime pas le groupe de ressources.

    Cette commande supprime le site Web TestSite du groupe de ressources TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Pour supprimer un groupe de ressources, utilisez l’applet de commande **Supprimer AzureRmResourceGroup** . Cette applet de commande supprime le groupe de ressources et à ses ressources.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    Vous êtes invité à confirmer la suppression.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script de déploiement

Les exemples de déploiement antérieures dans cette rubrique a montré uniquement les applets de commande individuels nécessaires pour déployer des ressources sur Azure. L’exemple suivant montre un script de déploiement qui crée le groupe de ressources et déploie les ressources.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles de gestionnaire de ressources, voir [Création de modèles de gestionnaire de ressources Azure](./resource-group-authoring-templates.md).
- Pour savoir comment déployer des modèles, voir [déployer une application avec Azure Gestionnaire de ressources du modèle](./resource-group-template-deploy.md).
- Pour obtenir un exemple détaillé de déploiement d’un projet, voir [déployer microservices mis dans Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Pour en savoir plus sur la résolution des problèmes d’un déploiement qui a échoué, voir [déploiements de groupe de ressources de résolution des problèmes dans Azure](./resource-manager-troubleshoot-deployments-powershell.md).

