<properties
   pageTitle="Machine virtuelle Azure DotNet Core didacticiel 1 | Microsoft Azure"
   description="Machine virtuelle Azure DotNet Core didacticiel"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatisation des déploiements d’application pour le Machines virtuelles Azure

Cette série de quatre détails de déploiement et la configuration des ressources Azure et applications à l’aide de modèles de gérer les ressources Azure. Dans cette série, un exemple de modèle est déployé et le modèle de déploiement examiné. L’objectif de cette série est pour informer sur la relation entre les ressources Azure et fournir mains sur expérience de déploiement de modèles de gestionnaire de ressources Azure totalement intégré. Ce document suppose un niveau de base de connaissances avec le Gestionnaire de ressources Azure, avant de commencer ce didacticiel se familiariser avec les concepts de base Azure le Gestionnaire de ressources.

## <a name="music-store-application"></a>Application du magasin de musique

L’exemple utilisé dans cette série est un .net application Core simuler un magasin de musique expérience d’achat. Cette application peut être déployée sur un Linux ou Windows système virtuel, déploiements exemple ont été créés pour les deux. L’application inclut une application web et une base de données SQL. Avant de lire les articles dans cette série, déployez l’application en utilisant le bouton de déploiement trouvé sur cette page. Lorsque entièrement déployé, l’architecture d’application / Azure ressemble à l’illustration suivante. 

Le modèle de gestionnaire de ressources de musique Store sont accessibles ici, [Modèle de Windows Store musique](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Application du magasin de musique](./media/virtual-machines-windows-dotnet-core/music-store.png)

Chacun de ces composants, y compris le modèle associer JSON est examinée dans les articles suivants de quatre.

- [**Architecture de l’application**](./virtual-machines-windows-dotnet-core-2-architecture.md) – composants d’Application tels que des sites web et les bases de données doivent être hébergées sur les ressources de l’ordinateur Azure tels que des machines virtuelles et des bases de données SQL Azure. Ce document basiques besoin de calcul de mappage, aux ressources Azure et déploiement de ces ressources avec un modèle d’Azure le Gestionnaire de ressources. 

- [**Accès et la sécurité**](./virtual-machines-windows-dotnet-core-3-access-security.md) – lors de l’hébergement des applications dans Azure, il est nécessaire à prendre en considération comment accéder à l’application et le fonctionnement de différents access de composants d’application eux. Ce document détails fournissant et sécuriser les accès à internet à l’application et accès entre les composants d’application.

- [**Disponibilité et les proportions**](./virtual-machines-windows-dotnet-core-4-availability-scale.md) – disponibilité et l’échelle se réfèrent à la fonctionnalité permettant d’applications pour rester en cours d’exécution pendant l’arrêt de l’infrastructure et la possibilité de mettre à l’échelle des ressources cluster pour répondre à la demande de l’application. Cette application hautement disponible et détails du document les composants nécessaires pour déployer une équilibrage de charge.

- [**Déploiement des applications**](./virtual-machines-windows-dotnet-core-5-app-deployment.md) - lors du déploiement des applications sur le Machines virtuelles Azure, la méthode par lequel les fichiers binaires sont installés sur l’ordinateur virtuel doit être considérée. Ce document en détail automatisation installation de l’application à l’aide des Extensions de Script personnalisé Azure Machine virtuelle.

L’objectif lorsque vous développez des modèles de gestionnaire de ressources Azure consiste à automatiser le déploiement d’Azure Infrastructure et l’installation et la configuration des applications hébergé sur cette infrastructure Azure. En utilisant les articles suivants fournit un exemple de cette expérience.

## <a name="deploy-the-music-store-application"></a>Déploiement de l’application de magasin de musique

L’application du magasin de musique peut être déployée à l’aide de ce bouton.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Le modèle de gestionnaire de ressources Azure requiert les valeurs de paramètre suivantes.

|Nom du paramètre |Description   |
|---|---|
|ADMINUSERNAME   | Nom d’utilisateur qui est utilisé sur la machine virtuelle et la base de données SQL Azure.  |
|ADMINPASSWORD | Mot de passe est utilisée dans la Machine virtuelle Azure et de la base de données SQL.  |
|NUMBEROFINSTANCES | Le nombre de machines virtuelles à créer. Chacun de ces ordinateurs virtuels héberger l’application web magasin de musique, et tout le trafic équilibrée de charge est entre eux. |
|PUBLICIPADDRESSDNSNAME | Nom DNS associé à l’adresse IP publique globalement unique. |

Une fois le déploiement modèle terminée, accédez à l’adresse IP à l’aide de n’importe quel navigateur internet. .Net site musical Core s’affiche.

## <a name="next-steps"></a>Étapes suivantes

<hr>

[Étape 1 : Architecture de l’Application avec les modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-2-architecture.md)

[Étape 2 : Access et la sécurité dans Azure modèles Gestionnaire de ressources](./virtual-machines-windows-dotnet-core-3-access-security.md)

[Étape 3 : disponibilité et l’échelle dans Azure modèles Gestionnaire de ressources](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[Étape 4 : déploiement d’applications avec les modèles de gestionnaire de ressources Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


