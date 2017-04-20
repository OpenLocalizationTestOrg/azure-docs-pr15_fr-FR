<properties
   pageTitle="Créer des rapports et vérifier le fonctionnement du tissu de Service Azure | Microsoft Azure"
   description="Découvrez comment envoyer des rapports d’intégrité à partir de votre code de service et vérifier l’état de votre service en utilisant les outils d’analyse d’intégrité fournissant Azure Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Créer des rapports et vérifiez l’état du service
Lorsque vos services rencontrent des problèmes, votre capacité à répondre aux et corriger les incidents et pannes dépend de votre capacité à détecter les problèmes rapidement. Si vous signalez des problèmes et échecs pour le Gestionnaire d’état Azure Service tissu à partir de votre code de service, vous pouvez utiliser les outils TISSU Service fournit pour vérifier l’état d’intégrité d’analyse du fonctionnement standard.

Il existe deux manières que vous pouvez le signaler d’intégrité du service :

- Utiliser les objets [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) ou [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
Vous pouvez utiliser la `Partition` et `CodePackageActivationContext` objets pour signaler l’état des éléments qui font partie du contexte actuel. Par exemple, le code qui s’exécute comme une partie du réplica peut créer des rapports d’intégrité uniquement sur ce réplica, la partition auquel il appartient et l’application qu’il s’agit d’un composant de.

- Utiliser `FabricClient`.   
Vous pouvez utiliser `FabricClient` à état des rapports à partir du code de service si le cluster n’est pas [sécurisée](service-fabric-cluster-security.md) ou si le service est en cours d’exécution avec des privilèges d’administrateur. Cela n’être vrai dans la plupart des scénarios réels. Avec `FabricClient`, vous pouvez le signaler santé sur n’importe quelle entité qui fait partie du cluster. Dans l’idéal, cependant, code de service doit uniquement envoyer des rapports qui sont liées à son propre état.

Cet article vous guide dans un exemple qui rapports d’intégrité du code de service. L’exemple illustre également comment les outils fournissant tissu de Service peuvent être utilisés pour vérifier l’état d’intégrité. Cet article est destiné à être une brève introduction à l’état des fonctionnalités de tissu de Service de contrôle. Pour plus d’informations, vous pouvez lire la série d’articles détaillés à propos d’intégrité qui commencent par le lien à la fin de cet article.

## <a name="prerequisites"></a>Conditions préalables
Vous devez disposer des éléments suivants installés :

   * Visual Studio 2015
   * Service TISSU SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Pour créer un cluster de développement sécurisé local
- Ouvrez PowerShell avec privilèges d’administrateur, puis exécutez les commandes suivantes.

![Commandes qui montrent comment créer un cluster de développement sécurisé](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Pour déployer une application et vérifier son état d’intégrité

1. Ouvrir Visual Studio en tant qu’administrateur.

2. Créer un projet à l’aide du modèle de **Service avec état** .

    ![Créer une application de Service tissu avec le Service d’état](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Appuyez sur **F5** pour exécuter l’application en mode débogage. L’application sera déployée sur le cluster local.

4. Une fois que l’application est exécutée, avec le bouton droit de l’icône Gestionnaire de Cluster Local dans la zone de notification et sélectionnez **Gérer le Cluster Local** dans le menu contextuel pour ouvrir l’Explorateur de tissu de Service.

    ![Ouvrez l’Explorateur de tissu de Service à partir de la zone de notification](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. L’état de l’application doit être affiché comme dans cette image. Pour l’instant, l’application doit être exact sans erreurs.

    ![Application exact dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Vous pouvez également vérifier l’état à l’aide de PowerShell. Vous pouvez utiliser ```Get-ServiceFabricApplicationHealth``` pour vérifier l’état d’une application et vous pouvez utiliser ```Get-ServiceFabricServiceHealth``` pour vérifier l’état d’un service. L’état d’intégrité de l’application même dans PowerShell est dans cette image.

    ![Application exact dans PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Pour ajouter des événements d’état personnalisé à votre code de service
Les modèles de projet tissu de Service dans Visual Studio contiennent des exemples de code. Les étapes suivantes montrent comment vous pouvez signaler les événements d’état personnalisé à partir de votre code de service. Ces rapports automatiquement apparaît dans les outils standards pour le contrôle d’état que Service TISSU fournit, tels que Service TISSU Explorer affichage Azure santé portail et PowerShell.

1. Rouvrez l’application que vous avez créée précédemment dans Visual Studio, ou créez une nouvelle application à l’aide du modèle de Visual Studio **Service avec état** .

2. Ouvrez le fichier Stateful1.cs, puis recherchez la `myDictionary.TryGetValueAsync` appeler le `RunAsync` méthode. Vous pouvez voir que cette méthode retourne un `result` qui contient la valeur actuelle du compteur, car la logique clée de cette application est à conserver un décompte en cours d’exécution. S’il s’agissait d’une application réelle, et si l’absence de résultat représenté une défaillance, vous pouvez ajouter un indicateur à cet événement.

3. Pour signaler un événement d’état lorsque l’absence de résultat représente une défaillance, ajoutez les étapes suivantes.

    un. Ajouter la `System.Fabric.Health` espace de noms vers le fichier Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Ajoutez le code suivant après la `myDictionary.TryGetValueAsync` appeler

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Nous rapport d’intégrité réplica, car il est signalée à partir d’un service dynamique. La `HealthInformation` paramètre stocke des informations sur le problème de santé qui est signalée.

    Si vous avez créé un service sans état, utilisez le code suivant

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Si votre service s’exécute avec des privilèges d’administrateur ou si le cluster n’est pas [sécurisé](service-fabric-cluster-security.md), vous pouvez également utiliser `FabricClient` à état des rapports comme indiqué dans les étapes suivantes.  

    un. Créer la `FabricClient` instance après la `var myDictionary` déclaration.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Ajoutez le code suivant après la `myDictionary.TryGetValueAsync` appeler.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Nous allons simuler cette erreur et voir apparaissent-elles dans les outils d’analyse d’intégrité. Pour simuler une panne, commentez la première ligne dans le code de création de rapports d’intégrité que vous avez ajouté précédemment. Une fois que vous Commentez la première ligne, le code ressemblera à l’exemple suivant.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Ce code se déclenche maintenant cet état d’intégrité chaque fois `RunAsync` s’exécute. Après avoir effectué la modification, appuyez sur **F5** pour exécuter l’application.

6. Une fois que l’application est en cours d’exécution, ouvrez l’Explorateur de tissu de Service pour vérifier l’état d’intégrité de l’application. Cette fois, Service TISSU Explorer vous montrent que l’application ne fonctionne pas correctement. Il s’agit en raison de l’erreur qui a été signalé à partir du code que nous avons ajouté précédemment.

    ![Applications défectueux dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si vous sélectionnez réplica principal dans l’arborescence de l’Explorateur de tissu de Service, vous verrez que **État d’intégrité** indique un message d’erreur, trop. Service TISSU Explorer affiche également les détails du rapport d’intégrité qui ont été ajoutés à la `HealthInformation` paramètre dans le code. Vous pouvez voir les mêmes rapports d’intégrité dans PowerShell et le portail Azure.

    ![État du réplica dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ce rapport reste dans le Gestionnaire d’état jusqu'à ce qu’il est remplacé par un autre rapport ou jusqu'à ce que ce réplica est supprimé. Étant donné que nous n’a pas défini `TimeToLive` pour cet état d’intégrité dans la `HealthInformation` objet, le rapport n’expire jamais.

Nous vous recommandons que santé doit être signalée au niveau plus granulaires, c'est-à-dire dans ce cas le réplica. Vous pouvez également signaler santé sur `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

À l’état du rapport sur `Application`, `DeployedApplication`, et `DeployedServicePackage`, utilisez `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Étapes suivantes
[Approfondie sur état tissu de Service](service-fabric-health-introduction.md)
