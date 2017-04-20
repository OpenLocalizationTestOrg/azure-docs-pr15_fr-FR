<properties
   pageTitle="Configurer votre environnement de développement | Microsoft Azure"
   description="Installer les outils, le Kit de développement logiciel et le runtime et créez un cluster de développement local. Au terme de ce programme d’installation, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Pour créer et exécuter des [applications Azure Service TISSU] [ 1] sur votre ordinateur de développement, installez le runtime, SDK et outils. Vous devez également activer l’exécution de scripts Windows PowerShell inclus dans le Kit de développement.

## <a name="prerequisites"></a>Conditions préalables
### <a name="supported-operating-system-versions"></a>Versions de système d’exploitation pris en charge
Les versions des systèmes d’exploitation suivants sont pris en charge pour le développement :

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 inclut uniquement Windows PowerShell 2.0 par défaut. Applets de commande PowerShell tissu de service nécessite PowerShell 3.0 ou version ultérieure. Vous pouvez [télécharger Windows PowerShell 5.0] [ powershell5-download] à partir du Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Installer les outils, le Kit de développement logiciel et le runtime

Le programme d’installation de la plateforme Web propose deux configurations pour le développement de tissu de Service :

- [Installer le Service TISSU runtime, SDK et outils pour Visual Studio 2015 (nécessite une mise à jour de Visual Studio 2015 2 ou version ultérieure)][full-bundle-vs2015]
- [Installer le Service TISSU runtime et SDK (aucune outils Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Activer l’exécution de scripts PowerShell

Service TISSU utilise de scripts Windows PowerShell pour créer un cluster de développement local et pour le déploiement des applications à partir de Visual Studio. Par défaut, Windows empêche l’exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d’exécution PowerShell. Ouvrez PowerShell en tant qu’administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez terminé de configurer votre environnement de développement, commencez à créer et exécuter des applications.

- [Créer votre première application Service tissu dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Découvrez comment déployer et gérer les applications sur votre cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [En savoir plus sur les modèles de programmation : Services fiable et intervenants fiable](service-fabric-choose-framework.md)
- [Consultez les exemples de code de Service tissu sur GitHub](https://aka.ms/servicefabricsamples)
- [Visualiser votre cluster à l’aide de l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md)
- [Suivez les rubriques d’apprentissage tissu de Service pour obtenir une présentation de la plateforme large](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Page de campagne tissu de service"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "RC VS"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Lien VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Lien WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Lien du Kit de développement logiciel WebPI Core"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
