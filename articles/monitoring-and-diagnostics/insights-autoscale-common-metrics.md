<properties
    pageTitle="Azure moniteur autoscaling courantes indicateurs. | Microsoft Azure"
    description="Découvrez les mesures couramment utilisés pour autoscaling vos Services en nuage, les Machines virtuelles et les applications Web."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Mesures courantes Azure moniteur autoscaling

Azure autoscaling moniteur vous permet de mettre à l’échelle le nombre d’instances en cours d’exécution vers le haut ou vers le bas, en fonction des données de télémétrie (métrique). Ce document décrit les mesures courantes que vous souhaiterez peut-être utiliser. Dans le portail Azure pour les Services de Cloud et de batteries de serveurs, vous pouvez choisir la mesure de la ressource à l’échelle par. Toutefois, vous pouvez également choisir n’importe quel métrique à partir d’une autre ressource à l’échelle par.

Voici les détails sur la façon de rechercher et afficher les mesures que vous voulez mettre à l’échelle par. Les éléments suivants s’applique pour suivre l’évolution ainsi que des jeux d’échelle Machine virtuelle.

## <a name="compute-metrics"></a>Calculer des indicateurs
Par défaut, machine virtuelle Azure v2 est fourni avec l’extension diagnostics configurée et qu’ils ont les mesures suivantes activés.

- [Métrique invité v2 machine virtuelle Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métrique invité Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Vous pouvez utiliser la `Get MetricDefinitions` API/PoSH/infrastructure du langage commun pour afficher les mesures disponibles pour la ressource VMSS. 

Si vous utilisez des jeux d’échelle machine virtuelle et vous ne voyez pas une mesure particulière répertoriée, il est probable *désactivé* dans votre numéro de poste diagnostics.

Si une mesure particulière n’est pas en cours échantillonnée ou transféré à la fréquence souhaitée, vous pouvez mettre à jour la configuration des diagnostics.

Si tous les cas ci-dessus sont vraie, puis passez en revue [Utiliser PowerShell pour activer les Diagnostics Azure dans une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) PowerShell pour configurer et mettre à jour votre numéro de poste Azure machine virtuelle Diagnostics pour activer la métrique. Cet article inclut également un exemple de fichier de configuration de diagnostics.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calculer les mesures pour v2 machine virtuelle Windows en tant qu’invité du système d’exploitation

Lorsque vous créez une nouvelle machine virtuelle (v2) dans Azure, diagnostics est activée en utilisant l’extension Diagnostics.

Vous pouvez générer une liste des mesures à l’aide de la commande suivante dans PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez créer une alerte pour les mesures suivantes.

|Métrique nom|   Unité|
|---|---|
|\Processor(_Total)\% temps processeur    |Pourcentage|
|\Processor(_Total)\% temps privilégié   |Pourcentage|
|\Processor(_Total)\% temps utilisateur |Pourcentage|
|Fréquence de \Processor \Processor informations (_Total) |Nombre|
|\System\Processes| Nombre|
|Nombre de \Thread \Processus (_Total)| Nombre|
|Nombre de \Handle \Processus (_Total)  |Nombre|
|\Memory\% octets dédiés utilisés   |Pourcentage|
|\Memory\Available octets|   Octets|
|\Memory\Committed octets    |Octets|
|Limite de \Memory\Commit|  Octets|
|\Memory\Pool de réserve|  Octets|
|\Memory\Pool paginée|   Octets|
|\PhysicalDisk(_Total)\% disque fois| Pourcentage|
|\PhysicalDisk(_Total)\% temps de lecture du disque|    Pourcentage|
|\PhysicalDisk(_Total)\% temps d’écriture de disque|   Pourcentage|
|\PhysicalDisk (_Total) \Disk transferts/s   |CountPerSecond|
|Lectures \Disk \PhysicalDisk (_Total)   |CountPerSecond|
|\PhysicalDisk (_Total) \Disk écritures/s  |CountPerSecond|
|\PhysicalDisk (_Total) \Disk octets/s   |BytesPerSecond|
|\Disk \PhysicalDisk (_Total) octets lus/s| BytesPerSecond|
|\PhysicalDisk (_Total) \Disk écriture octets/s |BytesPerSecond|
|\Avg \PhysicalDisk (_Total). Long.|  Nombre|
|\Avg \PhysicalDisk (_Total). Lecture long.| Nombre|
|\Avg \PhysicalDisk (_Total). Écriture long. |Nombre|
|\LogicalDisk(_Total)\% libérer de l’espace| Pourcentage|
|\LogicalDisk (_Total) \Free mégaoctets|   Nombre|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calculer les mesures pour Linux VM v2 en tant qu’invité du système d’exploitation

Lorsque vous créez une nouvelle machine virtuelle (v2) dans Azure, diagnostics est activée par défaut à l’aide de l’extension Diagnostics.

Vous pouvez générer une liste des mesures à l’aide de la commande suivante dans PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Vous pouvez créer une alerte pour les mesures suivantes.

|Métrique nom                            |Unité|
|---|---|
|\Memory\AvailableMemory                |Octets|
|\Memory\PercentAvailableMemory         |Pourcentage|
|\Memory\UsedMemory                     |Octets|
|\Memory\PercentUsedMemory              |Pourcentage|
|\Memory\PercentUsedByCache             |Pourcentage|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Octets|
|\Memory\PercentAvailableSwap           |Pourcentage|
|\Memory\UsedSwap                       |Octets|
|\Memory\PercentUsedSwap                |Pourcentage|
|\Processor\PercentIdleTime             |Pourcentage|
|\Processor\PercentUserTime             |Pourcentage|
|\Processor\PercentNiceTime             |Pourcentage|
|\Processor\PercentPrivilegedTime       |Pourcentage|
|\Processor\PercentInterruptTime        |Pourcentage|
|\Processor\PercentDPCTime              |Pourcentage|
|\Processor\PercentProcessorTime        |Pourcentage|
|\Processor\PercentIOWaitTime           |Pourcentage|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Secondes|
|\PhysicalDisk\AverageWriteTime         |Secondes|
|\PhysicalDisk\AverageTransferTime      |Secondes|
|\PhysicalDisk\AverageDiskQueueLength   |Nombre|
|\NetworkInterface\BytesTransmitted     |Octets|
|\NetworkInterface\BytesReceived        |Octets|
|\NetworkInterface\PacketsTransmitted   |Nombre|
|\NetworkInterface\PacketsReceived      |Nombre|
|\NetworkInterface\BytesTotal           |Octets|
|\NetworkInterface\TotalRxErrors        |Nombre|
|\NetworkInterface\TotalTxErrors        |Nombre|
|\NetworkInterface\TotalCollisions      |Nombre|




## <a name="commonly-used-web-server-farm-metrics"></a>Métrique Web (batterie de serveurs) couramment utilisés

Vous pouvez également effectuer échelle automatique en fonction de mesures du serveur web courantes telles que la file d’attente Http. Il est métrique nom est **HttpQueueLength**.  La section suivante répertorie les mesures de batterie de serveurs (Web Apps) serveur disponible.

### <a name="web-apps-metrics"></a>Indicateurs des applications Web

Vous pouvez générer une liste des mesures applications Web à l’aide de la commande suivante dans PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez émettre une alerte ou mettre à l’échelle par les mesures.

|Métrique nom        |Unité|
|---                |---|
|CpuPercentage      |Pourcentage|
|MemoryPercentage   |Pourcentage|
|DiskQueueLength    |Nombre|
|HttpQueueLength    |Nombre|
|BytesReceived      |Octets|
|Octets envoyés          |Octets|


## <a name="commonly-used-storage-metrics"></a>Fréquemment utilisé métriques de stockage
Vous pouvez mettre à l’échelle par la longueur de file d’attente de stockage, qui est le nombre de messages dans la file d’attente de stockage. File d’attente du stockage est une métrique spéciale et le seuil appliqué sera le nombre de messages par instance. Cela signifie que s’il existe deux instances et si le seuil est défini sur 100, il mettra à l’échelle lorsque le nombre total de messages dans la file d’attente est 200. Par exemple, 100 messages par instance.

Vous pouvez configurer il s’agit dans le portail Azure dans la carte de **paramètres** . Pour les jeux d’échelle machine virtuelle, vous pouvez mettre à jour le paramètre échelle automatique dans le modèle de processeur pour utiliser *metricName* comme *ApproximateMessageCount* et passez l’ID de la file d’attente de stockage comme *metricResourceUri*.

Par exemple, avec un compte de stockage classique la metricTrigger paramètre échelle incluront :

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Pour un compte de stockage (non classique), la metricTrigger inclut :

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Indicateurs de Bus des services couramment utilisés

Vous pouvez mettre à l’échelle par Service file d’attente longueur, qui est le nombre de messages dans la file d’attente Bus des services. Longueur file d’attente service est une métrique spéciale et le seuil spécifié appliqué sera le nombre de messages par instance. Cela signifie que s’il existe deux instances et si le seuil est défini sur 100, il mettra à l’échelle lorsque le nombre total de messages dans la file d’attente est 200. Par exemple, 100 messages par instance.

Pour les jeux d’échelle machine virtuelle, vous pouvez mettre à jour le paramètre échelle automatique dans le modèle de processeur pour utiliser *metricName* comme *ApproximateMessageCount* et passez l’ID de la file d’attente de stockage comme *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Bus des services, le concept de groupe de ressources n’existe pas mais le Gestionnaire de ressources Azure crée un groupe de ressources par défaut par région. Le groupe de ressources est généralement au format « Par défaut - ServiceBus-[Région] ». Par exemple, 'Par défaut-ServiceBus-EastUS', 'Par défaut-ServiceBus-WestUS', 'Par défaut-ServiceBus-AustraliaEast' etc..
