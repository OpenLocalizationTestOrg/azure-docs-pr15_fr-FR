<properties
    pageTitle="Commandes PowerShell basée sur le Gestionnaire de ressources Azure Azure Web App | Microsoft Azure"
    description="Découvrez comment utiliser les nouvelles commandes basées sur le Gestionnaire de ressources Azure PowerShell pour gérer vos applications Web Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>À l’aide de PowerShell basée sur le Gestionnaire de ressources Azure pour gérer les applications Web Azure#

> [AZURE.SELECTOR]
- [Azure infrastructure du langage commun](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Avec Microsoft Azure PowerShell version 1.0.0 nouvelles commandes ont été ajoutées, qui permettent l’utilisateur à utiliser les commandes basées sur le Gestionnaire de ressources Azure PowerShell pour gérer les applications Web.

Pour en savoir plus sur la gestion des groupes de ressources, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md). 

Pour en savoir plus sur la liste complète des paramètres et options pour les applets de commande PowerShell, voir la [Référence applet de commande complète des applets de commande PowerShell basée sur le Gestionnaire de ressources Azure Web App](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Plans de gestion du Service d’application ##

### <a name="create-an-app-service-plan"></a>Créer un Plan de services d’application ###
Pour créer un plan de services d’application, utilisez l’applet de commande **New-AzureRmAppServicePlan** .

Voici les descriptions des paramètres différents :

-   **Nom**: nom de l’offre de service d’application.
-   **Emplacement**: emplacement de plan de service.
-   **ResourceGroupName**: groupe de ressources qui inclut le plan de services d’application nouvellement créé.
-   **Niveau**: le niveau de tarification souhaité (valeur par défaut est disponible, les autres options sont partagés, basique, Standard et Premium).
-   **WorkerSize**: la taille des travailleurs (valeur par défaut est petite si le paramètre de niveau a été spécifié en tant que Basic, Standard ou Premium. Autres options sont les moyennes et grandes.)
-   **NumberofWorkers**: le nombre de travailleurs de l’application de service plan (valeur par défaut est 1). 

Exemple pour utiliser cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Créer un Plan de services d’application dans un environnement de Service d’application ###
Pour créer un plan de services d’application dans un environnement de service d’application, utilisez la même commande **New-AzureRmAppServicePlan** commande avec des paramètres supplémentaires pour spécifier le nom de l’ASE et nom de groupe de ressources de ASE.

Exemple pour utiliser cette applet de commande :

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Pour en savoir plus sur l’environnement de service d’application, consultez [présentation environnement de Service d’application](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Répertorier les Plans de Service d’application existant ###

Pour répertorier les plans de services d’application existant, utilisez l’applet de commande **Get-AzureRmAppServicePlan** .

Pour répertorier toutes les offres de service d’application sous votre abonnement, utilisez : 

    Get-AzureRmAppServicePlan

Pour répertorier toutes les offres de service d’application sous un groupe de ressources spécifiques, utilisez :

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir un plan de services d’application spécifique, utilisez :

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurer un Plan de Service application existant ###

Pour modifier les paramètres d’un plan de service d’application existant, utilisez l’applet de commande **Set-AzureRmAppServicePlan** . Vous pouvez modifier le niveau, la taille de travail et le nombre de collaborateurs 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Mise à l’échelle d’un Plan de services d’application ####

Pour redimensionner un Plan de Service application existante, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Modifier la taille de travail d’un Plan de Service d’application ####

Pour modifier la taille des travailleurs dans un Plan de Service application existante, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Modifier le niveau d’un Plan de Service d’application ####

Pour modifier le niveau d’un Plan de Service application existante, utilisez :

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Supprimer un Plan de Service application existant ###

Pour supprimer un plan de service d’application existant, toutes les applications web affectées doivent être déplacé ou supprimé en premier. Puis en utilisant l’applet de commande **Supprimer AzureRmAppServicePlan** vous pouvez supprimer le plan de services d’application.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gestion des applications Web de Service d’application ##

### <a name="create-a-web-app"></a>Créer une application Web ###

Pour créer une application web, utilisez l’applet de commande **New-AzureRmWebApp** .

Voici les descriptions des paramètres différents :

- **Nom**: nom de l’application web.
- **AppServicePlan**: nom pour le plan de service utilisé pour héberger l’application web.
- **ResourceGroupName**: groupe de ressources qui héberge le plan de services d’application.
- **Emplacement**: l’emplacement de l’application web.

Exemple pour utiliser cette applet de commande :

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Créer une application Web dans un environnement de Service d’application ###

Pour créer une application web dans un environnement de Service de l’application (ASE). Utilisez la même commande **New-AzureRmWebApp** avec des paramètres supplémentaires pour spécifier le nom ASE et le nom du groupe de ressources auquel appartient la ASE.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Pour en savoir plus sur l’environnement de service d’application, consultez [présentation environnement de Service d’application](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Supprimer une application Web existante ###

Pour supprimer une application web existant que vous pouvez utiliser l’applet de commande **Supprimer AzureRmWebApp** , vous devez spécifier le nom de l’application web et le nom de groupe de ressources.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Liste des applications Web existantes ###

Pour répertorier les applications web existant, utilisez l’applet de commande **Get-AzureRmWebApp** .

Pour répertorier toutes les applications web sous votre abonnement, utilisez :

    Get-AzureRmWebApp

Pour répertorier toutes les applications web sous un groupe de ressources spécifiques, utilisez :

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez :

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurer une application Web existante ###

Pour modifier les paramètres et configurations pour une application web existante, utilisez l’applet de commande **Set-AzureRmWebApp** . Pour une liste complète des paramètres, vérifiez le [lien de référence applet de commande](https://msdn.microsoft.com/library/mt652487.aspx)

Exemple (1) : utilisez cette applet de commande pour modifier des chaînes de connexion

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemple (2) : ajouter ou modifier les paramètres de l’application

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemple (3) : configurer l’application web pour s’exécuter en mode 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Modifier l’état d’une application Web existante ###

#### <a name="restart-a-web-app"></a>Redémarrer une application web ####

Pour reprendre une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrêter une application web ####

Pour arrêter une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Démarrer une application web ####

Pour démarrer une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gérer les profils de publication Web App ###

Chaque application web a un profil de publication qui peut être utilisé pour publier vos applications, plusieurs opérations peuvent être exécutées sur les profils de publication.

#### <a name="get-publishing-profile"></a>Disposez d’un profil de publication ####

Pour obtenir le profil de publication pour une application web, utilisez :

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Cette commande renvoie le profil de publication à la ligne de commande en tant que sortie également le profil de publication à un fichier texte.

#### <a name="reset-publishing-profile"></a>Réinitialiser le profil de publication ####

Pour les deux réinitialiser le mot de passe publication FTP et web déployer pour une application web, utilisez :

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gérer les certificats d’application Web ###

Pour en savoir plus sur la gestion des certificats web app, voir [liaison de certificats SSL à l’aide de PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Étapes suivantes ###
- Pour en savoir plus sur prise en charge du Gestionnaire de ressources Azure PowerShell, voir [utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure.](../powershell-azure-resource-manager.md)
- Pour connaître les environnements de Service d’application, voir [Introduction à l’environnement de Service d’application.](app-service-app-service-environment-intro.md)
- Pour en savoir plus sur la gestion des certificats SSL de Service d’application à l’aide de PowerShell, voir [liaison de certificats SSL à l’aide de PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Pour en savoir plus sur la liste complète des applets de commande PowerShell basée sur le Gestionnaire de ressources Azure d’Azure Web Apps, voir [Azure applet de commande référence de Web applications Azure Gestionnaire de ressources PowerShell Cmdlets.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Pour en savoir plus sur la gestion du Service d’application à l’aide d’infrastructure du langage commun, voir [Using Azure Resource Manager-Based XPlat CLI pour Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
