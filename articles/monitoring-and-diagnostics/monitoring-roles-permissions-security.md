<properties
    pageTitle="Prise en main rôles, autorisations et la sécurité avec le moniteur Azure | Microsoft Azure"
    description="Découvrez comment utiliser les rôles intégrés et les autorisations du moniteur Azure limiter l’accès à l’analyse des ressources."
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
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Prise en main rôles, autorisations et la sécurité avec le moniteur d’Azure

De nombreuses équipes doivent régler strictement accès aux données et des paramètres de surveillance. Par exemple, si vous avez des membres d’une équipe travaillant en mode exclusif sur le contrôle (ingénieurs du support technique, les ingénieurs devops) ou si vous utilisez un fournisseur de service géré, vous souhaiterez peut-être leur accorder l’accès aux données analyse uniquement en limitant leurs possibilités pour créer, modifier ou supprimer des ressources. Cet article vous explique comment appliquer un rôle RBAC surveillance intégré à un utilisateur dans Azure ou créer votre propre rôle personnalisé pour un utilisateur ayant besoin des autorisations limitées surveillance rapidement. Il traite puis considérations relatives à la sécurité de vos ressources de moniteur Azure et comment vous pouvez limiter l’accès aux données qu’ils contiennent.

## <a name="built-in-monitoring-roles"></a>Rôles de surveillance intégrés

Rôles intégrés du moniteur Azure sont conçus pour aider à limiter l’accès aux ressources dans un abonnement tout en permettant au responsable de l’infrastructure pour obtenir et configurer les données dont elles ont besoin d’analyse. Moniteur Azure propose deux rôles de prédéfinies : A surveillance lecteur et un collaborateur de surveillance.

### <a name="monitoring-reader"></a>Lecteur d’analyse

Personnes voient attribuées le rôle de surveillance Reader pour afficher toutes les données d’analyse dans un abonnement, mais ne peut pas modifier toutes les ressources ou modifier les paramètres relatifs à la surveillance des ressources. Ce rôle est approprié pour les utilisateurs dans une société, par exemple ingénieurs prise en charge ou opérations, qui doivent être en mesure de :

- Afficher des tableaux de bord de surveillance dans le portail et créer leurs propres tableaux de bord de surveillance privé.
- Requête pour les indicateurs à l’aide de [l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931930.aspx), [applets de commande PowerShell](insights-powershell-samples.md)ou [infrastructure du langage commun disponibilité sur plusieurs plateformes](insights-cli-samples.md).
- Interroger le journal d’activité à l’aide du portail, l’API REST Azure moniteur, applets de commande PowerShell ou infrastructure du langage commun disponibilité sur plusieurs plateformes.
- Afficher les [paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) pour une ressource.
- Afficher le [profil de journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) pour un abonnement.
- Afficher les paramètres d’échelle.
- Afficher les activités alerte et les paramètres.
- Accéder aux données d’analyse des applications et afficher les données AI Analytique.
- Rechercher des données d’espace de travail de journal Analytique (OMS), y compris les données d’utilisation de l’espace de travail.
- Afficher les groupes d’administration journal Analytique (OMS).
- Extraire le schéma de recherche journal Analytique (OMS).
- Liste des packs d’intelligence journal Analytique (OMS).
- Récupérer et l’exécuter journal Analytique (OMS) recherches enregistrées.
- Récupérer la configuration du stockage journal Analytique (OMS).

> [AZURE.NOTE] Ce rôle ne donne pas accès en lecture aux données de journal qui a été transmises à un concentrateur événement ou stockées dans un compte de stockage. Pour plus d’informations sur la configuration d’accès à ces ressources, [consultez la section ci-dessous](#security-considerations-for-monitoring-data) .

### <a name="monitoring-contributor"></a>Surveillance des collaborateurs

Les utilisateurs voient attribuées le rôle de collaborateur surveillance pouvant afficher toutes les données d’analyse dans un abonnement et créer ou modifier des paramètres d’analyse, mais pas d’autres ressources. Ce rôle est un sur-ensemble du rôle Lecteur surveillance et est approprié pour les membres d’équipe de surveillance ou fournisseurs de services gérés qui, outre les autorisations ci-dessus, doivent également être en mesure d’une organisation :

- Publier des tableaux de bord surveillance en tant qu’un tableau de bord partagé.
- Définir [les paramètres de diagnostic](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) pour un resource.*
- Définissez le [profil de journal](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles) pour un subscription.*
- Définir l’alerte activité et les paramètres.
- Créer des tests de site web d’analyse des applications et des composants.
- Espace de travail de liste journal Analytique (OMS) des clés partagées.
- Activer ou désactiver les modules d’intelligence journal Analytique (OMS).
- Créer et supprimer et exécuter journal Analytique (OMS) recherches enregistrées.
- Créer et supprimer la configuration du stockage journal Analytique (OMS).

* utilisateur doit également séparément être autorisé ListKeys sur la ressource cible (stockage compte ou un événement concentrateur espace de noms) pour définir un profil de journal ou le paramètre de diagnostic.

> [AZURE.NOTE] Ce rôle ne donne pas accès en lecture aux données de journal qui a été transmises à un concentrateur événement ou stockées dans un compte de stockage. Pour plus d’informations sur la configuration d’accès à ces ressources, [consultez la section ci-dessous](#security-considerations-for-monitoring-data) .

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Autorisations et les rôles RBAC personnalisés de surveillance
Si les rôles intégrés ci-dessus ne répond aux besoins exactes de votre équipe, vous pouvez [créer un rôle RBAC personnalisé](../active-directory/role-based-access-control-custom-roles.md) avec des autorisations plus granulaires. Voici les opérations Azure moniteur RBAC courantes avec leurs descriptions.

| Opération                                                   | Description                                                                                                                                               |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Microsoft.Insights/AlertRules/[Read, écriture, suppression]         | Règles d’alerte en lecture/écriture/supprimer.                                                                                                                            |
| Microsoft.Insights/AlertRules/Incidents/Read                | Liste des incidents (l’historique de la règle d’alerte déclenchée) pour les règles d’alerte. Cela s’applique uniquement au portail.                                              |
| Microsoft.Insights/AutoscaleSettings/[Read, écriture, suppression]  | Paramètres d’échelle en lecture/écriture/supprimer.                                                                                                                     |
| Microsoft.Insights/DiagnosticSettings/[Read, écriture, suppression] | Paramètres des diagnostics en lecture/écriture/supprimer.                                                                                                                    |
| Microsoft.Insights/eventtypes/digestevents/Read             | Cette autorisation est nécessaire pour les utilisateurs qui doivent pouvoir accéder aux journaux d’activité via le portail.                                                                   |
| Microsoft.Insights/eventtypes/values/Read                   | La liste événements de journal d’activité (gestion des événements) dans un abonnement. Cette autorisation s’applique au portail et par programmation access pour le journal d’activité. |
| Microsoft.Insights/LogDefinitions/Read                      | Cette autorisation est nécessaire pour les utilisateurs qui doivent pouvoir accéder aux journaux d’activité via le portail.                                                                   |
| Microsoft.Insights/MetricDefinitions/Read                   | Lire les définitions métriques (liste des types de métriques disponibles pour une ressource).                                                                                  |
| Microsoft.Insights/Metrics/Read                             | Lire les mesures pour une ressource.                                                                                                                              |

> [AZURE.NOTE] Accès aux alertes, les paramètres de diagnostic et de mesures pour une ressource requiert que l’utilisateur a accès en lecture sur le type de ressource et la portée de cette ressource. Création (« écriture ») un profil paramètre ou journal de diagnostic qui archive à un compte de stockage ou flux aux hubs événement, l’utilisateur doit également avoir l’autorisation ListKeys sur la ressource cible.

Par exemple, en utilisant le tableau ci-dessus, vous pouviez créer un rôle RBAC personnalisé pour un « lecteur de journal activité » comme suit :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Considérations relatives à la sécurité d’analyser les données
Analyse des données, notamment les fichiers journaux — peuvent contenir des informations sensibles, telles que les noms d’utilisateur ou les adresses IP. Analyse des données à partir d’Azure est fourni sous trois formes de base :
1. Le journal d’activité, qui décrit toutes les actions de plan de contrôle de votre abonnement Azure.
2. Journaux de diagnostic, qui sont des journaux émis par une ressource.
3. Mesures, qui sont émis par des ressources.

Trois de ces types de données peuvent être stockées dans un compte de stockage ou diffusés sur événement Hub, qui sont tous deux ressources Azure à usage général. Car il s’agit des ressources à usage général, création, suppression et la façon d’y accéder sont une opération dotés de privilèges généralement réservée pour les administrateurs. Nous vous conseille d’utiliser les pratiques suivantes pour les ressources relatif à l’analyse demandez-vous :

- Utilisez un compte de stockage unique, dédié d’analyser les données. Si vous avez besoin séparer les données d’analyse dans plusieurs comptes de stockage, jamais partager l’utilisation d’un compte de stockage entre la surveillance et des données non surveillance, que ce peuvent donner par inadvertance ceux qui uniquement besoin d’y accéder à l’analyse de données (par exemple. un SIEM tiers) accès non analyse des données.
- Utilisez un seul dédié Bus de Service ou d’un événement concentrateur espace de noms sur tous les paramètres des diagnostics pour la même raison que ci-dessus.
- Limiter l’accès aux comptes de stockage relatif à l’analyse ou hubs événement en les maintenant dans un groupe de ressources distinct et [Utilisez étendue](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) sur vos rôles surveillance pour limiter l’accès à uniquement ce groupe de ressources.
- N’est jamais accordée l’autorisation ListKeys pour les comptes de stockage ou hubs événement à la portée d’abonnement lorsqu’un utilisateur doit uniquement accès aux données d’analyse. Donnez à la place, les autorisations suivantes à l’utilisateur à une ressource ou un groupe de ressources (si vous avez un groupe de ressources de surveillance dédié) étendue.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Limiter l’accès aux comptes de stockage relatif à l’analyse
Lorsqu’un utilisateur ou une application doit accéder à l’analyse des données dans un compte de stockage, vous devez [Générer un sa compte](https://msdn.microsoft.com/library/azure/mt584140.aspx) sur le compte de stockage qui contient les données d’analyse avec accès en lecture seule au niveau de service de stockage d’objets blob. Dans PowerShell, cela peut ressembler à :

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Vous pouvez ensuite donner le jeton à l’entité que doivent lire à partir de cet espace de stockage compte et il peut répertorier et lire à partir de tous les objets BLOB dans ce compte de stockage.

Par ailleurs, si vous avez besoin contrôler cette autorisation avec RBAC, vous pouvez accorder cette entité l’autorisation Microsoft.Storage/storageAccounts/listkeys/action sur ce compte de stockage particulier. Cela est nécessaire pour les utilisateurs qui doivent être en mesure de définir un paramètre de diagnostic ou se profil à archiver à un compte de stockage. Par exemple, vous pouvez créer le rôle RBAC personnalisé suivant pour un utilisateur ou une application qui a uniquement besoin de lire à partir d’un compte de stockage :

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [AZURE.WARNING] L’autorisation ListKeys permet à l’utilisateur répertorier les clés de compte de stockage principal et secondaire. Ces touches accorder à l’utilisateur tous les signés autorisations (lecture, écriture, créer des objets BLOB, supprimer des objets BLOB, etc.) dans l’ensemble connecté services (blob, file d’attente, table, fichier) dans ce compte de stockage. Nous vous recommandons d’utiliser un sa compte ci-dessus lorsque cela est possible.

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Limiter l’accès aux hubs relatif à l’analyse des événements
Un modèle similaire peut être suivi avec hubs de l’événement, mais vous devez tout d’abord créer une règle d’autorisation écouter dédiée. Si vous voulez accorder l’accès à une application qui n’a besoin pour écouter hubs relatif à l’analyse des événements, procédez comme suit :

1. Créer une stratégie d’accès partagé sur l’événement concentrateur (s) qui ont été créés pour le flux de données d’analyse avec uniquement les revendications écouter. Pour cela, dans le portail. Par exemple, vous pouvez appeler il « monitoringReadOnly. » Si possible, vous souhaiterez donner cette touche directement au consommateur et ignorez l’étape suivante.
2. Si le consommateur doit être en mesure d’obtenir la clé ad hoc, accordez à l’utilisateur l’action ListKeys de ce concentrateur de l’événement. Ceci est également nécessaire pour les utilisateurs qui doivent être en mesure de définir un paramètre de diagnostic ou se profil au flux aux hubs événement. Par exemple, vous pouvez créer une règle RBAC :

   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```


## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les autorisations dans le Gestionnaire de ressources et RBAC](../active-directory/role-based-access-control-what-is.md)
- [Lire la présentation de surveillance dans Azure](monitoring-overview.md)
