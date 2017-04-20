<properties 
    pageTitle="Comment administrer Azure Redis Cache | Microsoft Azure"
    description="Découvrez comment effectuer des tâches d’administration tels que les mises à jour redémarrage et planifiez pour Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Comment administrer Azure Redis Cache

Cette rubrique décrit comment effectuer des tâches d’administration tels que le redémarrage et la planification des mises à jour pour vos instances de Cache Redis Azure.

>[AZURE.IMPORTANT] Les paramètres et les fonctionnalités décrites dans cet article sont uniquement disponibles pour cache de niveau Premium.


## <a name="administration-settings"></a>Paramètres d’administration

Les paramètres de Cache Redis Azure **Administration** vous permettent de vous permet d’effectuer les tâches administratives suivantes pour le cache de résolution premium. Pour accéder aux paramètres d’administration, cliquez sur **paramètres** ou **tous les paramètres** de la carte de Cache Redis et faire défiler à la section **Administration** de la carte de **paramètres** .

![Administration](./media/cache-administration/redis-cache-administration.png)

-   [Redémarrage](#reboot)
-   [Mises à jour de la planification](#schedule-updates)

## <a name="reboot"></a>Redémarrage

La carte **redémarrer** vous permet de redémarrer une ou plusieurs des nœuds du cache. Cela vous permet de tester votre application pour la résilience en cas de panne.

![Redémarrage](./media/cache-administration/redis-cache-reboot.png)

Si vous avez un cache premium avec cluster activé, vous pouvez sélectionner quels milieu des fragments du cache de redémarrer.

![Redémarrage](./media/cache-administration/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs des nœuds de cache, sélectionnez les nœuds de votre choix, puis cliquez sur **redémarrer**. Si vous avez un cache premium avec cluster activé, sélectionnez le shard(s) redémarrer, puis cliquez sur **redémarrer**. Après quelques minutes, le redémarrage ou les nœuds sélectionné et sont reconnecter quelques minutes plus tard.

L’impact sur les applications clientes varie selon l’ou les nœuds que vous redémarrez.

-   **Masque** - lors du redémarrage le nœud maître, Azure Redis Cache bascule vers le nœud réplica et promeut au masque. Pendant ce basculement, il peut y avoir un court intervalle dans lequel les connexions peuvent échouer dans le cache.
-   **Esclave** - lors du redémarrage le nœud esclave, il n’est généralement aucun impact sur les clients de cache.
-   **Les deux maîtres et esclaves** - lors du redémarrage sont les deux nœuds de cache, toutes les données sont perdues dans le cache et les connexions de l’échec du cache jusqu'à ce que le nœud principal revient en ligne. Si vous avez configuré [permanence des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache revient en ligne. Notez que toutes les écritures cache qui se sont produites après la dernière sauvegarde sont perdus.
-   **Ou les nœuds d’un cache premium avec cluster activé** - lorsque vous redémarrez l’ou les nœuds d’un cache premium avec cluster activé, le comportement est le même que lorsque vous redémarrez ou les nœuds d’un cache non groupé.


>[AZURE.IMPORTANT] Redémarrage est disponible uniquement pour cache de niveau Premium.

## <a name="reboot-faq"></a>Redémarrez le Forum aux questions

-   [Le nœud dois-je redémarrer pour tester mon application ?](#which-node-should-i-reboot-to-test-my-application)
-   [Puis-je redémarrer le cache pour effacer les connexions client ?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Allez perdre des données mon cache si faire un redémarrage ?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [Puis-je redémarrer mon cache à l’aide de PowerShell, infrastructure du langage commun ou autres outils de gestion ?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [Quelle tarification niveaux peut utiliser la fonctionnalité redémarrage ?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Le nœud dois-je redémarrer pour tester mon application ?

Pour tester la résilience de votre application contre toute panne du nœud principal de cache, redémarrez le nœud **maître** . Pour tester la résilience de votre application contre toute panne du nœud secondaire, redémarrez le nœud **esclave** . Pour tester la résilience de votre application par rapport à la panne totale du cache, redémarrez **les deux** nœuds.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Puis-je redémarrer le cache pour effacer les connexions client ?

Oui, si vous réinitialisez le cache de toutes les connexions clientes sont désactivées. Cette option est utile dans le cas où toutes les connexions clientes sont utilisées, par exemple en raison d’une erreur logique ou un bogue dans l’application cliente. Chaque niveau de tarification comporte différentes [limites de connexion client](cache-configure.md#default-redis-server-configuration) pour les différentes tailles, et une fois que ces limites sont atteintes, aucune plusieurs connexions client ne sont acceptées. Redémarrer le cache fournit une méthode pour supprimer toutes les connexions clientes.

>[AZURE.IMPORTANT] Si vos connexions client disparaissent en raison d’une erreur logique ou les bogues dans votre code client, notez que StackExchange.Redis se reconnectera automatiquement une fois que le nœud Redis est en ligne. Si le problème sous-jacent n’est pas résolu, les connexions client continuent à être utilisées jusqu'à.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Allez perdre des données mon cache si faire un redémarrage ?

Si vous redémarrez le **masque** et le **esclave** nœuds toutes les données dans le cache (ou dans cette partagé Si vous utilisez un cache premium avec cluster activé) est perdue. Si vous avez configuré [permanence des données](cache-how-to-premium-persistence.md), la sauvegarde la plus récente est restaurée lorsque le cache revient en ligne. Notez que toutes les écritures cache ayant eu lieu après que la sauvegarde a été effectuée sont perdues.

Si vous redémarrez simplement un des nœuds, donnée n’est perdue en règle générale, mais il peut être. Par exemple, si le nœud maître est réinitialisé et un cache d’écriture est en cours, les données à partir de l’écriture de cache est perdue. Un autre scénario perte de données serait si vous redémarrez un nœud et l’autre nœud qui se passe pour aller vers le bas en raison d’une défaillance en même temps. Pour plus d’informations sur les causes possibles de perte de données, voir [Mes données dans Redis est passée ?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Puis-je redémarrer mon cache à l’aide de PowerShell, infrastructure du langage commun ou autres outils de gestion ?

Oui, pour PowerShell instructions voir [redémarrer un cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Quelle tarification niveaux peut utiliser la fonctionnalité redémarrage ?

Redémarrage est disponible uniquement dans la prime tarifs niveau.

## <a name="schedule-updates"></a>Mises à jour de la planification

La carte de **mises à jour de la planification** vous permet de désigner une période de maintenance pour le cache de résolution. Lorsque la fenêtre de maintenance est spécifiée, des mises à jour du serveur Redis sont apportées au cours de cette fenêtre. Notez que la fenêtre de maintenance s’applique uniquement aux Redis mises à jour du serveur et pas de n’importe quel Azure mises à jour ou mises à jour du système d’exploitation des ordinateurs virtuels hébergeant le cache.

![Mises à jour de la planification](./media/cache-administration/redis-schedule-updates.png)

Pour spécifier une période de maintenance, vérifiez vos jours et spécifier l’heure de début de fenêtre de maintenance pour chaque jour, puis cliquez sur **OK**. Notez que l’horaire de la fenêtre maintenance est au format UTC. 

>[AZURE.NOTE] La fenêtre de gestion par défaut des mises à jour est 5 heures. Cette valeur n’est pas configurable à partir du portail Azure, mais vous pouvez le configurer à l’aide de PowerShell le `MaintenanceWindow` paramètre de l’applet de commande [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Pour plus d’informations, voir [puis-je gérées la fonctionnalité mises à jour planifiées à l’aide de PowerShell, infrastructure du langage commun ou autres outils de gestion des ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Planifier des mises à jour Forum aux questions

-   [Quand mises à jour se produisent si je n’utilise pas la fonctionnalité de planification de mises à jour ?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Quels types de mises à jour apportées au cours de la fenêtre de maintenance planifiée ?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [J’ai pouvez gérées mises à jour planifiées à l’aide de PowerShell, infrastructure du langage commun ou autres outils de gestion ?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [Quelle tarification niveaux peut utiliser la fonctionnalité de mises à jour de planification ?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quand mises à jour se produisent si je n’utilise pas la fonctionnalité de planification de mises à jour ?

Si vous ne spécifiez pas une période de maintenance, mises à jour peuvent être effectuées à tout moment.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Quels types de mises à jour apportées au cours de la fenêtre de maintenance planifiée ?

Uniquement Redis serveur mises à jour apportées au cours de la fenêtre de maintenance planifiée. La fenêtre Gestion ne s’applique pas aux mises à jour Azure ou les mises à jour pour le système d’exploitation machine virtuelle.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>J’ai pouvez gérées mises à jour planifiées à l’aide de PowerShell, infrastructure du langage commun ou autres outils de gestion ?

Oui, vous pouvez gérer vos mises à jour planifiées à l’aide des applets de commande PowerShell suivante.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Nouvelle AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Nouvelle AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Supprimer AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Quelle tarification niveaux peut utiliser la fonctionnalité de mises à jour de planification ?

Mises à jour du calendrier est disponible uniquement dans la prime tarifs niveau.

## <a name="next-steps"></a>Étapes suivantes

-   Explorer d’autres fonctionnalités de [niveau de premium Azure Redis Cache](cache-premium-tier-intro.md) .





