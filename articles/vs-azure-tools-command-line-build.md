<properties
   pageTitle="Génération de ligne de commande pour Azure | Microsoft Azure"
   description="Génération de ligne de commande pour Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Génération de ligne de commande pour Azure

## <a name="overview"></a>Vue d’ensemble

Vous pouvez créer un package de déploiement d’Azure en exécutant MSBuild à l’invite. Vous pouvez configurer et définir des générations pour débogage, de test et de production, en plus d’automatisation de certaines du processus de génération.


## <a name="microsoft-build-engine-msbuild"></a>Moteur de génération de Microsoft (MSBuild)

À l’aide de Microsoft Build Engine (MSBuild), vous pouvez générer des produits dans build environnement où Visual Studio n’est pas installé. MSBuild utilise un format XML pour les fichiers de projet d’extensible et entièrement pris en charge par Microsoft. Dans ce format de fichier, vous pouvez décrire les éléments devant être pour un ou plusieurs plateformes et configurations.

Vous pouvez également exécuter MSBuild à l’invite, et cette rubrique décrit cette approche. En définissant des propriétés à l’invite, vous pouvez créer des configurations spécifiques d’un projet. De même, vous pouvez également définir les objectifs qui la commande MSBuild générera. Pour plus d’informations sur les paramètres de ligne de commande et MSBuild, voir [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Installation

Comme décrit dans la procédure suivante, vous devez installer logiciels et outils sur le serveur de génération avant de pouvoir créer un package d’Azure à l’aide de MSBuild :

1. Installez .NET Framework 4 ou version ultérieure, qui inclut MSBuild.

1. Installer les [Outils de création de Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (recherchez MicrosoftAzureAuthoringTools x64.msi ou MicrosoftAzureAuthoringTools x86.msi.

1. Installer les [Bibliothèques Azure pour .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (recherchez MicrosoftAzureLibsForNet x64.msi ou MicrosoftAzureLibs x86.msi.

1. Copiez le fichier Microsoft.WebApplication.targets à partir d’une installation Visual Studio sur un autre ordinateur.

    Le fichier se trouve dans le répertoire C:\Program Files (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v11.0 pour Visual Studio 2012) et vous devez le copier dans le même répertoire sur le serveur de génération.

1. Installez les [Outils Azure pour Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Recherchez WindowsAzureTools.vs120.exe générer des projets Visual Studio 2013.

## <a name="msbuild-parameters"></a>Paramètres de MSBuild

Le moyen le plus simple pour créer un package est exécution avec la `/t:Publish` option. Par défaut, cette commande crée un répertoire en relation avec le dossier racine pour le projet, par exemple ProjectDir\bin\Configuration\app.publish\. quand vous créez un projet d’Azure, vous générez deux fichiers, le fichier de package et le fichier de configuration associées :

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Par défaut, chaque projet Azure inclut un fichier de configuration du service pour local (débogage) crée et une autre pour les versions préliminaires cloud (intermédiaire ou production), mais vous pouvez ajouter ou supprimer des fichiers de configuration du service selon vos besoins. Quand vous créez un package dans Visual Studio, vous êtes invité le fichier de configuration du service à inclure à côté du package. Quand vous créez un package à l’aide de MSBuild, le fichier de configuration du service local est inclus par défaut. Pour inclure un autre fichier de configuration du service, définissez la `TargetProfile` propriétés de la commande MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Si vous voulez utiliser un autre répertoire pour le package stocké et les fichiers de configuration, définissez le chemin d’accès à l’aide de la `/p:PublishDir=Directory\` option, y compris le séparateur de barre oblique inverse à droite.

## <a name="deployment"></a>Déploiement

Une fois que le package est créé, vous pouvez déployez Azure. Pour consulter le didacticiel décrivant ce processus, consultez le site Web Azure. Pour plus d’informations sur la façon d’automatiser ce processus, consultez la section [Continu remise Services Cloud dans Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
