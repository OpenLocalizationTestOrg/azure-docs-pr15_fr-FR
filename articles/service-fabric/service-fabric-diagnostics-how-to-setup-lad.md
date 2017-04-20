<properties
   pageTitle="Collecter les journaux à l’aide de Linux Azure Diagnostics | Microsoft Azure"
   description="Cet article décrit comment configurer Azure Diagnostics pour collecter les journaux d’un cluster de Service TISSU Linux exécutant dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecte des journaux à l’aide des Diagnostics Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Lorsque vous exécutez un cluster tissu de Service Azure, il est recommandé de collecter les journaux de tous les nœuds dans un emplacement central. Vous rencontrez des journaux dans un emplacement central facilite analyser et résoudre les problèmes, qu’ils soient dans vos services, votre application ou le cluster lui-même. Une façon de télécharger et de collecte des journaux consiste à utiliser l’extension Diagnostics Azure, qui télécharge les journaux au stockage Azure. Vous pouvez lire les événements à partir du stockage et placez-les dans un produit comme [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou une autre solution journal d’analyse.

## <a name="log-sources-that-you-might-want-to-collect"></a>Sources du journal que vous pouvez collecter
- **Les journaux tissu de service**: émis à partir de la plateforme via [LTTng](http://lttng.org) et téléchargés vers votre compte de stockage. Les journaux peuvent être opérationnels événements ou événements runtime émet la plateforme. Ces fichiers journaux est stockés dans l’emplacement qui spécifie le manifeste cluster. (Pour obtenir les détails de compte de stockage, recherchez la balise **AzureTableWinFabETWQueryable** et recherchez **StoreConnectionString**.)
- **Événements d’application**: émis à partir du code de votre service. Vous pouvez utiliser n’importe quelle solution de journalisation qui écrit textuel journaux--par exemple, LTTng. Pour plus d’informations, consultez la documentation LTTng sur traçage de votre application.  


## <a name="deploy-the-diagnostics-extension"></a>Déploiement de l’extension Diagnostics
La première étape de collecte des journaux consiste à déployer l’extension Diagnostics sur chacun des ordinateurs virtuels dans le cluster tissu de Service. L’extension Diagnostics collecte des journaux sur chaque ordinateur virtuel et les téléchargements pour le compte de stockage que vous spécifiez. Les étapes varient selon selon que vous utilisez le portail Azure ou le Gestionnaire de ressources Azure.

Pour déployer l’extension Diagnostics sur les ordinateurs virtuels du cluster dans le cadre de la création d’un cluster, définir **des Diagnostics** sur **activé**. Après avoir créé le cluster, vous ne pouvez pas modifier ce paramètre à l’aide du portail.

Ensuite, configurez Linux Azure Diagnostics (SCÉNARISTE) pour collecter les fichiers et les placer dans votre compte de stockage. Ce processus est expliqué en tant que scénario 3 (« télécharger les fichiers journaux ») dans l’article [SCÉNARISTE à l’aide de surveillance et diagnostiquer machines virtuelles Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Suivant ce processus obtient accès aux traces. Vous pouvez télécharger les traces sur un visualiseur de votre choix.

Vous pouvez également déployer l’extension Diagnostics à l’aide du Gestionnaire de ressources Azure. Le processus est similaire pour Windows et Linux et présentée pour les groupes de Windows dans un [comment collecter les journaux de Diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Vous pouvez également utiliser les opérations Management Suite, comme décrit dans les [Opérations de gestion des Suite journal Analytique avec Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Une fois que vous avez terminé cette configuration, l’agent SCÉNARISTE surveille les fichiers journaux spécifiés. Chaque fois qu’une nouvelle ligne est ajoutée au fichier, il crée une entrée de journal système qui est envoyée à l’espace de stockage que vous avez spécifié.


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus en détail les événements que vous devez examiner lors de la résolution des problèmes, consultez [LTTng documentation](http://lttng.org/docs) et [SCÉNARISTE à l’aide](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
