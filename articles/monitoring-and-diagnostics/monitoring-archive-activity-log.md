<properties
    pageTitle="Archiver le journal d’activité Azure | Microsoft Azure"
    description="Apprenez à archiver le journal activité Azure pour la rétention à long terme dans un compte de stockage."
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
    ms.date="08/23/2016"
    ms.author="johnkem"/>

# <a name="archive-the-azure-activity-log"></a>Archiver le journal d’activité Azure
Dans cet article, nous montrent comment vous pouvez utiliser le portail Azure, PowerShell Cmdlets ou infrastructure du langage commun disponibilité sur plusieurs plateformes à archiver le [**Journal d’activité Azure**](monitoring-overview-activity-logs.md) dans un compte de stockage. Cette option est utile si vous voulez conserver votre journal d’activité plus de 90 jours (avec un contrôle total sur la stratégie de rétention) pour la sauvegarde, analyse statique ou d’audit. Si vous n’avez besoin de conserver vos événements de 90 jours ou moins vous n’avez pas besoin de configurer archivage à un compte de stockage, dans la mesure où les événements de journal d’activité sont conservés dans la plateforme Azure pendant 90 jours sans l’archivage.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer, vous devez [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) à laquelle vous pouvez archiver le journal activité. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant qui contient les données d’autres, non surveillance qu’il contient afin que vous pouvez mieux contrôler l’accès aux données d’analyse. Toutefois, si vous archivez également les journaux de Diagnostic et les mesures pour un compte de stockage, il peut être préférable d’utiliser ce compte de stockage pour votre journal d’activité pour conserver toutes les données d’analyse dans un emplacement central. Le compte de stockage que vous utilisez doit être un compte de stockage objectif général, pas un compte de stockage blob.

## <a name="log-profile"></a>Profil de journal
Pour archiver le journal d’activité à l’aide d’une des méthodes suivantes, vous définissez le **Profil de journal** pour un abonnement. Le profil de journal définit le type d’événements qui sont stockés ou diffusés en continu et sorties — concentrateur compte et/ou événement de stockage. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements stockées dans un compte de stockage. Si la stratégie de rétention est définie à zéro, les événements sont stockés indéfiniment. Dans le cas contraire, cela peut être défini à n’importe quelle valeur comprise entre 1 et 2147483647. [Vous pouvez en savoir plus sur les journaux des profils ici](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles).

## <a name="archive-the-activity-log-using-the-portal"></a>Archiver le journal d’activité à l’aide du portail
1. Dans le portail, cliquez sur le lien du **Journal d’activité** dans la barre de navigation gauche. Si vous ne voyez pas un lien du journal d’activité, cliquez sur le lien **d’Autres Services** .

    ![Accédez à la carte du journal d’activité](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. En haut de la carte, cliquez sur **Exporter**.

    ![Cliquez sur le bouton Exporter](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. Dans la carte qui s’affiche, activez la case à cocher pour **Exporter vers un compte de stockage** et sélectionnez un compte de stockage.

    ![Configurer un compte de stockage](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. À l’aide du curseur ou la zone de texte, définissez un nombre de jours pour lesquels les événements du journal d’activité doivent être conservées dans votre compte de stockage. Si vous préférez que vos données conservées indéfiniment dans le compte de stockage, définissez cette valeur à zéro.
5. Cliquez sur **Enregistrer**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiver le journal d’activité via PowerShell
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| Propriété         | Obligatoire | Description                                                                                                                                                                                                                                                                                       |
|------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorageAccountId | N°       | Nº ressource du compte de stockage à laquelle les journaux d’activité doit être enregistrés.                                                                                                                                                                                                                        |
| Emplacements        | Oui      | Liste séparées par des virgules des régions pour lequel vous voulez collecter des événements de journal d’activité. Vous pouvez afficher une liste de toutes les régions [en accédant à cette page](https://azure.microsoft.com/en-us/regions) ou à l’aide de [L’API REST de gestion Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| RetentionInDays  | Oui      | Nombre de jours pour lesquels les événements doivent être conservés, entre 1 et 2147483647. Une valeur nulle stocke les journaux indéfiniment (indéfiniment).                                                                                                                                                                                             |
| Catégories       | Oui      | Liste séparées par des virgules des catégories d’événements qui doivent être collectés. Valeurs possibles sont écriture, suppression et Action.                                                                                                                                                                                 |
## <a name="archive-the-activity-log-via-cli"></a>Archiver le journal d’activité via l’infrastructure du langage commun
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| Propriété        | Obligatoire | Description                                                                                                                                                                                                                                                                                       |
|-----------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nom            | Oui      | Nom de votre profil de journal.                                                                                                                                                                                                                                                                         |
| Id_stockage       | N°       | Nº ressource du compte de stockage à laquelle les journaux d’activité doit être enregistrés.                                                                                                                                                                                                                        |
| emplacements       | Oui      | Liste séparées par des virgules des régions pour lequel vous voulez collecter des événements de journal d’activité. Vous pouvez afficher une liste de toutes les régions [en accédant à cette page](https://azure.microsoft.com/en-us/regions) ou à l’aide de [L’API REST de gestion Azure](https://msdn.microsoft.com/library/azure/gg441293.aspx). |
| retentionInDays | Oui      | Nombre de jours pour lesquels les événements doivent être conservés, entre 1 et 2147483647. Une valeur nulle stockera les journaux indéfiniment (indéfiniment).                                                                                                                                                                                             |
| catégories      | Oui      | Liste séparées par des virgules des catégories d’événements qui doivent être collectés. Valeurs possibles sont écriture, suppression et Action.                                                                                                                                                                                 |

## <a name="storage-schema-of-the-activity-log"></a>Schéma de stockage du journal d’activité
Une fois que vous avez configuré archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement de journal d’activité se produit. Les objets BLOB dans le conteneur suivent la même mise en forme dans le journal d’activité et les journaux de Diagnostic. La structure de ces objets BLOB est la suivante :

> perspectives-opérationnels-journaux/name = par défaut/resourceId = / abonnements / {ID de l’abonnement} / y = {l’année numérique à quatre chiffres} / m = {à deux chiffres numérique mois} / d = {à deux chiffres numérique day} / h = {horloge de 24 heures à deux chiffres hour}/m=00/PT1H.json

Par exemple, un nom blob peut être :

> Insights-Operational-Logs/Name=default/ResourceID=/Subscriptions/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.JSON

Chaque blob PT1H.json contient un blob JSON des événements qui se sont produites au sein de l’heure spécifiée dans l’URL de blob (par exemple, h = 12). Au cours de l’heure actuelle, événements sont ajoutés au fichier PT1H.json qu’ils se produisent. La valeur des minutes (m = 00) est toujours 00, dans la mesure où les événements du journal d’activité sont classés par des objets BLOB individuels par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le groupe « enregistrements », en suivant ce format :

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
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
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nom de l’élément    | Description                                                                                                    |
|-----------------|----------------------------------------------------------------------------------------------------------------|
| heure            | Horodatage lorsque l’événement a été généré par le service Azure traitement de la requête qui correspond à l’événement.    |
| resourceId      | Nº ressource de la ressource concernée.                                                                          |
| NomOpération   | Nom de l’opération.                                                                                         |
| catégorie        | Catégorie de l’action, par exemple. Écriture, lecture, Action.                                                               |
| resultType      | Le type de résultat, par exemple. Début de réussite, d’échec                                                            |
| resultSignature | Dépend du type de ressource.                                                                                  |
| durationMs      | Durée de l’opération en millisecondes                                                                      |
| callerIpAddress | Adresse IP de l’utilisateur qui a effectué l’opération, déclaration UPN ou réclamer nom principal de service basée sur la disponibilité.         |
| ID de corrélation   | En règle générale, un GUID dans le format de chaîne. Événements qui partagent un ID de corrélation appartiennent à la même action premiers.         |
| identité        | Blob JSON décrivant l’autorisation et revendications.                                                             |
| autorisation   | BLOB de propriétés RBAC de l’événement. En règle générale, inclut les propriétés de « action », « rôle » et « étendue ».            |
| niveau           | Niveau de l’événement. Une des valeurs suivantes : « Critique », « Erreur », « Avertissement », « Information » et « Commentaires » |
| emplacement        | Région dans laquelle l’emplacement s’est produite (ou global).                                                             |
| propriétés      | Définir des `<Key, Value>` paires (c'est-à-dire dictionnaire) qui décrivent les détails de l’événement.                             |

> [AZURE.NOTE] Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.

## <a name="next-steps"></a>Étapes suivantes
- [Télécharger des objets BLOB pour l’analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Flux de données du journal d’activité aux Hubs d’événement](monitoring-stream-activity-logs-event-hubs.md)
- [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)
