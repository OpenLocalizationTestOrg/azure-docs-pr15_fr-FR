<properties
   pageTitle="Amener Chaos dans Service TISSU clusters | Microsoft Azure"
   description="Utilisation d’Injection et Cluster Analysis Services API pour gérer Chaos dans le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Amener Chaos contrôlé dans clusters tissu de Service
Systèmes distribués à grande échelle comme infrastructure cloud est fondamentalement non fiable. TISSU Service Azure permet aux développeurs écrire des services fiables en haut d’une infrastructure de sources non fiable. Pour écrire des services robustes, les développeurs doivent être en mesure d’entraîner des erreurs par rapport à ce sources non fiable infrastructure pour tester la stabilité de leurs services.

La probabilité d’Injection et le Service d’analyse Cluster (également connu sous le Service d’analyse de défaillance) permet aux développeurs à inciter les actions de défaillance pour tester des services. Toutefois, les erreurs simulés vous aider à vous uniquement jusqu'à présent. Pour prendre davantage le test, vous pouvez utiliser Chaos.

Chaos simule défauts continues, entrelacés (ordonnés et anormal) dans l’ensemble du cluster sur une période prolongée. Après avoir configuré Chaos avec la vitesse et le type de fautes, vous pouvez commencer ou arrêter via c# API ou PowerShell pour générer des erreurs dans le cluster et votre service.

Pendant l’exécution de Chaos, elle produit des événements qui capturent l’état de l’exécution pour le moment. Par exemple, un ExecutingFaultsEvent contient toutes les erreurs qui sont en cours d’exécution dans cette itération. Un ValidationFailedEvent contient les détails d’une défaillance qui a été trouvée lors de la validation de cluster. Vous pouvez appeler l’API GetChaosReportAsync pour obtenir le rapport de portions Chaos.

## <a name="faults-induced-in-chaos"></a>Erreurs qui en résultent Chaos
Chaos génère des erreurs sur l’ensemble du cluster tissu de Service et les compresse erreurs qui sont affichent en mois ou années en quelques heures. La combinaison des pannes entrelacés avec le taux de défaillance élevé de recherche des cas de coin qui sont ignorés dans le cas contraire. Cet exercice Chaos permet d’accéder à une amélioration significative de la qualité du code du service.

Chaos provoque des erreurs dans les catégories suivantes :

 - Redémarrez un nœud
 - Redémarrez un package de code déployé
 - Supprimer un réplica
 - Redémarrer une copie
 - Déplacer un réplica principal (configurable)
 - Déplacer un réplica secondaire (configurable)

Chaos s’exécute en plusieurs itérations. Chaque itération se compose des défaillances et de validation de cluster pour la période spécifiée. Vous pouvez configurer le temps passé pour le cluster placez son et de validation réussir. Si une défaillance se trouve dans la validation de cluster, Chaos génère et persiste un ValidationFailedEvent avec l’horodatage UTC et les détails de l’échec.

Par exemple, considérez une instance de Chaos est configuré pour s’exécuter pendant une heure avec un maximum de trois erreurs simultanées. Chaos provoque des erreurs de trois, puis valide le bon fonctionnement du cluster. Il parcourt précédent passe étape jusqu'à ce qu’il soit explicitement arrêté via l’API StopChaosAsync ou une heure. Si le cluster devient incorrect dans n’importe quel itération (autrement dit, il ne pas placez son dans un délai configuré), Chaos génère une ValidationFailedEvent. Cet événement indique que quelque chose s’est produite et que vous devrez peut-être une analyse plus poussée.

Dans sa forme actuelle, Chaos provoque uniquement les erreurs approuvés. Cela signifie que, en l’absence de fautes externes, quorum perte ou la perte de données ne se produit jamais.

## <a name="important-configuration-options"></a>Options de configuration importantes
 - **TimeToRun**: temps Total que s’exécute Chaos avant d’être terminé avec succès. Vous pouvez arrêter Chaos avant qu’elle est exécutée pour la période TimeToRun via l’API StopChaos.
 - **MaxClusterStabilizationTimeout**: la quantité maximale de délai d’attente pour le cluster devenir correcte avant de vérifier dessus à nouveau. Cette attente consiste à réduire la charge sur le cluster pendant que récupérer. Les vérifications effectuées sont :
    - Si le bon fonctionnement du cluster est OK
    - Si l’état du service est OK
    - Si le réplica cible définie la taille est réalisé pour la partition de service
    - Qu’il existe aucun réplica InBuild
 - **MaxConcurrentFaults**: le nombre maximal de fautes simultanées qui sont dues dans chaque itération. Le Chaos le numéro, plu une version ultérieure est. Cela entraîne basculement plus complexes et les combinaisons de transition. Chaos garantit que, en l’absence de fautes externe, aucune perte quorum ou une perte de données, quelle que soit une valeur élevée comment cette configuration présente.
 - **EnableMoveReplicaFaults**: Active ou désactive les pannes occasionnant réplicas principales ou secondaires à déplacer. Ces erreurs sont désactivées par défaut.
 - **WaitTimeBetweenIterations**: la quantité de délai d’attente entre les itérations, autrement dit, après un arrondi des défaillances et validation correspondante.
 - **WaitTimeBetweenFaults**: la quantité de délai d’attente entre deux défauts consécutives dans une itération.

## <a name="how-to-run-chaos"></a>Comment faire pour exécuter Chaos
**C# :**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell :**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
