<properties
    pageTitle="RBAC : Rôles intégrés | Microsoft Azure"
    description="Cette rubrique décrit intégré dans les rôles pour contrôler l’accès basé sur un rôle (RBAC)."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/25/2016"
    ms.author="kgremban"/>

#<a name="rbac-built-in-roles"></a>RBAC : Rôles intégrés

Azure contrôle d’accès basé sur un rôle (RBAC) est fourni avec les rôles prédéfinis suivants qui peuvent être affectées à des utilisateurs, groupes et services. Vous ne pouvez pas modifier les définitions de rôles intégrés. Toutefois, vous pouvez créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md) pour répondre aux besoins spécifiques de votre organisation.

## <a name="roles-in-azure"></a>Rôles dans Azure

Le tableau suivant fournit de brèves descriptions des rôles intégrés. Cliquez sur le nom de rôle pour afficher la liste détaillée des **actions** et **notactions** pour le rôle. La propriété **actions** spécifie les actions autorisées sur les ressources Azure. Chaînes action peuvent utiliser des caractères génériques. La propriété **notactions** spécifie les actions qui sont exclues les actions autorisées.

>[AZURE.NOTE] Les définitions de rôle Azure évoluent constamment. Cet article est conservé comme à jour que possible, mais vous pouvez toujours trouver les dernières définitions de rôles dans Azure PowerShell. Utiliser les applets de commande `(get-azurermroledefinition "<role name>").actions` ou `(get-azurermroledefinition "<role name>").notactions` selon vos besoins.

| Nom de rôle | Description |
| --------- | ----------- |
| [Collaboration de Service de gestion de l’API](#api-management-service-contributor) | Gérer les services de gestion de l’API |
| [Application Insights composant collaborateur](#application-insights-component-contributor) | Gérer les composants d’Application perspectives |
| [Opérateur d’Automation](#automation-operator) | En mesure de démarrer, arrêter, interrompre et reprendre les tâches |
| [Collaboration BizTalk](#biztalk-contributor) | Gérer les services BizTalk |
| [Collaboration ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Gérer les bases de données ClearDB MySQL |
| [Collaboration](#contributor) | Permet de gérer tous les éléments sauf access. |
| [Données usine collaborateur](#data-factory-contributor) | Peut créer et gérer des références de données et ressources enfants qu’ils contiennent. |
| [DevTest ateliers utilisateur](#devtest-labs-user) | Peuvent afficher tous les éléments et vous connecter, début, de redémarrage et d’arrêt machines virtuelles |
| [Collaboration de Zone DNS](#dns-zone-contributor) | Gérer les enregistrements et les zones DNS |
| [Collaboration DocumentDB compte](#documentdb-account-contributor) | Gérer les comptes DocumentDB |
| [Systèmes intelligents compte collaborateur](#intelligent-systems-account-contributor) | Gérer les comptes systèmes intelligents |
| [Collaboration de réseau](#network-contributor) | Peuvent gérer toutes les ressources réseau |
| [Nouveau Relic APM compte collaborateur](#new-relic-apm-account-contributor) | Peut gérer les comptes de gestion des performances nouvelle Relic Application et les applications |
| [Propriétaire](#owner) | Peuvent gérer tous les éléments, y compris l’accès |
| [Reader](#reader) | Peut afficher tout le contenu, mais ne peuvent pas apporter des modifications |
| [Redis Cache collaborateur](#redis-cache-contributor) | Gérer les Redis cache |
| [Planificateur tâche Collections collaborateur](#scheduler-job-collections-contributor) | Gérer les collections de travail planificateur |
| [Collaboration de Service de recherche](#search-service-contributor) | Gérer les services de recherche |
| [Gestionnaire de sécurité](#security-manager) | Gérer les composants de sécurité, stratégies de sécurité et machines virtuelles |
| [Collaboration de base de données SQL](#sql-db-contributor) | Gérer les bases de données SQL, mais pas les stratégies de sécurité |
| [Gestionnaire de sécurité SQL](#sql-security-manager) | Peut gérer les stratégies de sécurité de serveurs SQL Server et les bases de données |
| [SQL Server collaborateur](#sql-server-contributor) | Gérer les serveurs SQL et bases de données, mais pas les stratégies de sécurité |
| [Collaboration de compte de stockage classique](#classic-storage-account-contributor) | Gérer les comptes de stockage classique |
| [Collaboration de compte de stockage](#storage-account-contributor) | Gérer les comptes de stockage |
| [Administrateur de l’accès utilisateur](#user-access-administrator) | Gérer les accès utilisateur aux ressources Azure |
| [Machine virtuelle classique collaborateur](#classic-virtual-machine-contributor) | Gérer les machines virtuelles classiques, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés |
| [Machine virtuelle collaborateur](#virtual-machine-contributor) | Gérer les machines virtuelles, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés |
| [Collaboration réseau classique](#classic-network-contributor) | Gérer les réseaux virtuels classiques et adresses IP réservé |
| [Web Plan de collaboration](#web-plan-contributor) | Gérer les offres web |
| [Collaboration de site Web](#website-contributor) | Gérer les sites Web, mais pas les offres web auquel ils sont connectés |

## <a name="role-permissions"></a>Autorisations de rôle
Les tableaux suivants décrivent les autorisations spécifiques accordées à chaque rôle. Cela peut inclure les **Actions**qui accorder des autorisations et **NotActions**, les restreindre.

### <a name="api-management-service-contributor"></a>Collaboration de Service de gestion de l’API
Gérer les services de gestion de l’API

| **Actions** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Créer et gérer des Services de gestion de l’API |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Rôles de lecture et les attributions de rôle |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="application-insights-component-contributor"></a>Application Insights composant collaborateur
Gérer les composants d’Application perspectives

| **Actions** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et les attributions de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.Insights/webtests/* | Créer et gérer des tests de site web |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="automation-operator"></a>Opérateur d’Automation
En mesure de démarrer, arrêter, interrompre et reprendre les tâches

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et les attributions de rôle |
| Microsoft.Automation/automationAccounts/jobs/read | Lire des travaux compte automation |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reprendre une tâche de compte automation |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Arrêter une tâche compte automation |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Lire des flux de travail de compte automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspendre un travail compte automation |
| Microsoft.Automation/automationAccounts/jobs/write | Écrire des travaux compte automation |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Lire un planning de compte automation |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Lire un planning de compte automation |
| Microsoft.Automation/automationAccounts/read | Lire les comptes d’automation |
| Microsoft.Automation/automationAccounts/runbooks/read | Lire les procédures opérationnelles automation |
| Microsoft.Automation/automationAccounts/schedules/read | Lire les tableaux d’analyse automation |
| Microsoft.Automation/automationAccounts/schedules/write | Écrire automation tableaux d’analyse |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="biztalk-contributor"></a>Collaboration BizTalk
Gérer les services BizTalk

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et les attributions de rôle |
| Microsoft.BizTalkServices/BizTalk/* | Créer et gérer des services BizTalk |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="cleardb-mysql-db-contributor"></a>Collaboration ClearDB MySQL DB
Gérer les bases de données ClearDB MySQL

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et les attributions de rôle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |
| successbricks.cleardb/Databases/* | Créer et gérer des bases de données ClearDB MySQL |

### <a name="contributor"></a>Collaboration
Peuvent gérer tous les éléments sauf access

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer des ressources de tous les types |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Delete | Impossible de supprimer les rôles et les attributions de rôle |  
| Microsoft.Authorization/*/Write | Impossible de créer les rôles et les attributions de rôle |

### <a name="data-factory-contributor"></a>Données usine collaborateur
Créer et gérer des références de données et ressources enfants qu’ils contiennent.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.DataFactory/dataFactories/* | Créer et gérer des références de données et ressources enfants qu’ils contiennent. |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="devtest-labs-user"></a>DevTest ateliers utilisateur
Peuvent afficher tous les éléments et vous connecter, début, de redémarrage et d’arrêt machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Compute/availabilitySets/read | Lire les propriétés des jeux de disponibilité |
| Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles machine virtuelle, état d’exécution, extensions machine virtuelle, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Libérer des machines virtuelles |
| Microsoft.Compute/virtualMachines/read | Lire les propriétés d’une machine virtuelle |
| Microsoft.Compute/virtualMachines/restart/action | Redémarrez machines virtuelles |
| Microsoft.Compute/virtualMachines/start/action | Machines virtuelles début |
| Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
| Microsoft.DevTestLab/labs/createEnvironment/action | Créer un environnement de test |
| Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules |
| Microsoft.DevTestLab/labs/formulas/read | Lire les formules |
| Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer les stratégies d’atelier |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Rejoindre un groupe d’adresses charge équilibrage principal |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Participer à un équilibrage de charge règle NAT entrante |
| Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface de réseau (par exemple, tous les les charge programmes d’équilibrage faisant partie de l’interface réseau) |
| Microsoft.Network/networkInterfaces/join/action | Participer à une Machine virtuelle à une interface réseau |
| Microsoft.Network/networkInterfaces/read | Lire les interfaces réseau |
| Microsoft.Network/networkInterfaces/write | Écrire des interfaces réseau |
| Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
| Microsoft.Network/publicIPAddresses/join/action | Participer à une adresse IP publique |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/subnets/join/action | Participer à un réseau virtuel |
| Microsoft.Resources/deployments/operations/read | Opérations de déploiement de lecture |
| Microsoft.Resources/deployments/read | Lire les déploiements |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Storage/storageAccounts/listKeys/action | Clés de compte de stockage de liste |

### <a name="dns-zone-contributor"></a>Collaboration de Zone DNS
Gérer les enregistrements et zones DNS.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/\*/lu | Rôles de lecture et les attributions de rôle |
| Microsoft.Insights/alertRules/\* | Créer et gérer des règles d’alerte |
| Microsoft.Network/dnsZones/\* | Créer et gérer des enregistrements et des zones DNS |
| Microsoft.ResourceHealth/availabilityStatuses/read | Lire l’état des ressources |
| Microsoft.Resources/deployments/\* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/\* | Créer et gérer des tickets de Support |


### <a name="documentdb-account-contributor"></a>Collaboration DocumentDB compte
Gérer les comptes DocumentDB

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer les comptes DocumentDB |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="intelligent-systems-account-contributor"></a>Systèmes intelligents compte collaborateur
Gérer les comptes systèmes intelligents

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes systèmes intelligents |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="network-contributor"></a>Collaboration de réseau
Peuvent gérer toutes les ressources réseau

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.Network/* | Créer et gérer des réseaux |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="new-relic-apm-account-contributor"></a>Nouveau Relic APM compte collaborateur
Peut gérer les comptes de gestion des performances nouvelle Relic Application et les applications

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |
| NewRelic.APM/accounts/* | Créer et gérer les comptes de gestion des performances Relic nouvelle application |

### <a name="owner"></a>Propriétaire
Peuvent gérer tous les éléments, y compris l’accès

| **Actions** ||
| ------- | ------ |
| * | Créer et gérer des ressources de tous les types |

### <a name="reader"></a>Reader
Peut afficher tout le contenu, mais ne peuvent pas apporter des modifications

| **Actions** ||
| ------- | ------ |
| * / lu | Lire les ressources de tous les types, sauf secrets. |

### <a name="redis-cache-contributor"></a>Redis Cache collaborateur
Gérer les Redis cache

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Cache/redis/* | Créer et gérer des Redis cache |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="scheduler-job-collections-contributor"></a>Planificateur tâche Collections collaborateur
Gérer les collections de travail planificateur

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travail |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="search-service-contributor"></a>Collaboration de Service de recherche
Gérer les services de recherche

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Search/searchServices/* | Créer et gérer des services de recherche |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="security-manager"></a>Gestionnaire de sécurité
Gérer les composants de sécurité, stratégies de sécurité et machines virtuelles

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.ClassicCompute/*/read | Lire les informations de configuration classique calculer machines virtuelles |
| Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles |
| Microsoft.ClassicNetwork/*/read | Lire les informations de configuration de réseau classique  |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Security/* | Créer et gérer des composants de sécurité et des stratégies |
| Microsoft.Support/* | Créer et gérer des tickets de support  |

### <a name="sql-db-contributor"></a>Collaboration de base de données SQL
Gérer les bases de données SQL, mais pas les stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Rôles de lecture et le rôle affectations |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/databases/* | Créer et gérer des bases de données SQL |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Support/* | Créer et gérer des tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Impossible de modifier les stratégies d’audit |
| Microsoft.Sql/servers/databases/auditingSettings/* | Impossible de modifier les paramètres d’audit |
| Microsoft.Sql/servers/databases/auditRecords/read  | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Impossible de modifier les données des stratégies de masquage |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier les stratégies alerte de sécurité |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité |

### <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL
Peut gérer les stratégies de sécurité de serveurs SQL Server et les bases de données

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer des paramètres d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer des stratégies de connexion de base de données SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer des données de base de données SQL server des stratégies de masquage |
| Microsoft.Sql/servers/databases/read | Bases de données SQL de lecture |
| Microsoft.Sql/servers/databases/schemas/read | Schémas de base de données en lecture SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Colonnes de tableau de base de données en lecture SQL server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Tables de base de données en lecture SQL server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer des stratégies alertes de sécurité des base de données SQL server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer des mesures de sécurité de base de données SQL server |
| Microsoft.Sql/servers/read | Lire les serveurs SQL |
| Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer des stratégies de l’alertes de sécurité SQL server |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="sql-server-contributor"></a>SQL Server collaborateur
Gérer les serveurs SQL Server et les bases de données, mais pas les stratégies de sécurité

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture|
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Sql/servers/* | Créer et gérer des serveurs SQL Server |
| Microsoft.Support/* | Créer et gérer des tickets de support |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Ne peut pas modifier les stratégies d’audit SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Ne peut pas modifier les paramètres d’audit SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Ne peut pas modifier les stratégies d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Ne peut pas modifier les paramètres d’audit de la base de données SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read  | Impossible de lire les enregistrements d’audit |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Impossible de modifier les stratégies de connexion de base de données SQL server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Ne peut pas modifier les données de base de données SQL server des stratégies de masquage |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Impossible de modifier stratégies alertes de sécurité des base de données SQL server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Impossible de modifier les mesures de sécurité de base de données SQL server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Ne peut pas modifier les stratégies de l’alertes de sécurité SQL server |

### <a name="classic-storage-account-contributor"></a>Collaboration de compte de stockage classique
Gérer les comptes de stockage classique

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer des comptes de stockage |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="storage-account-contributor"></a>Collaboration de compte de stockage
Peut gérer les comptes de stockage, mais ne peuvent pas accéder à ces.

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Lire tous les d’autorisation |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres des Diagnostics |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Storage/storageAccounts/* | Créer et gérer des comptes de stockage |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="user-access-administrator"></a>Administrateur de l’accès utilisateur
Gérer les accès utilisateur aux ressources Azure

| **Actions** ||
| ------- | ------ |
| * / lu | Lire les ressources de tous les Types, sauf secrets. |
| Microsoft.Authorization/* | Gérer les autorisations |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="classic-virtual-machine-contributor"></a>Machine virtuelle classique collaborateur
Gérer les machines virtuelles classiques, mais pas le réseau ou stockage compte virtuel auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine cluster classique |
| Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer des machines virtuelles |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Participer à des groupes de sécurité réseau |
| Microsoft.ClassicNetwork/reservedIps/link/action | Lier des adresses IP réservé |
| Microsoft.ClassicNetwork/reservedIps/read | Adresses IP pouvant être lu |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Rejoindre des réseaux virtuels |
| Microsoft.ClassicNetwork/virtualNetworks/read | Lire des réseaux virtuels |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Lire des disques de stockage en compte |
| Microsoft.ClassicStorage/storageAccounts/images/read | Lire des images de compte de stockage |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Clés de compte de stockage de liste |
| Microsoft.ClassicStorage/storageAccounts/read | Lire les comptes de stockage classique |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="virtual-machine-contributor"></a>Machine virtuelle collaborateur
Gérer les machines virtuelles mais pas le réseau ou stockage compte virtuel auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Compute/availabilitySets/* | Créer et gérer des ensembles de disponibilité cluster |
| Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
| Microsoft.Compute/virtualMachines/* | Créer et gérer des machines virtuelles |
| Microsoft.Compute/virtualMachineScaleSets/* | Créer et gérer des ensembles d’échelle machine virtuelle |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Participer à des pools d’adresses réseau application passerelle serveur principal |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Participer à des pools d’adresses charge équilibrage principal |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Participer à équilibrage de charge entrant pools NAT |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Participer à équilibrage de charge NAT règles de trafic entrant |
| Microsoft.Network/loadBalancers/read | Programmes d’équilibrage de charge en lecture |
| Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
| Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
| Microsoft.Network/networkSecurityGroups/join/action | Participer à des groupes de sécurité réseau |
| Microsoft.Network/networkSecurityGroups/read | Lire les groupes de sécurité réseau |
| Microsoft.Network/publicIPAddresses/join/action | Participer à des adresses IP publiques réseau |
| Microsoft.Network/publicIPAddresses/read | Lire des adresses IP publiques réseau |
| Microsoft.Network/virtualNetworks/read | Lire des réseaux virtuels |
| Microsoft.Network/virtualNetworks/subnets/join/action | Participer à des sous-réseaux virtuel |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Storage/storageAccounts/listKeys/action | Clés de compte de stockage de liste |
| Microsoft.Storage/storageAccounts/read | Lire les comptes d’espace de stockage |
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="classic-network-contributor"></a>Collaboration réseau classique
Gérer les réseaux virtuels classiques et adresses IP réservé

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |

### <a name="web-plan-contributor"></a>Web Plan de collaboration
Gérer les offres web

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |
| Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |

### <a name="website-contributor"></a>Collaboration de site Web
Gérer les sites Web, mais pas les offres web auquel ils sont connectés

| **Actions** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Autorisation de lecture |
| Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte perspectives |
| Microsoft.Insights/components/* | Créer et gérer des composants de perspectives |
| Microsoft.ResourceHealth/availabilityStatuses/read | État de lecture des ressources |
| Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupe de ressources |
| Microsoft.Resources/subscriptions/resourceGroups/read | Lire les groupes de ressources |  
| Microsoft.Support/* | Créer et gérer des tickets de support |
| Microsoft.Web/certificates/* | Créer et gérer des certificats de site Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Lire des sites affectés à un nom d’hôte |
| Microsoft.Web/serverFarms/join/action | Participer à des batteries de serveurs |
| Microsoft.Web/serverFarms/read | Lire des batteries de serveurs |
| Microsoft.Web/sites/* | Créer et gérer des sites Web |

## <a name="see-also"></a>Voir aussi
- [Contrôle d’accès basé sur un rôle](role-based-access-control-configure.md): prise en main RBAC dans le portail Azure.
- [Rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md): Apprenez à créer des rôles personnalisés pour répondre à vos besoins d’accès.
- [Créer un accès à un rapport historique des modifications](role-based-access-control-access-change-history-report.md): effectuer le suivi de modification attributions de rôle dans RBAC.
- [Résolution des problèmes de contrôle d’accès basé sur un rôle](role-based-access-control-troubleshooting.md): obtenir des suggestions pour la résolution des problèmes courants.
