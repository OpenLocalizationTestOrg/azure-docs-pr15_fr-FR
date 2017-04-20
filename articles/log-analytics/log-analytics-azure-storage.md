<properties
    pageTitle="Collecte des données de stockage Azure dans la vue d’ensemble du journal Analytique | Microsoft Azure"
    description="Ressources Azure peuvent écrire les journaux et les indicateurs à un compte de stockage Azure, souvent à l’aide des Diagnostics Azure. Journal Analytique peut indexer ces données et le rendre disponible pour la recherche."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Collecte des données de stockage Azure dans la vue d’ensemble du journal Analytique

De nombreuses ressources Azure sont en mesure d’écrire les journaux et les indicateurs à un compte de stockage Azure. Journal Analytique peut utiliser ces données et le rendre plus faciles à contrôler vos ressources Azure.

Pour écrire dans le stockage Azure une ressource peut utiliser les diagnostics de Windows Azure ou avoir son propre méthode pour écrire des données. Ces données peuvent être écrites dans différents formats à un des emplacements suivants :

+ Table Azure
+ Objets blob Azure
+ EventHub

Journal Analytique prend en charge les services Azure écrivent des données à l’aide des [journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). En outre, journal Analytique prend en charge les autres services qui les journaux et les mesures dans différents formats et emplacements de sortie.  

>[AZURE.NOTE] Vous êtes responsable de taux de données Azure normal pour le stockage et transactions lorsque vous envoyez des diagnostics à un compte de stockage et pour lorsque Analytique journal lit les données à partir de votre compte de stockage.

![Diagramme de stockage Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Prise en charge des ressources Azure

Journal Analytique recueille des données pour les ressources Azure suivantes :

| Type de ressource | Journaux (Diagnostic catégories) | Solution d’Analytique de journal |
| --------------------------------------- | -------------------------------- | --------------- |
| Analyse de l’application | Disponibilité <br> Événements personnalisés <br> Exceptions <br> Demandes <br> | Application perspectives (Preview) |
| Gestion des API | | *Aucun* (Preview) |
| Automatisation <br> Microsoft.Automation/AutomationAccounts | JobLogs <br> JobStreams          | AzureAutomation (Preview) |
| Archivage sécurisé clé <br> Microsoft.KeyVault/Vaults               | AuditEvent                       | KeyVault (Preview) |
| Passerelle d’application <br> Microsoft.Network/ApplicationGateways   | ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog | AzureNetworking (Preview) |
| Groupe de sécurité réseau <br> Microsoft.Network/NetworkSecurityGroups | NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter | AzureNetworking (Preview) |
| TISSU de service                          | ETWEvent <br> Événement opérationnel <br> Événement acteur fiable <br> Événement Service fiable| ServiceFabric (Preview) |
| Machines virtuelles | Journal système Linux <br> Événements Windows <br> Journal IIS <br> Windows ETWEvent | *Aucun* |
| Rôles Web <br> Rôles de travail | Journal système Linux <br> Événements Windows <br> Journal IIS <br> Windows ETWEvent | *Aucun* |

>[AZURE.NOTE] Pour une analyse Azure machines virtuelles (Linux et Windows), nous vous recommandons d’installation de l' [extension de journal Analytique machine virtuelle](log-analytics-azure-vm-extension.md). L’agent fournit des informations approfondies sur vos ordinateurs virtuels que si vous utilisez les tests de diagnostic écrite dans le stockage.

Vous pouvez nous aider à hiérarchiser les journaux supplémentaires pour OMS analyser par vote dans notre [page de commentaires](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).


- Consultez [Azure analyser les journaux de diagnostic à l’aide de journal Analytique](log-analytics-azure-storage-json.md) pour en savoir plus sur comment journal Analytique peut lire les journaux à partir d’Azure services qui prennent en charge [les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md):
  - Archivage sécurisé clé Azure
  - Automatisation Azure
  - Passerelle d’application
  - Groupes de sécurité réseau
- Consultez [stockage d’objets blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour en savoir plus sur comment journal Analytique peut lire les journaux pour Azure services ce diagnostics écriture au stockage de tables ou les journaux IIS écrites dans blob storage, notamment :
  - TISSU de service
  - Rôles Web
  - Rôles de travail
  - Machines virtuelles


Perspectives applications se trouve dans Aperçu privé et il utilise les exporter continue à stockage blob. Pour participer à la version bêta privée, contactez l’équipe Microsoft Account ou s’y référer les détails sur le [site de commentaires](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Étapes suivantes

- [Azure analyser les journaux de diagnostic à l’aide de journal Analytique](log-analytics-azure-storage-json.md) pour lire les journaux à partir d’Azure services ce diagnostics écriture au stockage blob au format JSON.
- [Stockage d’objets blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour lire les journaux pour Azure services ce diagnostics écriture au stockage de tables ou les journaux IIS écrites dans stockage blob.
- [Activer des Solutions](log-analytics-add-solutions.md) afin de fournir des données.
- [Utiliser des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
