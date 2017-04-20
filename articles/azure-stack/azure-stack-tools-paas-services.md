<properties
    pageTitle="Outils et PaaS services pour pile Azure | Microsoft Azure"
    description="Découvrez comment commencer à utiliser les services PaaS dans Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Outils pour pile Azure

Vous pouvez télécharger les outils décrits ci-après. Si vous souhaitez être informé d’outils et de nouveaux services, suivez #AzureStack sur Twitter.

## <a name="template-tools"></a>Outils de modèle

### <a name="azure-stack-github-templates"></a>Modèles de pile Github Azure
Explorer la collection croissante [d’Azure pile GitHub modèles](https://github.com/Azure/AzureStack-QuickStart-Templates). Comme [Azure](https://github.com/Azure/azure-quickstart-templates), ces modèles de « Démarrage rapide » le Gestionnaire de ressources Azure visent à vous aider à démarrer avec des blocs de construction simples et des exemples, vous êtes prêts à déployer sur le Microsoft Azure pile Technical Preview preuve de Concept environnement. Vous trouverez premières exemples de charge de travail de fête pour [ad-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint-2013-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), bien que plusieurs 101 modèles simples comme [101 simple-windows machine virtuelle](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Exemple et l’outil de création d’un package d’élément marketplace
[Télécharger et utiliser l’outil de création d’un package](http://www.aka.ms/azurestackmarketplaceitem) pour créer des éléments pour vos propres modèles personnalisés à ajouter à la place de marché pile Azure marketplace. Vous trouverez des instructions sur la façon de créer un élément marketplace et le rendre disponible à vos clients dans [Marketplace créer l’élément](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Outils de développement


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Utilisez Visual Studio et Azure pile TP2 sur l’ordinateur virtuel MAS CON01
Si vous souhaitez utiliser Visual Studio sur la console de machine virtuelle pour travailler avec les modèles de pile Azure, vous devez installer les versions correctes des outils requis. Utilisez la procédure suivante pour installer les versions prises en charge pour le protocole TP2.

1. Utiliser une connexion Bureau à distance pour vous connecter à la machine virtuelle MAS CON01 avec les informations d’identification azurestack\azurestackadmin.
2. Installer et ouvrez Web Platform Installer.
3. Rechercher et installer **Visual Studio Communauté 2015 avec Microsoft Azure SDK - 2.9.5**.
4. À l’aide **d’Ajout/Suppression de programmes**, désinstallez **Microsoft Azure PowerShell (Sept)** qui est installée dans le cadre du Kit de développement.
5. Ouvrez le programme d’installation de la plateforme Web.
6. Rechercher et installer **Microsoft Azure PowerShell - Azure pile Technical Preview 2**. 
7. Redémarrez l’ordinateur virtuel MAS CON01.
8. Utiliser une connexion Bureau à distance pour vous connecter à la machine virtuelle MAS CON01 avec les informations d’identification azurestack\azurestackadmin.
9. Ouvrez Visual Studio et valider que vous pouvez vous connecter à l’environnement pile Azure, obtenez des modèles et ainsi de suite. 

### <a name="azure-powershell-sdk"></a>Azure SDK PowerShell
PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure et Azure pile avec Windows PowerShell. Vous pouvez utiliser les applets de commande pour créer, tester, déployer et gérer des solutions et services remis via la plateforme Azure pile.
[Kit de développement PowerShell Azure Téléchargez](http://aka.ms/azStackPsh) et [installez PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure croisée interfaces de ligne de commande plateforme
Installez rapidement l’Interface de ligne Azure (Azure commande) pour utiliser un jeu de commandes shell open source pour la création et la gestion des ressources de Microsoft Azure pile.

[Télécharger l’infrastructure du langage commun Windows](http://aka.ms/azstack-windows-cli)

[Télécharger l’infrastructure du langage commun Mac](http://aka.ms/azstack-linux-cli)

[Télécharger l’infrastructure du langage commun Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Si vous êtes sur un ordinateur Mac ou Linux, vous pouvez également ouvrir l’infrastructure du langage commun à l’aide de la commande`npm install -g azure-cli@0.9.11`</br>
> + Si vous rencontrez des problèmes de validation de certificat, exécutez la commande`set NODE_TLS_REJECT_UNAUTHORIZED=0`
