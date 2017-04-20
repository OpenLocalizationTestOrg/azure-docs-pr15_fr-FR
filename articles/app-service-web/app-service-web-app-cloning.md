<properties
    pageTitle="Web application clonant à l’aide de PowerShell"
    description="Découvrez comment cloner vos applications Web aux nouvelles applications Web à l’aide de PowerShell."
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
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Application de Service application Azure clonant à l’aide de PowerShell#

Dans la version de Microsoft Azure PowerShell version 1.1.0 une nouvelle option a été ajoutée au nouveau-AzureRMWebApp qui serait donner à l’utilisateur la possibilité de cloner une application nouvellement créée dans une zone différente ou dans la même région d’une application Web existante. Cela va permettre aux clients à déployer un nombre d’applications dans différentes régions rapidement et facilement.

Application clonant est actuellement pris en charge uniquement pour les offres de service premium couche application. La nouvelle fonctionnalité utilise les limitations mêmes en tant que fonction de sauvegarde des applications Web, voir [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Pour en savoir plus sur l’utilisation de PowerShell Azure en fonction de gestionnaire de ressources Azure applets de commande pour gérer vos applications Web vérifier [le Gestionnaire de ressources Azure en fonction de commandes PowerShell Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Clonant une application existante ##

Scénario : Une application web existante dans la région Sud, l’utilisateur souhaite Cloner le contenu vers une nouvelle application web dans la région du Nord centrale nous. Pour ce faire, vous pouvez utiliser la version Azure le Gestionnaire de ressources de l’applet de commande PowerShell pour créer une nouvelle application web avec l’option - SourceWebApp.

Connaître le nom de groupe de ressources qui contient l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations de l’application web source (dans ce cas nommées source webapp) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Pour créer un nouveau Plan de Service de l’application, nous pouvons utiliser la commande New-AzureRmAppServicePlan comme dans l’exemple suivant

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

À l’aide de la commande New-AzureRmWebApp, nous pouvons créer la nouvelle application web dans la région du Nord centrale contactez-nous et lier à un niveau premium existant Plan de Service d’application, en outre nous pouvons utiliser le même groupe de ressources en tant que l’application web source ou définir un nouveau groupe de ressources, la section suivante montre :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Pour cloner une application web existante, y compris le déploiement aux emplacements, l’utilisateur doit utiliser le paramètre IncludeSourceWebAppSlots, la commande PowerShell suivante illustre l’utilisation de ce paramètre avec la commande New-AzureRmWebApp :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Pour dupliquer une application web existante dans la même région, l’utilisateur doit créer un nouveau groupe de ressources et un nouveau service application planifier dans la même région, puis en utilisant la commande PowerShell suivante pour cloner l’application web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonant une application existante dans un environnement de Service d’application ##

Scénario : Une application web existante dans la région Sud, l’utilisateur souhaite Cloner le contenu vers une nouvelle application web à l’environnement de Service application (ASE) un existant.

Connaître le nom de groupe de ressources qui contient l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations de l’application web source (dans ce cas nommées source webapp) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Connaître le nom de l’ASE et le nom du groupe de ressources auquel appartient la ASE, l’utilisateur peut utiliser la commande New-AzureRmWebApp pour créer la nouvelle application web dans le ASE existant, la section suivante montre :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Le paramètre emplacement est obligatoire en raison de motif hérité, mais dans le cas de la création d’une application dans ASE il sera ignoré. 

## <a name="cloning-an-existing-app-slot"></a>Clonant un emplacement d’application existant ##

Scénario : L’utilisateur souhaite cloner un emplacement de l’application Web existant vers soit une nouvelle application Web ou un nouvel emplacement dans le navigateur. La nouvelle application Web peuvent être dans la même région en tant que l’emplacement d’origine dans le navigateur ou dans une zone différente.

Connaître le nom de groupe de ressources qui contient l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir des informations d’emplacement de l’application de web source (dans ce cas nommées source webappslot) liées à l’application Web source-webapp :

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Le suivant illustre la création d’un cloner de l’application web source pour une nouvelle application web :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuration du Gestionnaire de trafic lors de la duplication d’une application ##

Création d’applications web plusieurs régions et configuration du Gestionnaire de trafic Azure pour acheminer le trafic vers toutes ces applications web, est un scénario n importantes pour vous assurer que les applications de clients sont hautement disponibles, lorsque clonant une application web existante vous avez la possibilité de se connecter les deux applications web à un nouveau profil manager le trafic ou existant, notez que le Gestionnaire de ressources Azure seule version du Gestionnaire de trafic est pris en charge.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Créer un nouveau profil le trafic gestionnaire lors de la duplication d’une application ###

Scénario : L’utilisateur souhaite cloner une application web à un autre région, lors de la configuration d’un profil de gestionnaire de trafic Azure le Gestionnaire de ressources qui incluent les deux applications web. Les éléments suivants illustre la création d’un cloner de l’application web source pour une nouvelle application web lors de la configuration d’un nouveau profil le trafic gestionnaire :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Ajout de nouveaux cloner Web App à un profil existant le trafic Manager ###

Scénario : L’utilisateur a déjà un profil de gestionnaire de trafic Azure le Gestionnaire de ressources il souhaite ajouter les deux applications web comme points de terminaison. Pour ce faire, nous avons besoin de disposer de l’id du profil gestionnaire le trafic existant tout d’abord, nous devons l’id de l’abonnement, nom de groupe de ressources et le nom du profil responsable le trafic existant.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Après avoir l’id de gestionnaire le trafic, les éléments suivants illustre la création d’un cloner de l’application web source pour une nouvelle application web lors de les ajouter à un profil existant le trafic gestionnaire :

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrictions actuelles ##

Cette fonctionnalité n’est actuellement en mode Aperçu avant, nous travaillons pour ajouter de nouvelles fonctions au fil du temps, la liste suivante sont les restrictions connues dans la version actuelle d’application clonant :

- Paramètres d’échelle automatique ne sont pas cloner
- Paramètres de planification de la sauvegarde ne sont pas cloner
- Paramètres de VNET ne sont pas cloner
- Application perspectives ne sont pas automatiquement définies sur l’application web de destination
- Facile Auth paramètres ne sont pas cloner
- Extension kudu ne sont pas cloner
- Conseil règles ne sont pas cloner
- Contenu de la base de données ne sont pas cloner


### <a name="references"></a>Références ###
- [Le Gestionnaire de ressources Azure en fonction de commandes PowerShell Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Web application clonant à l’aide du portail Azure](app-service-web-app-cloning-portal.md)
- [Sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)
- [Azure prise en charge le Gestionnaire de ressources pour Azure le trafic Gestionnaire Preview](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introduction à l’environnement de Service d’application](app-service-app-service-environment-intro.md)
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md)
