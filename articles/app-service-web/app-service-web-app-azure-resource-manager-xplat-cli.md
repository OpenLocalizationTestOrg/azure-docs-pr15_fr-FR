<properties
    pageTitle="Outils de ligne de commande disponibilité sur plusieurs plateformes basées sur le Gestionnaire de ressources Azure Azure Web application | Microsoft Azure"
    description="Découvrez comment utiliser les outils de ligne de commande de disponibilité sur plusieurs plateformes basées sur le Gestionnaire de ressources Azure nouvelle pour gérer vos applications Web Azure."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>À l’aide de la ressource Azure gestionnaire basé XPlat infrastructure du langage commun Azure Web App#

> [AZURE.SELECTOR]
- [Azure infrastructure du langage commun](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [PowerShell Azure](app-service-web-app-azure-resource-manager-powershell.md)

Avec la version de Microsoft Azure disponibilité sur plusieurs plateformes de ligne de commande Outils version 0.10.5, les nouvelles commandes ont été ajoutées. Ces commandes permettent à l’utilisateur à utiliser les commandes basées sur le Gestionnaire de ressources Azure PowerShell pour gérer les applications Web.

Pour en savoir plus sur la gestion des groupes de ressources, voir [utiliser l’infrastructure du langage commun Azure pour gérer les ressources Azure et groupes de ressources](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Plans de gestion du Service d’application ##

### <a name="create-an-app-service-plan"></a>Créer un Plan de services d’application ###
Pour créer un plan de services d’application, utilisez la commande **créer appserviceplan azure** .

Voici les descriptions des paramètres différents :

-   **--groupe de ressources**: groupe de ressources qui inclut le plan de services d’application nouvellement créé.
-   **--nom**: nom de l’offre de service d’application.
-   **--emplacement**: emplacement plan du service d’application.
-   **--couche**: la référence (SKU) tarification souhaité (les options sont : F1 (gratuit). D1 (partagée). B1 (base petite), B2 (moyen simple) et B3 (Basic grand). S1 (petite Standard), S2 (moyen Standard) et S3 (Standard grand). P1 (petite Premium), P2 (moyen Premium) et P3 (Premium grand).)
-   **--instances**: le nombre de travailleurs de l’application de service plan (valeur par défaut est 1).

Exemple pour utiliser cette applet de commande :

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Répertorier les Plans de Service d’application existant ###

Pour répertorier les plans de services d’application existant, utilisez **appserviceplan azure liste** commande.

Pour répertorier toutes les offres de service d’application sous un groupe de ressources spécifiques, utilisez :

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Pour obtenir un plan de services d’application spécifique, utilisez la commande **appserviceplan azure afficher** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurer un Plan de Service application existant ###

Pour modifier les paramètres d’un plan de service d’application existant, utilisez la commande **appserviceplan azure config** . Vous pouvez modifier la référence (SKU) et le nombre de collaborateurs 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Mise à l’échelle d’un Plan de services d’application ####

Pour redimensionner un Plan de Service application existante, utilisez :

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Modification de la référence (SKU) d’un Plan de Service d’application ####

Pour modifier la référence (SKU) d’un Plan de Service application existante, utilisez :

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Supprimer un Plan de Service application existant ###

Pour supprimer un plan de service d’application existant, toutes les applications web affectées doivent être déplacé ou supprimé en premier. Puis en utilisant la commande **Supprimer webapp azure** , que vous pouvez supprimer le plan de services d’application.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gestion des applications Web de Service d’application ##

### <a name="create-a-web-app"></a>Créer une application Web ###

Pour créer une application web, utilisez la commande **créer webapp azure** .

Voici les descriptions des paramètres différents :

- **--nom**: nom de l’application web.
- **--plan**: nom pour le plan de service utilisé pour héberger l’application web.
- **--groupe de ressources**: groupe de ressources qui héberge le plan de services d’application.
- **--emplacement**: l’emplacement de l’application web.

Exemple pour utiliser cette applet de commande :

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Supprimer une application Web existante ###

Pour supprimer une application web existant que vous pouvez utiliser la commande **Supprimer webapp azure** , vous devez spécifier le nom de l’application web et le nom de groupe de ressources.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Liste des applications Web existantes ###

Pour répertorier les applications web existant, utilisez la commande **liste webapp azure** .

Pour répertorier toutes les applications web sous un groupe de ressources spécifiques, utilisez :

    azure webapp list --resource-group ContosoAzureResourceGroup

Pour obtenir une application web spécifique, utilisez la commande **Afficher webapp azure** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurer une application Web existante ###

Pour modifier les paramètres et configurations pour une application web existante, utilisez la commande **du jeu de configuration webapp azure** .

Exemple (1) : modifier la version de php d’une application web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Exemple (2) : ajouter ou modifier les paramètres de l’application

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Pour connaître la configuration d’autres peut être modifié, utilisez la commande **config webapp azure set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Modifier l’état d’une application Web existante ###

#### <a name="restart-a-web-app"></a>Redémarrer une application web ####

Pour reprendre une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Arrêter une application web ####

Pour arrêter une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Démarrer une application web ####

Pour démarrer une application web, vous devez spécifier le groupe nom et des ressources de l’application web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gérer les profils de publication Web App ###

Chaque application web a un profil de publication qui peut être utilisé pour publier vos applications.

#### <a name="get-publishing-profile"></a>Disposez d’un profil de publication ####

Pour obtenir le profil de publication pour une application web, utilisez :

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Cette commande renvoie la publication nom d’utilisateur de profil et le mot de passe pour la ligne de commande.

### <a name="manage-web-app-hostnames"></a>Gérer les noms d’hôtes Web App ###

Pour gérer les liaisons de nom d’hôte pour votre application web, utilisez la commande de **noms d’hôtes de la configuration de la webapp azure**  

#### <a name="list-hostname-bindings"></a>Liste hostname liaisons ####

Pour obtenir les liaisons de nom d’hôte actif pour une application web, utilisez :

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Ajouter des liaisons nom d’hôte ####

Pour ajouter des liaisons hostname dans une application web, utilisez :

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Supprimer des liaisons de nom d’hôte ####

Pour supprimer des liaisons de nom d’hôte, utilisez :

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Étapes suivantes ###
- Pour en savoir plus sur prise en charge Azure Gestionnaire de ressources CLI, voir [l’infrastructure du langage commun Azure permet de gérer les ressources et groupes ressources Azure.](../xplat-cli-azure-resource-manager.md)
- Pour en savoir plus sur la gestion du Service d’application à l’aide de PowerShell, voir [Using Azure Resource Manager-Based PowerShell pour gérer les applications Web Azure.](app-service-web-app-azure-resource-manager-powershell.md)
