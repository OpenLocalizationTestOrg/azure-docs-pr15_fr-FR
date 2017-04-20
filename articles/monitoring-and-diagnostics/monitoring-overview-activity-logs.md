<properties
    pageTitle="Vue d’ensemble du journal d’activité Azure | Microsoft Azure"
    description="Découvrez les nouveautés du journal d’activité Azure et comment vous pouvez l’utiliser pour comprendre les événements qui se produisent au sein de votre abonnement Azure."
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
    ms.date="10/25/2016"
    ms.author="johnkem"/>

# <a name="overview-of-the-azure-activity-log"></a>Vue d’ensemble du journal d’activité Azure
Le **Journal d’activité Azure** est un journal qui offre une vision les opérations effectuées sur les ressources dans votre abonnement. Le journal d’activité anciennement appelé « Journaux d’Audit » ou « Journaux opérationnelles », dans la mesure où il signale les événements de plan de commande pour vos abonnements. En utilisant le journal d’activité, vous pouvez déterminer le « quoi, qui et quand » pour toutes les opérations (place, POST, DELETE) effectuées sur les ressources dans votre abonnement d’écriture. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas les opérations de lecture (GET).

Le journal d’activité diffère de [Journaux de Diagnostic](./monitoring-overview-of-diagnostic-logs.md), qui sont tous les journaux émis par une ressource. Ces journaux fournissent des données sur le fonctionnement de cette ressource, plutôt que les opérations sur cette ressource.

Vous pouvez récupérer les événements dans le journal d’activité à l’aide du portail Azure, infrastructure du langage commun, applets de commande PowerShell et l’API REST Azure moniteur.

Afficher cette [vidéo de présentation du journal d’activité](https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz).  

## <a name="what-you-can-do-with-the-activity-log"></a>Ce que vous pouvez faire avec le journal d’activité
Voici quelques éléments que vous pouvez faire avec le journal d’activité :

- La requête et afficher dans le **portail Azure**.
- La requête via des API REST, PowerShell Cmdlet ou infrastructure du langage commun.
- [Créer une alerte de message électronique ou webhook déclenchant désactiver un événement de journal d’activité.](./insights-auditlog-to-webhook-email.md)
- [L’enregistrer sur un **Compte de stockage** pour inspection archivage ou manuelle](./monitoring-archive-activity-log.md). Vous pouvez spécifier le temps de rétention (en jours) en utilisant les **Profils de journal**.
- Analyser dans PowerBI en utilisant le [**pack de contenu PowerBI**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/).
- [Diffuser à un **Événement concentrateur** ](./monitoring-stream-activity-logs-event-hubs.md) pour la réception par un service tiers ou une solution personnalisée analytique tels que PowerBI.

## <a name="export-the-activity-log-with-log-profiles"></a>Exporter le journal d’activité avec les profils de journal
Un **Profil de journal** contrôle comment votre journal d’activité est exporté. En utilisant un profil de journal, vous pouvez configurer :

- Où le journal d’activité doit être envoyé (compte de stockage ou événement Hubs)
- Les catégories d’événements (écriture, suppression, Action) doivent être envoyées
- Les régions (emplacements) doivent être exportées.
- La durée de conservation du journal d’activité dans un compte de stockage – une rétention de zéro jours signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, la valeur peut être n’importe quel nombre de jours compris entre 1 et 2147483647. Si vous définissez des stratégies de rétention, mais le stockage des journaux dans un compte de stockage est désactivé (par exemple, si une seule Hubs événement ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres peuvent être configurés via l’option « Exportation » dans la carte du journal d’activité dans le portail. Ils peuvent également être configurée par programme [à l’aide de l’API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx), applets de commande PowerShell ou infrastructure du langage commun. Un abonnement peut avoir uniquement un profil de journal.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurer les profils de journal à l’aide du portail Azure
Vous pouvez diffuser le journal d’activité à un concentrateur événement ou les stocker dans un compte de stockage à l’aide de l’option « Export » dans le portail Azure.

1. Accédez à la carte de **Journal d’activité de** l’aide du menu sur le côté gauche du portail.

    ![Accédez au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** en haut de la carte.

    ![Bouton Exporter dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Dans la carte qui s’affiche, vous pouvez sélectionner :  
    - régions pour lequel vous voulez exporter des événements
    - le compte de stockage à laquelle vous voulez enregistrer les événements
    - le nombre de jours que vous souhaitez conserver les événements dans l’espace de stockage. Une valeur de 0 jours conserve les journaux indéfiniment.
    - le Namespace Bus de Service dans lequel vous souhaitez qu’un concentrateur événement par diffusion en continu de ces événements.

    ![Exporter la carte du journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliquées à votre abonnement.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurer des profils journal les applets de commande PowerShell Azure
#### <a name="get-existing-log-profile"></a>Obtenir le profil de journal existant
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Ajouter un profil de journal
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriété         | Obligatoire | Description   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nom             | Oui      | Nom de votre profil de journal.                                 |
| StorageAccountId | N°       | Nº ressource du compte de stockage à laquelle le journal d’activité doit être enregistré.                         |
| serviceBusRuleId | N°       | Service Bus règle ID de l’espace de noms Bus des services que vous voulez avoir hubs événement créés dans. Est une chaîne de format suivant : `{service bus resource ID}/authorizationrules/{key name}`. |
| Emplacements        | Oui      | Liste séparées par des virgules des régions pour lequel vous voulez collecter des événements de journal d’activité.              |
| RetentionInDays  | Oui      | Nombre de jours pour lesquels les événements doivent être conservés, entre 1 et 2147483647. Une valeur nulle stocke les journaux indéfiniment (indéfiniment). |
| Catégories       | N°       | Liste séparées par des virgules des catégories d’événements qui doivent être collectés. Valeurs possibles sont écriture, suppression et Action.                                 |

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurer les profils de journal à l’aide de l’infrastructure du langage commun disponibilité sur plusieurs plateformes Azure
#### <a name="get-existing-log-profile"></a>Obtenir le profil de journal existant
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La `name` propriété doit correspondre au nom de votre profil de journal.

#### <a name="add-a-log-profile"></a>Ajouter un profil de journal
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriété         | Obligatoire | Description   |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nom             | Oui      | Nom de votre profil de journal.                                 |
| Id_stockage        | N°       | Nº ressource du compte de stockage à laquelle le journal d’activité doit être enregistré.                         |
| serviceBusRuleId | N°       | Service Bus règle ID de l’espace de noms Bus des services que vous voulez avoir hubs événement créés dans. Est une chaîne de format suivant : `{service bus resource ID}/authorizationrules/{key name}`. |
| emplacements        | Oui      | Liste séparées par des virgules des régions pour lequel vous voulez collecter des événements de journal d’activité.              |
| retentionInDays  | Oui      | Nombre de jours pour lesquels les événements doivent être conservés, entre 1 et 2147483647. Une valeur nulle stocke les journaux indéfiniment (indéfiniment).     |
| catégories       | N°       | Liste séparées par des virgules des catégories d’événements qui doivent être collectés. Valeurs possibles sont écriture, suppression et Action.                                 |

#### <a name="remove-a-log-profile"></a>Supprimer un profil de journal
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Schéma d’événement
Chaque événement dans le journal d’activité comporte un blob JSON similaire à cet exemple :

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nom de l’élément         | Description             |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorisation        | BLOB de propriétés RBAC de l’événement. En règle générale, inclut les propriétés de « action », « rôle » et « étendue ».|
| appelant               | Adresse de messagerie de l’utilisateur qui a effectué l’opération, déclaration UPN ou réclamer nom principal de service basée sur la disponibilité.|
| canaux             | Une des valeurs suivantes : « Administrateur », « Opération »|
| ID de corrélation        | En règle générale, un GUID dans le format de chaîne. Événements qui partagent un ID de corrélation appartiennent à la même action premiers.   |
| Description          | Description de texte statique d’un événement.                              |
| eventDataId          | Identificateur unique d’un événement.    |
| source d’événement          | Nom de l’infrastructure ayant généré cet événement ou service Azure.    |
| httpRequest          | BLOB décrivant la requête Http. En règle générale, inclut la « clientRequestId », « clientIpAddress » et « méthode » (HTTP. Par exemple, placer).                            |
| niveau                | Niveau de l’événement. Une des valeurs suivantes : « Critique », « Erreur », « Avertissement », « Information » et « Commentaires »  |
| resourceGroupName    | Nom du groupe de ressources pour la ressource concernée.               |
| resourceProviderName | Nom du fournisseur de ressources pour la ressource concernée             |
| ResourceURI des services Web          | Nº ressource de la ressource concernée.                               |
| operationId          | GUID partagé entre les événements qui correspondent à une seule opération.         |
| NomOpération        | Nom de l’opération.  |
| propriétés           | Définir des `<Key, Value>` paires (autrement dit, un dictionnaire) qui décrivent les détails de l’événement.                                |
| état               | Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : démarré, en cours, a réussi, a échoué, actif, résolu.                                |
| Sous-état            | En règle générale, le code d’état HTTP du reste correspondant appeler, mais peuvent également inclure d’autres chaînes décrivant un sous-état, telles que ces valeurs courantes : OK (Code d’état HTTP : 200), créé (Code d’état HTTP : 201), acceptée (Code d’état HTTP : 202), le contenu ne (Code d’état HTTP : 204), demande incorrecte (Code d’état HTTP : 400), introuvable (Code d’état HTTP : 404), conflit (Code d’état HTTP : 409), erreur interne du serveur (Code d’état HTTP : 500), Service indisponible (Code d’état HTTP : 503), délai de la passerelle (Code d’état HTTP : 504). |
| eventTimestamp       | Horodatage lorsque l’événement a été généré par le service Azure traitement de la requête qui correspond à l’événement.     |
| submissionTimestamp  | Horodatage lorsque l’événement est devenu disponible pour les requêtes.             |
| subscriptionId       | ID d’abonnement Azure.  |
| nextLink             | Jeton de continuation pour récupérer le jeu de résultats suivant lorsqu’ils sont divisés en plusieurs réponses. Généralement nécessaires lorsqu’il existe plus de 200 enregistrements.     |

## <a name="next-steps"></a>Étapes suivantes
- [Pour plus d’informations à propos du journal d’activité (anciennement les journaux d’Audit)](../resource-group-audit.md)
- [Flux de données du journal d’activité Azure aux Hubs d’événement](./monitoring-stream-activity-logs-event-hubs.md)
