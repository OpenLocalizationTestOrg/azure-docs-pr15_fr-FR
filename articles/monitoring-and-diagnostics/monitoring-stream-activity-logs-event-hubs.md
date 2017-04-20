<properties
    pageTitle="Diffuser le journal d’activité Azure aux événements Hubs | Microsoft Azure"
    description="Découvrez comment diffuser le journal d’activité Azure aux événements Hubs."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="johnkem"/>

# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Flux de données du journal d’activité Azure aux Hubs d’événement
Le [**Journal d’activité Azure**](./monitoring-overview-activity-logs.md) peuvent être diffusées en temps quasi réel à n’importe quelle application à l’aide de l’option « Exporter » intégrée dans le portail, ou en activant l’Id de règle Service Bus dans un profil de journal via les applets de commande PowerShell Azure Azure infrastructure du langage commun.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Ce que vous pouvez faire avec le journal d’activité et Hubs d’événement
Voici quelques méthodes que vous utilisez la fonctionnalité de diffusion en continu du journal d’activité :

- **Flux de données vers des systèmes de journalisation et de télémétrie tiers** – au fil du temps, Hubs événement de diffusion en continu devient le mécanisme de canal votre journal d’activité dans SIEMs tiers et vous y connecter des solutions analytique.
- **Créer une plate-forme journalisation et télémétrie personnalisé** – si ou est déjà une plateforme de télémétrie personnalisés sont simplement penser à un projet, hautement scalable publication abonnement nature des événements Hubs vous permet d’acquisition flexible du journal d’activité. [Consultez le guide de Dan Rosanova à l’aide de Hubs événement dans une plateforme de télémétrie l’échelle internationale ici.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Activer la diffusion en continu du journal d’activité
Vous pouvez activer la diffusion en continu du journal d’activité par programme ou via le portail. Dans les deux cas, que vous avez choisi un Namespace Bus de Service et une stratégie d’accès partagé de cet espace de noms, et un concentrateur événement est créé dans cet espace de noms en cas de la première nouvel événement de journal d’activité. Si vous n’avez pas un Namespace Bus de Service, vous devez tout d’abord en créer un. Si vous avez précédemment diffusés en continu les événements du journal d’activité vers ce Namespace Bus de Service, le Hub de l’événement qui a été créé précédemment sera réutilisé. La stratégie d’accès partagé définit les autorisations qui comporte le mécanisme de diffusion en continu. Aujourd'hui, la diffusion à un événement Hubs nécessite des autorisations de **Gérer**, **Veuillez lire**et **Envoyer** . Vous pouvez créer ou modifier les stratégies d’accès Service Bus Namespace partagé dans le portail classique sous l’onglet « Configurer » pour votre Namespace Bus de Service. Pour mettre à jour le profil de journal journal d’activité pour inclure la diffusion en continu, l’utilisateur qui effectue la modification doit avoir l’autorisation ListKey sur cette règle Service Bus d’autorisation.

### <a name="via-azure-portal"></a>Via le portail Azure 
1. Accédez à la carte de **Journal d’activité de** l’aide du menu sur le côté gauche du portail.

    ![Accédez au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** en haut de la carte.

    ![Bouton Exporter dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Dans la carte qui s’affiche, vous pouvez sélectionner les régions pour lequel vous voulez événements de flux et les Namespace Bus de Service dans lequel vous souhaitez qu’un concentrateur événement par diffusion en continu de ces événements.

    ![Exporter la carte du journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliquées à votre abonnement.


### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Si un profil de journal existe déjà, vous devez tout d’abord supprimer ce profil.

1. Utiliser `Get-AzureRmLogProfile` pour déterminer si un profil de journal existe
2. Si c’est le cas, utilisez `Remove-AzureRmLogProfile` pour la supprimer.
3. Utiliser `Set-AzureRmLogProfile` pour créer un profil :

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

ID de la règle de Service Bus est une chaîne avec ce format : {service Nº ressource bus} /authorizationrules/ {nom de la clé}, par exemple 

### <a name="via-azure-cli"></a>Via Azure infrastructure du langage commun
Si un profil de journal existe déjà, vous devez tout d’abord supprimer ce profil.

1. Utiliser `azure insights logprofile list` pour déterminer si un profil de journal existe
2. Si c’est le cas, utilisez `azure insights logprofile delete` pour la supprimer.
3. Utiliser `azure insights logprofile add` pour créer un profil :

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

ID de la règle de Service Bus est une chaîne avec ce format : `{service bus resource ID}/authorizationrules/{key name}`.
 
## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment utiliser les données du journal d’événement Hubs ?
[Le schéma du journal d’activité est disponible ici](./monitoring-overview-activity-logs.md). Chaque événement se trouve dans un tableau d’objets BLOB JSON appelée « enregistrements ».

## <a name="next-steps"></a>Étapes suivantes
- [Archiver le journal d’activité à un compte de stockage](./monitoring-archive-activity-log.md)
- [Lisez la vue d’ensemble du journal d’activité Azure](./monitoring-overview-activity-logs.md)
- [Configurer une alerte basée sur un événement de journal d’activité](./insights-auditlog-to-webhook-email.md)
