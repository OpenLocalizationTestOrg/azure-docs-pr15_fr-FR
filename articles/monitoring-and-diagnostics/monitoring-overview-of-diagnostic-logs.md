<properties
    pageTitle="Vue d’ensemble des journaux de Diagnostic Azure | Microsoft Azure"
    description="Découvrez ce que sont les journaux de Diagnostic Azure et comment vous pouvez les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure."
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
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Vue d’ensemble des journaux de Diagnostic Azure
**Les journaux de Diagnostic Azure** sont les journaux émis par une ressource qui fournissent des données fréquentes riches sur le fonctionnement de cette ressource. Le contenu de ces fichiers journaux varie selon le type de ressource (par exemple, journaux d’événements système Windows une catégorie de journal de Diagnostic pour machines virtuelles et des objets blob, table, et les journaux de file d’attente vous catégories de journaux de Diagnostic pour les comptes de stockage) et différer le [journal d’activité (auparavant connu sous le journal d’Audit ou journal opérationnel)](monitoring-overview-activity-logs.md), ce qui offre une vision les opérations effectuées sur les ressources dans votre abonnement. Pas toutes les ressources prend en charge le nouveau type de journaux de Diagnostic décrites ici. La liste des Services pris en charge ci-dessous montre les types de ressources prennent en charge les journaux de Diagnostic nouveau.

![Logique placement des journaux de Diagnostic](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de Diagnostic
Voici quelques éléments que vous pouvez faire avec les journaux de Diagnostic :

- Les enregistrer dans un **Compte de stockage** pour inspection audit ou manuelle. Vous pouvez spécifier la durée de conservation (en jours) en utilisant les **Paramètres de Diagnostic**.
- [Les **Événements Hubs** de flux](monitoring-stream-diagnostic-logs-to-event-hubs.md) de réception par un service tiers ou une solution personnalisée analytique tels que PowerBI.
- Les analyser avec [OMS journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Les journaux de diagnostic pour les ressources non cluster sont configurés à l’aide de paramètres de Diagnostic. **Les paramètres de diagnostic** pour un contrôle de ressource :

- Où les journaux de Diagnostic sont envoyés (compte de stockage, événement Hubs et/ou OMS journal Analytique).
- Les catégories de journal sont envoyés.
- Combien de temps chaque catégorie de journal doit être conservée dans un compte de stockage – une rétention de zéro jours signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, cette valeur peut être comprise entre 1 et 2147483647. Si vous définissez des stratégies de rétention, mais le stockage des journaux dans un compte de stockage est désactivé (par exemple si une seule Hubs événement ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres sont configurés facilement via la carte Diagnostics pour une ressource dans le portail Azure, aide des commandes PowerShell Azure et infrastructure du langage commun ou via l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [AZURE.WARNING] Les journaux de diagnostic et d’indicateurs pour les ressources de cluster (par exemple, machines virtuelles ou Service TISSU) utilisez [un mécanisme distinct pour la configuration et la sélection des données de sortie](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Comment activer la collecte des journaux de Diagnostic
Collecte des journaux de Diagnostic peut être activée dans le cadre de la création d’une ressource ou après la création d’une ressource via la carte de la ressource dans le portail. Vous pouvez également activer les journaux de Diagnostic à tout moment à l’aide des commandes PowerShell Azure ou infrastructure du langage commun, ou à l’aide de l’API REST de moniteur Azure.

> [AZURE.TIP] Il est possible que ces instructions ne s’appliquent pas directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spéciales qui peuvent s’appliquer à certains types de ressources.

[Cet article vous explique comment vous pouvez utiliser un modèle de ressources pour activer les paramètres de Diagnostic lors de la création d’une ressource](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Activer les journaux de Diagnostic dans le portail
Vous pouvez activer les journaux de Diagnostic dans le portail Azure lorsque vous créez des types de ressources cluster en activant l’extension Windows ou Linux Azure Diagnostics :

1.  Accédez à **Nouveau** et sélectionnez la ressource que qui vous intéresse.
2.  Après avoir configuré les paramètres de base et sélection d’une taille, dans la carte de **paramètres** , sous **suivi**, sélectionnez **activé** , puis choisissez un compte de stockage l’endroit où vous voulez stocker les journaux de Diagnostic. Vous êtes chargé débit normal pour le stockage et transactions lorsque vous envoyez des diagnostics à un compte de stockage.

    ![Activer les journaux de Diagnostic lors de la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  Cliquez sur **OK** et créer la ressource.

Pour les ressources non cluster, vous pouvez activer les journaux de Diagnostic dans le portail Azure après la création d’une ressource en procédant comme suit :

1.  Accédez à la carte pour la ressource et ouvrez la carte **Diagnostics** .
2.  Cliquez **sur** et choisissez un compte de stockage et/ou le Hub de l’événement.

    ![Activer les journaux de Diagnostic après la création de la ressource](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  Sous **journaux**, sélectionnez les **Catégories du journal** que vous voulez collecter ou un flux.
4.  Cliquez sur **Enregistrer**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Activer les journaux de Diagnostic via PowerShell
Pour activer les journaux de Diagnostic via les applets de commande PowerShell Azure, utilisez les commandes suivantes.

Pour activer le stockage des journaux de Diagnostic dans un compte de stockage, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

L’ID de compte de stockage est l’id de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu de journaux de Diagnostic à un concentrateur de l’événement, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

ID de la règle de Service Bus est une chaîne avec ce format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour autoriser l’envoi des journaux de Diagnostic dans un espace de travail journal Analytique, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] Le paramètre WorkspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.

Vous pouvez obtenir votre ID d’espace de travail journal Analytique dans le portail Azure.

Vous pouvez combiner ces paramètres pour permettre à plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-cli"></a>Activer les journaux de Diagnostic via l’infrastructure du langage commun
Pour activer les journaux de Diagnostic via l’infrastructure du langage commun Azure, utilisez les commandes suivantes :

Pour activer le stockage des journaux de Diagnostic dans un compte de stockage, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

L’ID de compte de stockage est l’id de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu de journaux de Diagnostic à un concentrateur de l’événement, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

ID de la règle de Service Bus est une chaîne avec ce format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour autoriser l’envoi des journaux de Diagnostic dans un espace de travail journal Analytique, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] Le paramètre workspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.

Vous pouvez obtenir votre ID d’espace de travail journal Analytique dans le portail Azure.

Vous pouvez combiner ces paramètres pour permettre à plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Activer les journaux de Diagnostic via l’API REST
Pour modifier les paramètres de Diagnostic à l’aide de l’API REST de moniteur Azure, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gérer les paramètres des diagnostics dans le portail

Afin de vous assurer que toutes vos ressources sont correctement configurés avec les paramètres de diagnostic, vous pouvez accéder à la carte de **surveillance** dans le portail et ouvrir la carte **Les journaux de Diagnostic** .

![Carte journaux des diagnostics dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Vous devrez peut-être cliquer sur « Plus services » pour trouver la carte de surveillance.

Dans cette carte, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les journaux de Diagnostic pour voir si elle a diagnostics activé et quel compte de stockage, concentrateur événement et/ou espace de travail journal Analytique ces journaux est dirige vers.

![Résultats de carte journaux des diagnostics dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Cliquez sur une ressource pour afficher tous les journaux qui ont été stockées dans le compte de stockage et donnent la possibilité de désactiver ou de modifier les paramètres des Diagnostics. Cliquez sur l’icône de téléchargement pour télécharger les journaux pour une période donnée.

![Une ressource du cartes de journaux des Diagnostics](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] Les journaux de diagnostic apparaît dans cet affichage uniquement et être téléchargés si vous avez configuré les paramètres de diagnostic pour les enregistrer dans un compte de stockage.

Cliquez sur le lien pour **Les paramètres de Diagnostic** pour ouvrir la carte de paramètres des diagnostics, où vous pouvez activer, désactiver ou modifier vos paramètres de diagnostics pour la ressource sélectionnée.

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Schéma pour les journaux de Diagnostic et services pris en charge
Le schéma pour les journaux de Diagnostic varie selon la catégorie de ressource et de journal. Voici les services pris en charge et leur schéma.

| Service                       | Schéma et documents                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    Équilibrage de charge logiciel     |    [Journal analytique pour équilibrage de charge Azure (Preview)](../load-balancer/load-balancer-monitor-log.md)             |
|    Groupes de sécurité réseau    |    [Journal analytique pour les groupes de sécurité réseau (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    Passerelles d’application       |    [Journalisation des diagnostics pour passerelle d’Application](../application-gateway/application-gateway-diagnostics.md)     |
|    Archivage sécurisé clé                  |    [Journalisation de l’archivage sécurisé clés Azure](../key-vault/key-vault-logging.md)                                                 |
|    Recherche Azure               |    [L’activation et à l’aide de la recherche le trafic Analytique](../search/search-traffic-analytics.md)                         |
|    Données Lake Store            |    [Accès aux journaux de diagnostics pour Azure données Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    Données Lake Analytique        |    [Accès aux journaux de diagnostics pour Azure données Lake Analytique](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    Applications de logique                 |    Aucun schéma n’est disponible.                                                                                         |
|    Lot Azure                |    [Journalisation des diagnostics lot Azure](../batch/batch-diagnostics.md)                                              |
|    Automatisation Azure           |    [Journal analytique pour Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    Concentrateur de l’événement                  |    Aucun schéma n’est disponible.                                                                                         |
|    Bus des services                |    Aucun schéma n’est disponible.                                                                                         |
|    Flux Analytique           |    Aucun schéma n’est disponible.                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>Prise en charge les catégories de journaux par type de ressource

|Type de ressource|Catégorie|Nom complet de catégorie|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux de travail|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux de service|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Demandes|Journaux de demandes|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Demandes|Journaux de demandes|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic pour l’exécution du flux de travail|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événements de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Groupe de sécurité réseau règle flux événement|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte équilibrage de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État d’intégrité de charge équilibrage sonde|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal des applications Access de passerelle|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de Performance application passerelle|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal des applications de pare-feu passerelle|
|Microsoft.Search/searchServices|OperationLogs|Journaux d’opération|
|Microsoft.ServerManagement/nodes|RequestLogs|Journaux de demandes|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.StreamAnalytics/streamingjobs|Erreur d’exécution|Erreur d’exécution|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|

## <a name="next-steps"></a>Étapes suivantes
- [Journaux de Diagnostic flux aux **Hubs d’événement**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Modifier les paramètres de Diagnostic à l’aide de l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [Analyser les journaux avec OMS journal Analytique](../log-analytics/log-analytics-azure-storage-json.md)
