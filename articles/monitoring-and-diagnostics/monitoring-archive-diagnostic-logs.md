<properties
    pageTitle="Archiver les journaux de Diagnostic Azure | Microsoft Azure"
    description="Apprenez à archiver vos journaux de Diagnostic Azure pour la rétention à long terme dans un compte de stockage."
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
    ms.date="08/26/2016"
    ms.author="johnkem"/>

# <a name="archive-azure-diagnostic-logs"></a>Archiver les journaux de Diagnostic Azure
Dans cet article, nous montrent comment vous pouvez utiliser le portail Azure, PowerShell Cmdlets, infrastructure du langage commun ou API REST archiver vos [Journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md) dans un compte de stockage. Cette option est utile si vous voulez conserver votre les journaux de Diagnostic avec une stratégie de rétention pour la sauvegarde, analyse statique ou d’audit.

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer, vous devez [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) à laquelle vous pouvez archiver vos journaux de Diagnostic. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant qui contient les données d’autres, non surveillance qu’il contient afin que vous pouvez mieux contrôler l’accès aux données d’analyse. Toutefois, si vous archivez également votre journal d’activité et les mesures de diagnostics pour un compte de stockage, il peut être préférable d’utiliser ce compte de stockage pour vos journaux de Diagnostic pour conserver toutes les données d’analyse dans un emplacement central. Le compte de stockage que vous utilisez doit être un compte de stockage objectif général, pas un compte de stockage blob.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Pour archiver votre journaux de Diagnostic à l’aide d’une des méthodes suivantes, vous définissez un **Paramètre de Diagnostic** pour une ressource particulière. Un facteur de diagnostic pour une ressource définit les catégories de journaux qui sont stockés ou diffusés en continu et sorties — concentrateur compte et/ou événement de stockage. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements de chaque catégorie de journal stockées dans un compte de stockage. Si une stratégie de rétention est définie à zéro, événements pour cette catégorie de journal sont stockés indéfiniment (c'est-à-dire quelconque, jamais). Une stratégie de rétention peut être dans le cas contraire de n’importe quel nombre de jours compris entre 1 et 2147483647. [Vous pouvez en savoir plus sur les paramètres des diagnostics ici](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archiver les journaux de Diagnostic à l’aide du portail

1. Dans le portail, cliquez sur dans la carte de ressources pour la ressource à laquelle vous souhaitez activer archivage des journaux de Diagnostic.
2. Dans la section **analyse** du menu Paramètres ressource, sélectionnez **Diagnostics**.

    ![Section du menu de ressources de surveillance](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Activez la case à cocher pour **Exporter vers un compte de stockage**, puis sélectionnez un compte de stockage. Vous pouvez également définir un nombre de jours de conservation de ces journaux à l’aide de la **rétention (jours)** curseurs. Une conservation de zéro jours stocke les journaux indéfiniment.

    ![Carte journaux des Diagnostics](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Cliquez sur **Enregistrer**.

Les journaux de diagnostic sont archivés à ce compte de stockage dès que les nouvelles données événement sont générées.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Journaux de Diagnostic archive via les applets de commande PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriété         | Obligatoire | Description                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId       | Oui      | Nº ressource de la ressource à laquelle vous souhaitez définir un facteur de diagnostic.                            |
| StorageAccountId | N°       | Nº ressource du compte de stockage à laquelle les journaux de Diagnostic doit être enregistrés.                          |
| Catégories       | N°       | Liste séparées par des virgules des catégories de journal pour activer.                                                     |
| Activé          | Oui      | Expression booléenne indiquant si diagnostics sont activées ou désactivées de cette ressource.                  |
| RetentionEnabled | N°       | Valeur booléenne qui indique si une stratégie de rétention sont activés sur cette ressource.                            |
| RetentionInDays  | N°       | Nombre de jours pour lesquels conserver les événements compris entre 1 et 2147483647. Une valeur nulle stocke les journaux indéfiniment. |

## <a name="archive-the-activity-log-via-the-cross-platform-cli"></a>Archiver le journal d’activité via l’infrastructure du langage commun disponibilité sur plusieurs plateformes

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriété         | Obligatoire | Description                                                                                           |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId       | Oui      | Nº ressource de la ressource à laquelle vous souhaitez définir un facteur de diagnostic.                            |
| Id_stockage        | N°       | Nº ressource du compte de stockage à laquelle les journaux de Diagnostic doit être enregistrés.                          |
| catégories       | N°       | Liste séparées par des virgules des catégories de journal pour activer.                                                     |
| activé          | Oui      | Expression booléenne indiquant si diagnostics sont activées ou désactivées de cette ressource.                  |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Journaux de Diagnostic archive via l’API REST
Pour plus d’informations sur la façon dont vous pouvez configurer un paramètre de diagnostic à l’aide de l’API REST de moniteur Azure, [consultez ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx) .

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schéma des journaux de Diagnostic dans le compte de stockage
Une fois que vous avez configuré archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement se produit dans une des catégories de journal que vous avez activé. Les objets BLOB dans le conteneur suivent la même mise en forme dans les journaux de Diagnostic et le journal d’activité. La structure de ces objets BLOB est la suivante :

> perspectives - journaux-{nom de la catégorie journal} / resourceId = / abonnements / {ID de l’abonnement} /RESOURCEGROUPS/ {nom du groupe de ressources} /PROVIDERS/ {nom du fournisseur ressource} / {type de ressource} / {nom de ressource} / y = {l’année numérique à quatre chiffres} / m = {à deux chiffres numérique mois} / d = {à deux chiffres numérique day} / h = {horloge de 24 heures à deux chiffres hour}/m=00/PT1H.json

Ou, plus simplement,

> perspectives - journaux-{nom de la catégorie journal} / resourceId = / {ressource Id} / y = {l’année numérique à quatre chiffres} / m = {à deux chiffres numérique mois} / d = {à deux chiffres numérique day} / h = {horloge de 24 heures à deux chiffres hour}/m=00/PT1H.json

Par exemple, un nom blob peut être :

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Chaque blob PT1H.json contient un blob JSON des événements qui se sont produites au sein de l’heure spécifiée dans l’URL de blob (par exemple, h = 12). Au cours de l’heure actuelle, événements sont ajoutés au fichier PT1H.json qu’ils se produisent. La valeur des minutes (m = 00) est toujours 00, dans la mesure où les événements de journal de Diagnostic sont classés par des objets BLOB individuels par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le groupe « enregistrements », en suivant ce format :

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nom de l’élément  | Description                                                                                                 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| heure          | Horodatage lorsque l’événement a été généré par le service Azure traitement de la requête qui correspond à l’événement. |
| resourceId    | Nº ressource de la ressource concernée.                                                                       |
| NomOpération | Nom de l’opération.                                                                                      |
| catégorie      | Catégorie de journal de l’événement.                                                                                  |
| propriétés    | Définir des `<Key, Value>` paires (c'est-à-dire dictionnaire) qui décrivent les détails de l’événement.                            |

> [AZURE.NOTE] Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.

## <a name="next-steps"></a>Étapes suivantes
- [Télécharger des objets BLOB pour l’analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [Journaux de Diagnostic flux aux Hubs d’événement](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [En savoir plus sur les journaux de Diagnostic](monitoring-overview-of-diagnostic-logs.md)
