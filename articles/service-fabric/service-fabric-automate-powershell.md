<properties
    pageTitle="Automatiser la gestion des applications de Service tissu à l’aide de PowerShell | Microsoft Azure"
    description="Déployer, mettre à niveau, tester et supprimer des applications de Service tissu à l’aide de PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatiser la politique d’application à l’aide de PowerShell

De nombreux aspects de la [politique d’application Service TISSU](service-fabric-application-lifecycle.md) peuvent être automatisées.  Cet article vous explique comment utiliser PowerShell pour automatiser les tâches courantes pour le déploiement, la mise à niveau, en supprimant et tester les applications Azure Service tissu.  Géré et HTTP APIs pour la gestion d’application sont également disponibles. Voir [politique d’application](service-fabric-application-lifecycle.md) pour plus d’informations.  

## <a name="prerequisites"></a>Conditions préalables
Avant de passer aux tâches dans l’article, veillez à :

+ Se familiariser avec les concepts de Service TISSU décrits dans la [présentation technique de tissu de Service](service-fabric-technical-overview.md).
+ [Installer les outils pour l’exécution, SDK et](service-fabric-get-started.md), qui également n’installe le module PowerShell **ServiceFabric** .
+ [L’exécution de scripts PowerShell activer](service-fabric-get-started.md#enable-powershell-script-execution).
+ Démarrer un cluster local.  Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur, puis exécutez le script de configuration de cluster à partir du dossier du Kit de développement logiciel :`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Avant d’exécuter des commandes PowerShell dans cet article, d’abord vous connecter pour le cluster Service TISSU local à l’aide de [**Se connecter ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ Les tâches suivantes requièrent un package d’application v1 à déployer et un package d’application v2 pour mise à niveau. Télécharger l' [exemple d’application **WordCount** ](http://aka.ms/servicefabricsamples) (situé dans les exemples de mise en route). Générer et empaqueter l’application dans Visual Studio (avec le bouton droit sur **WordCount** dans l’Explorateur de solutions et sélectionnez **Package**). Copier le package v1 dans `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` à `C:\Temp\WordCount`. Copie `C:\Temp\WordCount` à `C:\Temp\WordCountV2`, création du package d’application v2 pour mise à niveau. Ouvrir `C:\Temp\WordCountV2\ApplicationManifest.xml` dans un éditeur de texte. Dans l’élément **ApplicationManifest** , modifiez l’attribut **ApplicationTypeVersion** de « 1.0.0 » à « 2.0.0 » pour mettre à jour le numéro de version de l’application. Enregistrez le fichier ApplicationManifest.xml modifié.

## <a name="task-deploy-a-service-fabric-application"></a>Tâche : Déployer une application de Service TISSU

Après avoir créé et empaqueté l’application (ou téléchargé le package d’application), vous pouvez déployer l’application dans un cluster de Service TISSU local. Déploiement consiste à télécharger le package d’application, l’enregistrement du type d’application et création de l’instance d’application. Suivez les instructions dans cette section pour déployer une nouvelle application à un cluster.

### <a name="step-1-upload-the-application-package"></a>Étape 1 : Télécharger le package d’application
Téléchargement du package d’application dans le magasin d’image place dans un emplacement accessible aux composants tissu de Service internes.  Le package d’application contient la configuration nécessaire manifeste d’application, service manifestes et code et packages de données à créer l’application et les instances de service.  La commande [**Copier ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) télécharge le package. Par exemple :

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Étape 2 : Enregistrer le type d’application
Enregistrer le package d’application rend le type d’application et la version déclaré dans le manifeste d’application disponible pour une utilisation. Le système lit le package téléchargé à l’étape 1, vérifiez que le package (équivalent à l’exécution de [**Test ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localement), traiter le contenu du package et copier le package transformé vers un emplacement du système interne.  Exécutez l’applet de commande [**Registre ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Pour afficher tous les types d’application enregistrés dans le cluster, exécutez l’applet de commande [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Étape 3 : Créer l’instance d’application
Une application peut être instanciation à l’aide de n’importe quelle version de type d’application qui a été correctement enregistrée à l’aide de la commande [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . Le nom de chaque application est déclaré au déploiement et doit commencer par le **TISSU :** schéma et être unique pour chaque instance de l’application. Le nom de l’application et la version de type d’application sont déclarées dans le fichier **ApplicationManifest.xml** du package d’application. Si tous les services par défaut ont été définies dans le manifeste d’application du type d’application cible, ceux créés pour le moment.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

La commande [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) répertorie toutes les instances d’application qui ont été créés correctement, ainsi que son statut globale. La commande [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) répertorie toutes les instances de service qui ont été créés correctement au sein d’une instance d’application donnée. Services par défaut (le cas échéant) sont répertoriés.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tâche : Mettre à niveau une application de Service TISSU
Vous pouvez mettre à niveau une application de Service TISSU précédemment déployée avec un package d’application mis à jour. Cette tâche met à jour l’application WordCount qui a été déployée dans « tâche : déployer une application de Service tissu. » Pour plus d’informations, lisez via [l’application de Service TISSU mise à niveau](service-fabric-application-upgrade.md) .

Pour simplifier les choses pour cet exemple, uniquement le numéro de version a été mis à jour dans le package d’application WordCountV2 créé dans les conditions préalables. Un scénario plus plausible implique mise à jour de vos fichiers de données, configuration ou code de service puis reconstruction et compression de l’application avec des numéros de version mise à jour.  

### <a name="step-1-upload-the-updated-application-package"></a>Étape 1 : Télécharger le package d’application mis à jour
L’application v1 WordCount est prête à être mis à niveau. Si vous ouvrez une fenêtre PowerShell comme administrateur et tapez [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), vous voyez que cette version 1.0.0 WordCount application de type est déployée.  

Maintenant, copiez le package d’application mis à jour dans le magasin d’image tissu de Service (où les packages d’application sont stockés par tissu de Service). Le paramètre **ApplicationPackagePathInImageStore** vous informe tissu de Service dans lequel il peut trouver le package d’application. La commande suivante copie le package d’application dans **WordCountV2** dans le magasin d’image :  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Étape 2 : Enregistrer le type d’application mis à jour
L’étape suivante consiste à enregistrer la nouvelle version de l’application avec tissu de Service, qui peut être effectuée à l’aide de l’applet de commande [**Registre ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Étape 3 : Démarrer la mise à niveau
Différents paramètres de mise à niveau, des délais d’expiration et d’intégrité critères peuvent être appliqués aux mises à niveau de l’application. Lisez les documents [application mise à niveau des paramètres](service-fabric-application-upgrade-parameters.md) et [processus mise à niveau](service-fabric-application-upgrade.md) pour en savoir plus. Tous les services et instances doivent être _correcte_ après la mise à niveau.  Définissez la **HealthCheckStableDuration** à 60 secondes (de sorte que les services fonctionnent correctement au moins 20 secondes avant la mise à niveau se déroule pour le domaine de mise à niveau suivant).  Également définir **UpgradeDomainTimeout** 1200 secondes et **UpgradeTimeout** à 3000 secondes. Enfin, définissez **UpgradeFailureAction** sur **retour arrière**, qui demande que TISSU Service annule l’application à la version précédente si échecs sont produisent pendant la mise à niveau.

Vous pouvez maintenant démarrer la mise à niveau de l’application à l’aide de l’applet de commande [**Démarrer ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Notez que le nom de l’application est le même que le nom de l’application v1.0.0 précédemment déployées (tissu : / WordCount). Service TISSU utilise ce nom pour identifier quelle application est prise mis à niveau. Si vous définissez les délais d’expiration trop courte, vous pouvez rencontrer un message d’échec de délai d’expiration qui indique le problème. Reportez-vous à [résoudre les problèmes à niveau d’applications](service-fabric-application-upgrade-troubleshooting.md)ou augmenter les délais d’expiration.

### <a name="step-4-check-upgrade-progress"></a>Étape 4 : Progression de la mise à niveau à cocher
Vous pouvez surveiller l’avancement de la mise à niveau d’application à l’aide de [L’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md), ou à l’aide de l’applet de commande [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

En quelques minutes, l’applet de commande [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) indique que tous les domaines de mise à niveau ont été mis à niveau (terminée).

## <a name="task-test-a-service-fabric-application"></a>Tâche : Tester une application de Service TISSU

Pour écrire des services de haute qualité, les développeurs doivent être en mesure d’entraîner des erreurs de sources non fiables infrastructure pour tester la stabilité de leurs services. Service TISSU permet aux développeurs inciter les actions de défaillance et tester les services en présence d’erreurs en utilisant les scénarios de test chaos et le basculement.  Lisez la [vue d’ensemble de la capacité de test](service-fabric-testability-overview.md) pour plus d’informations.

### <a name="step-1-run-the-chaos-test-scenario"></a>Étape 1 : Exécuter le scénario de test chaos
Le scénario de test chaos génère des erreurs sur l’ensemble du cluster tissu de Service. Le scénario compresse défauts vus généralement par mois ou années à quelques heures. La combinaison des erreurs sont supprimés avec un taux de défaillance élevé recherche cas coin seraient manquer dans le cas contraire. L’exemple suivant montre le scénario de test chaos pendant 60 minutes.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Étape 2 : Exécuter le scénario de test de basculement
Le basculement tester cibles scénario une partition service spécifique au lieu de l’ensemble du cluster, et il laisse d’autres services ne sont pas affectées. Le scénario parcourt une séquence de pannes simulés dans validation du service pendant l’exécution de votre logique métier. Un échec de validation du service indique un problème qui nécessite des enquête. Le test de basculement provoque une défaillance à la fois, plutôt que d’utiliser le scénario de test chaos, qui peut amener plusieurs erreurs. L’exemple suivant montre le test de basculement pendant 60 minutes par rapport à la structure : / WordCount/WordCountService service.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tâche : Supprimer une application de Service TISSU
Vous pouvez supprimer une instance d’une application déployée, supprimer le type d’application généré à partir du cluster et supprimez le package d’application dans le ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Étape 1 : Supprimer une instance d’application
Lorsqu’une instance de l’application n’est plus nécessaire, vous pouvez le supprimer définitivement à l’aide de l’applet de commande [**Supprimer ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Cela supprime également automatiquement tous les services appartenant à l’application, supprimer définitivement tous les état du service. Cette opération ne peut pas être annulée et état de l’application ne peuvent pas être récupéré.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Étape 2 : Unregister le type d’application
Lorsque vous n’avez plus besoin d’une version particulière d’un type d’application, annuler son inscription à l’aide de l’applet de commande [**Unregister ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Annulation de l’inscription inutilisés types libère l’espace de stockage utilisé par le package d’application dans le magasin d’image. Type d’application peut être ou non dans la mesure où aucune application instancié sur celui-ci ou en attente de mises à niveau de l’application référençant.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Étape 3 : Supprimer le package d’application
Une fois que le type d’application est annulé, le package d’application peut être supprimé du magasin d’image à l’aide de l’applet de commande [**Supprimer ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Étapes suivantes
[Cycle de vie application tissu de service](service-fabric-application-lifecycle.md)

[Déployer une application](service-fabric-deploy-remove-applications.md)

[Mise à niveau de l’application](service-fabric-application-upgrade.md)

[Applets de commande de Service tissu Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Applets de commande de test Service tissu Azure](https://msdn.microsoft.com/library/azure/mt125844.aspx)
