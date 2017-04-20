<properties
   pageTitle="La journalisation des diagnostics lot Azure | Microsoft Azure"
   description="Enregistrer et analyser les événements de journal de diagnostic pour les ressources de compte Azure lot tels que des pools et des tâches."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Journalisation des diagnostics lot Azure

Comme avec de nombreux services Azure, le service lot émet un journal des événements pour certaines ressources pendant la durée de vie de la ressource. Vous pouvez activer les journaux de diagnostic Azure lot pour enregistrer les événements pour les ressources, telles que des pools et des tâches et utilisez les journaux de diagnostic évaluation et de suivi. Créent des événements comme pool, pool delete, début, tâche terminée et autres personnes sont inclus dans les journaux de diagnostic lot.

>[AZURE.NOTE] Cet article décrit la journalisation des événements pour les ressources de compte lot eux-mêmes, pas de tâche et données de sortie de la tâche. Pour plus d’informations sur le stockage des données de sortie de vos projets et des tâches, voir [sortie de projet et la tâche est pas conservé Azure lot](batch-task-output.md).

## <a name="prerequisites"></a>Conditions préalables

* [Compte lot Azure](batch-account-create-portal.md)

* [Compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Pour conserver les journaux de diagnostic lot, vous devez créer un compte de stockage Azure où Azure stockera les journaux. Vous spécifiez ce compte de stockage lorsque vous [activez la journalisation des diagnostics](#enable-diagnostic-logging) pour votre compte lot. Le compte de stockage que vous spécifiez lorsque vous activez le journal de collection de sites n’est pas un compte de stockage liées visé aux articles [packages d’application](batch-application-packages.md) et [persistance de sortie de tâche](batch-task-output.md) .

  >[AZURE.WARNING] Vous êtes **facturée** pour les données stockées dans votre compte de stockage Azure. Cela inclut les journaux de diagnostic mentionnés dans cet article. Gardez à l’esprit lorsque vous créez votre [stratégie de rétention de journal](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Activer la journalisation des Diagnostics

Journalisation des diagnostics n’est pas activée par défaut pour votre compte lot. Vous devez explicitement activer la journalisation des diagnostics pour chaque compte lot que vous voulez contrôler :

[Comment activer la collecte des journaux de Diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Nous vous conseillons de lire l’article complet de la [vue d’ensemble des journaux de Diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pour mieux comprendre non seulement comment faire pour activer la journalisation, mais les catégories de journal pris en charge par les différents services Azure. Par exemple, lot Azure prend actuellement en charge une catégorie de journal : **Les journaux de Service**.

## <a name="service-logs"></a>Journaux de service

Les journaux de Service lot Azure contenir d’événements émis par le service Azure lot pendant la durée de vie d’une ressource par lots comme une liste ou une tâche. Chaque événement émis par lot est stocké dans le compte de stockage spécifié au format JSON. Par exemple, il s’agit le corps d’un exemple **pool de créer un événement**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Chaque corps de l’événement se trouve dans un fichier .json dans le compte de stockage Azure spécifié. Si vous voulez accéder directement aux journaux, vous souhaiterez peut-être examiner le [schéma de journaux de Diagnostic dans le compte de stockage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Événements du journal de service

Le service lot émet actuellement les événements de journal des services suivants. Cette liste est peut-être pas exhaustive, étant donné que des événements supplémentaires ont été ajoutés dans la mesure où la dernière mise à jour de cet article.

| **Événements du journal de service** |
| ------------------ |
| [Créer du pool][pool_create] |
| [Pool supprimer commencer][pool_delete_start] |
| [Supprimer du pool complète][pool_delete_complete] |
| [Début de redimensionnement pool][pool_resize_start] |
| [Redimensionnement de pool complète][pool_resize_complete] |
| [Début de la tâche][task_start] |
| [Tâche terminée][task_complete] |
| [Échec de la tâche][task_fail] |

## <a name="next-steps"></a>Étapes suivantes

En plus de stockage des événements de journal de diagnostic dans un compte de stockage Azure, vous pouvez également diffuser en continu les événements du lot Service journal à un [Concentrateur d’événement Azure](../event-hubs/event-hubs-what-is-event-hubs.md)et les envoyer à [Azure journal Analytique](../log-analytics/log-analytics-overview.md).

* [Les journaux de Diagnostic Azure aux événements Hubs de flux](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Diffuser en continu les événements de diagnostic lot au service de pénétration données hautement extensibles, Hubs événement. Événement Hubs peuvent traiter des millions d’événements par seconde, que vous pouvez ensuite transformer et stocker à l’aide de n’importe quel fournisseur analytique en temps réel.

* [Analyser les journaux de diagnostic Azure à l’aide de journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)

  Envoyez votre les journaux de diagnostic à Analytique journal où vous pouvez les analyser dans le portail Suite de gestion des opérations (OMS), ou les exporter pour l’analyse dans Power BI ou Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
