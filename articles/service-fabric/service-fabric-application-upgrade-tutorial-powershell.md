<properties
   pageTitle="Mise à niveau du service TISSU application à l’aide de PowerShell | Microsoft Azure"
   description="Cet article décrit l’expérience de déploiement d’une application de Service tissu, la modification du code et déploiement d’une mise à niveau à l’aide de PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>Mise à niveau de l’application tissu de service à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

Les plus fréquemment utilisés et la mise à niveau propagée contrôlé il est recommandé de mise à niveau.  TISSU Service Azure surveille l’état de l’application mise à niveau selon un ensemble de stratégies d’intégrité. Une fois qu’un domaine de mise à jour (UD) est mis à niveau, Service TISSU évalue l’intégrité de l’application et continue pour le domaine de mise à jour suivant ou d’échec de la mise à niveau selon les stratégies d’intégrité.

Une mise à niveau de l’application analysée peut être effectuée à l’aide de la managé ou natif API, PowerShell ou reste. Pour obtenir des instructions sur l’exécution d’une mise à niveau à l’aide de Visual Studio, voir [mettre à niveau votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md).

Avec les mises à niveau propagées TISSU Service surveiller, l’administrateur application peut configurer la stratégie d’évaluation d’intégrité tissu de Service utilise pour déterminer si l’application est correcte. En outre, l’administrateur peut configurer l’action à effectuer lors de l’évaluation d’intégrité échoue (par exemple, effectuant une restauration automatique.) Cette section décrit une mise à niveau contrôlée pour un des échantillons SDK qui utilise PowerShell.

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Étape 1 : Créer et déployer l’échantillon objets visuels


Créer et publier l’application en cliquant sur le projet d’application, **VisualObjectsApplication** et en sélectionnant la commande **Publier** .  Pour plus d’informations, voir [application Service TISSU didacticiel de mise à niveau](service-fabric-application-upgrade-tutorial.md).  Par ailleurs, vous pouvez utiliser PowerShell pour déployer votre application.

> [AZURE.NOTE] Avant d’une des commandes tissu de Service peut être utilisée dans PowerShell, vous devez d’abord vous connecter au cluster en utilisant la `Connect-ServiceFabricCluster` applet de commande. De même, il est supposé que le Cluster a déjà été défini sur votre ordinateur local. Consultez l’article sur [la configuration de votre environnement de développement tissu de Service](service-fabric-get-started.md).

Après avoir créé le projet dans Visual Studio, vous pouvez utiliser la commande PowerShell **ServiceFabricApplicationPackage de copier** pour copier le package d’application dans le ImageStore. L’étape suivante consiste à enregistrer l’application à l’exécution de tissu de Service à l’aide de l’applet de commande **Registre ServiceFabricApplicationPackage** . La dernière étape consiste à démarrer une instance de l’application à l’aide de l’applet de commande **New-ServiceFabricApplication** .  Ces trois étapes sont similaires à l’aide de l’élément de menu **déployer** dans Visual Studio.

À présent, vous pouvez utiliser [Service TISSU Explorer pour afficher le cluster et l’application](service-fabric-visualizing-your-cluster.md). L’application a un service web qui peut être parcouru à dans Internet Explorer en tapant [http://localhost : 8081/visualobjects](http://localhost:8081/visualobjects) dans la barre d’adresses.  Vous devriez voir certains objets visuels flottantes déplacement dans l’écran.  En outre, vous pouvez utiliser **Get-ServiceFabricApplication** pour vérifier l’état de l’application.

## <a name="step-2-update-the-visual-objects-sample"></a>Étape 2 : Mettre à jour l’échantillon objets visuels

Vous remarquerez peut-être qu’avec la version qui a été déployée à l’étape 1, les objets visuels ne pivotent pas. Nous allons mettre à niveau une où les objets visuels également faire pivoter de cette application.

Sélectionnez le projet VisualObjects.ActorService au sein de la solution VisualObjects et ouvrez le fichier StatefulVisualObjectActor.cs. Dans le fichier, accédez à la méthode `MoveObject`, commentez `this.State.Move()`et ne commentez pas `this.State.Move(true)`. Cette modification fait pivoter les objets une fois que le service est mis à niveau.

Nous devons également à jour le fichier *ServiceManifest.xml* (sous PackageRoot) du projet **VisualObjects.ActorService**. Mettez à jour la *CodePackage* et la version du service 2.0 et les lignes correspondantes dans le fichier *ServiceManifest.xml* .
Vous pouvez utiliser l’option Visual Studio *Modifier les fichiers manifeste* après avec le bouton droit sur la solution d’apporter les modifications de fichier manifeste.


Une fois les modifications effectuées, le manifeste doit ressembler à ce qui suit (parties en surbrillance indique les modifications apportées) :

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Maintenant le fichier *ApplicationManifest.xml* (situé sous le projet **VisualObjects** sous la solution **VisualObjects** ) est mis à jour vers la version 2.0 du projet **VisualObjects.ActorService** . En outre, la version de l’Application est mis à jour à 2.0.0.0 comprise entre 1.0.0.0. *ApplicationManifest.xml* doit ressembler à l’extrait de code suivante :

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


À présent, générez le projet en sélectionnant simplement le projet **ActorService** , puis avec le bouton droit et en sélectionnant l’option **créer** dans Visual Studio. Si vous sélectionnez **tout reconstruire**, vous devez mettre à jour les versions pour tous les projets, dans la mesure où le code aurait été modifié. Ensuite, nous allons empaqueter l’application mis à jour par clic droit sur ***VisualObjectsApplication***, sélectionnez le Menu de tissu de Service et choisissez **Package**. Cette action crée un package d’application qui peut être déployé.  Votre application mise à jour est prête à être déployée.


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Étape 3 : Choisir stratégies d’intégrité et les paramètres de mise à niveau

Familiarisez-vous avec l' [application mise à niveau les paramètres](service-fabric-application-upgrade-parameters.md) et [processus de mise à niveau](service-fabric-application-upgrade.md) pour obtenir une bonne compréhension des différents paramètres de mise à niveau, les délais et critère de santé appliqué. Pour cette procédure, le critère d’évaluation d’intégrité du service est défini par défaut (et recommandé) valeurs, ce qui signifie que tous les services et instances doivent être _correcte_ après la mise à niveau.  

Toutefois, nous allons augmenter *HealthCheckStableDuration* à 60 secondes (de sorte que les services fonctionnent correctement au moins 20 secondes avant la mise à niveau se déroule sur le domaine de mise à jour suivant).  Nous allons également définir *UpgradeDomainTimeout* 1200 secondes et *UpgradeTimeout* 3000 secondes.

Pour finir, nous allons également définir *UpgradeFailureAction* à restaurer. Cette option nécessite tissu de Service restaurer l’application à la version précédente s’il rencontre des problèmes pendant la mise à niveau. Par conséquent, lors du démarrage de la mise à niveau (à l’étape 4), les paramètres suivants sont spécifiés :

FailureAction = retour arrière

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>Étape 4 : Préparation de demande de mise à niveau

L’application est désormais généré et prêt à être mis à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur et type **Get-ServiceFabricApplication**, il doit vous informer qu’il est de type d’application 1.0.0.0 de **VisualObjects** qui a été déployé.  

Le package d’application est stocké sous le chemin d’accès relatif suivant l’endroit où vous avez décompressé le Kit de développement TISSU Service - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Vous devez trouver un dossier « Package » dans ce répertoire, où se trouve le package d’application. Vérifier les horodatages pour vous assurer qu’il s’agit de la dernière version (vous devrez peut-être également modifier les chemins d’accès de manière appropriée).

Maintenant nous allons copier le package d’application mis à jour dans la ImageStore de tissu de Service (où les packages d’application sont stockés par tissu de Service). Le paramètre *ApplicationPackagePathInImageStore* vous informe tissu de Service dans lequel il peut trouver le package d’application. Nous avons mis l’application mis à jour en « VisualObjects\_V2 » avec la commande suivante (vous devrez peut-être modifier les chemins d’accès à nouveau correctement).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

L’étape suivante consiste à enregistrer cette application avec tissu de Service, qui peut être effectuée à l’aide de la commande suivante :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si la commande précédente n’aboutit pas, il est probable que vous avez besoin d’une reconstruction de tous les services. Comme indiqué à l’étape 2, vous devrez peut-être mettre à jour votre version serviceWeb également.

## <a name="step-5-start-the-application-upgrade"></a>Étape 5 : Commencer la mise à niveau de l’application

À présent, nous allons terminés pour démarrer la mise à niveau de l’application à l’aide de la commande suivante :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Le nom de l’application est la même tel qu’il a été décrit dans le fichier *ApplicationManifest.xml* . Service TISSU utilise ce nom pour identifier quelle application est prise mis à niveau. Si vous définissez les délais d’expiration trop courte, vous pouvez rencontrer un message d’erreur qui indique le problème. Reportez-vous à la section Résolution des problèmes ou augmenter les délais d’expiration.

À présent, le produit de mise à niveau de l’application, vous pouvez surveiller à l’aide de l’Explorateur de tissu de Service, ou à l’aide de la session PowerShell suivante commande : **TISSU Get-ServiceFabricApplicationUpgrade : / VisualObjects**.

En quelques minutes, l’état que vous avez obtenu à l’aide de la commande PowerShell précédente, doit indiquer que tous les domaines de mise à jour ont été mis à niveau (terminée). Et vous devez trouver que les objets visuels dans la fenêtre du navigateur ont commencé rotation !

Vous pouvez essayer la mise à niveau à partir de la version 2 à la version 3 ou version 2 à la version 1 comme un exercice. Déplacement de la version 2 à la version 1 est également considéré comme une mise à niveau. Lire des délais d’expiration effectuer vous-même familiarisé avec les stratégies d’intégrité. Lorsque vous déployez à un cluster Azure, les paramètres doivent être définis correctement. Il est judicieux de définir les délais d’expiration de manière conventionnelle.


## <a name="next-steps"></a>Étapes suivantes

[La mise à niveau votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide dans une mise à niveau de l’application à l’aide de Visual Studio.

Contrôler comment votre application mises à niveau à l’aide de [paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Vérifiez les mises à niveau votre application compatibles en apprendre à utiliser la [sérialisation de données](service-fabric-application-upgrade-data-serialization.md).

Découvrez comment utiliser les fonctionnalités avancées nécessitent pendant la mise à niveau votre application en faisant référence à [des rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en faisant référence à la procédure décrite dans les [mises à niveau de résolution des problèmes application](service-fabric-application-upgrade-troubleshooting.md).
