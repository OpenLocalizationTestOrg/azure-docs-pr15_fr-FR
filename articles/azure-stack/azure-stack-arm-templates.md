<properties
    pageTitle="Utiliser des modèles de gestionnaire de ressources Azure Azure empilés (développeurs client) | Microsoft Azure"
    description="Découvrez comment utiliser le Gestionnaire de ressources Azure modèles dans Azure pile à déployer et mise en service de toutes les ressources pour votre application dans une seule opération coordonnée."
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
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utiliser des modèles de gestionnaire de ressources Azure Azure empilés

Modèles de gestionnaire de ressources Azure déploiement et mise en service de toutes les ressources pour votre application dans une seule opération coordonnée. Vous définissez les ressources de l’application et la manière dont elle sera déployée.  Vous pouvez également redéployez modèles pour apporter des modifications aux ressources dans le groupe de ressources.

Ces modèles peuvent être déployés avec le portail Microsoft Azure pile PowerShell, la ligne de commande et Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Les modèles suivants sont disponibles sous [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Déploiement de SharePoint (sans haute disponibilité)

Utiliser l’extension DSC PowerShell pour créer une batterie de serveurs SharePoint 2013 qui inclut les éléments suivants :

-   Un réseau virtuel

-   Trois comptes de stockage

-   Deux outils d’équilibrage de charge externe

-   Une machine virtuelle configuré comme contrôleur de domaine pour une nouvelle forêt avec un domaine unique

-   Une machine virtuelle configuré comme un serveur autonome SQL Server 2014

-   Une machine virtuelle configurés comme une batterie de serveurs SharePoint 2013 un ordinateur

## <a name="deploy-ad-non-high-availability"></a>Déployer AD (sans haute disponibilité)

Utiliser l’extension DSC PowerShell pour créer un serveur de contrôleur de domaine Active Directory qui inclut les éléments suivants :

-   Un réseau virtuel

-   Un seul compte de stockage

-   Un programme d’équilibrage de charge externe

-   Une machine virtuelle configuré comme contrôleur de domaine pour une nouvelle forêt avec un domaine unique

## <a name="deploy-adsql-non-high-availability"></a>Déployer AD/SQL (sans haute disponibilité)

Utiliser l’extension DSC PowerShell pour créer un serveur autonome SQL Server 2014 qui inclut les éléments suivants :

-   Un réseau virtuel

-   Deux comptes de stockage

-   Un programme d’équilibrage de charge externe

-   Une machine virtuelle configuré comme contrôleur de domaine pour une nouvelle forêt avec un domaine unique

-   Une machine virtuelle configuré comme un serveur autonome SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utiliser l’extension DSC PowerShell pour configurer une machine virtuelle existante Gestionnaire de Configuration Local (PPCM) et enregistrez-le dans un serveur extraire Azure Automation compte DSC.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Créer une machine virtuelle à partir d’une image de l’utilisateur

Créer une machine virtuelle à partir d’une image personnalisée des utilisateurs. Ce modèle déploie également un réseau virtuel (avec le système DNS), adresse IP publique et une interface réseau.

## <a name="simple-vm"></a>Machine virtuelle simple

Déployer une machine virtuelle Windows simple qui inclut un réseau virtuel (avec le système DNS), adresse IP publique et une interface réseau.

## <a name="cancel-a-running-template-deployment"></a>Annuler un déploiement du modèle en cours d’exécution

Pour annuler un déploiement du modèle en cours d’exécution, utilisez la `Stop-AzureRmResourceGroupDeployment` applet de commande PowerShell.


## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec le portail](azure-stack-deploy-template-portal.md)

[Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)

