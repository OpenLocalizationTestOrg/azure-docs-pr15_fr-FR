<properties
   pageTitle="Résoudre les problèmes de configuration de votre cluster Service TISSU locale | Microsoft Azure"
   description="Cet article traite un ensemble de suggestions de résolution des problèmes de votre cluster de développement local"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Résoudre les problèmes de configuration de votre cluster de développement local

Si vous rencontrez un problème lors de l’interaction avec votre cluster de développement Azure Service TISSU local, passez en revue les suggestions suivantes pour les solutions possibles.

## <a name="cluster-setup-failures"></a>Échecs d’installation cluster

### <a name="cannot-clean-up-service-fabric-logs"></a>Ne peut pas nettoyer les journaux tissu de Service

#### <a name="problem"></a>Problème

Tout en exécutant le script DevClusterSetup, vous voyez un message d’erreur comme suit :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solution

Fermez la fenêtre PowerShell en cours et ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devriez maintenant pouvoir exécuter le script.

## <a name="cluster-connection-failures"></a>Échecs de connexion cluster

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Applets de commande PowerShell tissu de service ne sont pas reconnus dans Azure PowerShell

#### <a name="problem"></a>Problème

Si vous essayez d’exécuter un des applets de commande PowerShell tissu de Service, tel que `Connect-ServiceFabricCluster` dans une fenêtre PowerShell Azure, il échoue, indiquant que l’applet de commande n’est pas reconnu. La raison est que PowerShell Azure utilise la version 32 bits de Windows PowerShell (même sur les versions de système d’exploitation 64 bits), tandis que les applets de commande Service TISSU fonctionnent uniquement dans les environnements 64 bits.

#### <a name="solution"></a>Solution

Toujours exécuter les applets de commande tissu de Service directement à partir de Windows PowerShell.

>[AZURE.NOTE] La dernière version d’Azure PowerShell ne crée pas un raccourci spécial, afin que c’est le cas n’est plus.

### <a name="type-initialization-exception"></a>Exception de l’initialisation de type

#### <a name="problem"></a>Problème

Lorsque vous vous connectez au cluster dans PowerShell, vous voyez l’erreur TypeInitializationException pour System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solution

Votre variable path n’est pas correctement définie pendant l’installation. Veuillez vous déconnecter de Windows et reconnectez-vous. Cela actualisera entièrement votre chemin d’accès.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Connexion cluster échoue avec « Objet est fermé »

#### <a name="problem"></a>Problème

Un appel à se connecter ServiceFabricCluster échoue avec un message d’erreur comme suit :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solution

Fermez la fenêtre PowerShell en cours et ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devriez maintenant pouvoir se connecter.

### <a name="fabric-connection-denied-exception"></a>Exceptions de connexion refusée TISSU

#### <a name="problem"></a>Problème

Lors du débogage de Visual Studio, vous obtenez une erreur FabricConnectionDeniedException.

#### <a name="solution"></a>Solution

Cette erreur se produit généralement lorsque vous essayez à essayer pour démarrer un hôte service traitez manuellement, au lieu de laisser le runtime Service TISSU démarre pour vous.

Vérifiez que vous n’avez pas tous les projets service définir en tant que les projets de démarrage dans votre solution. Uniquement les projets d’application de Service tissu doivent être définis comme projets de démarrage.

>[AZURE.TIP] Si, après l’installation, votre cluster local commence à se comporter anormale, vous pouvez réinitialiser à l’aide de l’application de barre d’état système cluster local manager. Cela supprime le cluster existant et configurer un nouveau. Notez que toutes les applications déploiement et données associées sont supprimées.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre et résoudre les problèmes de votre cluster avec des rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualiser votre cluster avec l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md)
