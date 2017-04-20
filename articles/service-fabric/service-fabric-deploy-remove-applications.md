<properties
   pageTitle="Déploiement des applications de service TISSU | Microsoft Azure"
   description="Comment déployer et supprimer des applications de Service TISSU"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Déployer et supprimer des applications à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Une fois un [type d’application a été empaqueté][10], elle est prête à être déployée dans un cluster Azure Service tissu. Déploiement comprend les trois étapes suivantes :

1. Télécharger le package d’application
2. Enregistrer le type d’application
3. Créer l’instance d’application

>[AZURE.NOTE] Si vous utilisez Visual Studio pour le déploiement et débogage d’applications sur votre cluster de développement local, toutes les étapes suivantes sont gérées automatiquement via un script PowerShell figurant dans le dossier Scripts du projet d’application. Cet article fournit en arrière-plan sur ce que font les scripts de sorte que vous pouvez effectuer les opérations mêmes en dehors de Visual Studio.

## <a name="upload-the-application-package"></a>Télécharger le package d’application

Télécharger le package d’application place dans un emplacement accessible par les composants du Service TISSU internes. Vous pouvez utiliser PowerShell pour effectuer le téléchargement. Avant d’exécuter des commandes PowerShell dans cet article, commencent toujours à l’aide de [ServiceFabricCluster de se connecter](https://msdn.microsoft.com/library/mt125938.aspx) pour vous connecter au cluster tissu de Service.

Supposons que vous avez un dossier nommé *MyApplicationType* qui contient le manifeste d’application nécessaires, manifestes de service et packages code/config/données. La commande [Copier ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) télécharge le package pour le cluster magasin d’Image. L’applet de commande **Get-ImageStoreConnectionStringFromClusterManifest** , qui fait partie du module Service TISSU SDK PowerShell, est utilisée pour obtenir la chaîne de connexion store image.  Pour importer le module SDK, exécutez :

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Vous pouvez copier un package d’application de *Studio C:\users\ryanwi\Documents\Visual 2015\Projects\MyApplication\myapplication\pkg\debug* à *c:\temp\MyApplicationType* (renommer le répertoire de « débogage » à « MyApplicationType »). L’exemple suivant télécharge le package :

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Enregistrer le package d’application

Enregistrer le package d’application rend le type d’application et la version déclaré dans le manifeste d’application disponible pour une utilisation. Le système lit le package téléchargé à l’étape précédente, vérifiez que le package (équivalent à l’exécution des [Tests ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) localement), traiter le contenu du package et copier le package transformé vers un emplacement du système interne.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

La commande [Register ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) renvoie uniquement une fois que le système a copié correctement le package d’application. Temps nécessaire dépend du contenu du package d’application. Si nécessaire, le paramètre **- TimeoutSec** peut être utilisé pour fournir un plus long délai d’attente. (Le délai par défaut est 60 secondes).

La commande [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) répertorie toutes les versions de type application correctement enregistrée.

## <a name="create-the-application"></a>Créer l’application

Vous pouvez instanciation une application à l’aide de n’importe quelle version de type d’application qui a été correctement enregistrée via la commande [New-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . Le nom de chaque application doit commencer par le *TISSU :* schéma et être unique pour chaque instance de l’application. Tous les services par défaut définis dans le manifeste d’application du type d’application cible sont créés pour le moment.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

La commande [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) répertorie toutes les instances d’application qui ont été créés correctement, ainsi que son statut globale.

La commande [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) répertorie toutes les instances de service qui ont été créés correctement au sein d’une instance d’application donnée. Services par défaut (le cas échéant) sont répertoriés ici.

Plusieurs instances de l’application peuvent être créées pour n’importe quelle version donnée d’un type d’application enregistrée. Chaque instance de l’application s’exécute en isolement, avec sa propre répertoire de travail et les processus.

## <a name="remove-an-application"></a>Supprimer une application

Lorsqu’une instance de l’application n’est plus nécessaire, vous pouvez le supprimer définitivement en utilisant la commande [Supprimer ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Cette commande supprime automatiquement tous les services qui appartiennent à l’application ainsi que supprimer définitivement tous les état du service. Cette opération ne peut pas être annulée, et état de l’application ne peuvent pas être récupéré.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Lorsqu’une version particulière d’un type d’application n’est plus nécessaire, vous devez être annulée à l’aide de la commande [Annuler l’enregistrement ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Annulation de l’inscription inutilisés types libère espace de stockage utilisé par le contenu du package application de ce type dans le magasin d’image. Type d’application peut être annulé tant qu’aucune application n’instanciation sur celui-ci et n’en attente d’application mises à niveau sont faites référence.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copie ServiceFabricApplicationPackage vous invite à fournir un ImageStoreConnectionString

L’environnement du Service TISSU SDK disposez déjà les valeurs par défaut correctes configurer. Mais, si nécessaire, le ImageStoreConnectionString pour toutes les commandes doit correspondre à la valeur qui utilise le cluster tissu de Service. Vous pouvez trouver dans le manifeste cluster récupéré via la commande [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau de service TISSU application](service-fabric-application-upgrade.md)

[Introduction au niveau de service TISSU](service-fabric-health-introduction.md)

[Diagnostiquer et résoudre les problèmes d’un service de tissu de Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modéliser une application de Service TISSU](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
