<properties
    pageTitle="Exemples de démarrage rapide Azure infrastructure du langage commun moniteur. | Microsoft Azure"
    description="Exemples de commandes infrastructure du langage commun pour les fonctionnalités Azure moniteur. Moniteur Azure est un service Microsoft Azure qui vous envoie des notifications, appelez URL web en fonction des valeurs de données de télémétrie configurée et les Services en nuage échelle, Machines virtuelles et Web Apps permet."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor--cross-platform-cli-quick-start-samples"></a>Exemples de démarrage rapide Azure infrastructure du langage commun disponibilité sur plusieurs plateformes moniteur

Cet article vous explique exemple commandes de l’interface de ligne (commande) pour vous aider à accéder aux fonctionnalités moniteur Azure. Azure moniteur permet de Services de Cloud échelle, Machines virtuelles et applications Web et envoie des notifications ou appel URL web en fonction des valeurs de données de télémétrie configuré.

>[AZURE.NOTE] Moniteur Azure est le nouveau nom de ce qui était appelé « Perspectives Azure » jusqu'à 25 septembre 2016. Toutefois, les espaces de noms et donc les commandes ci-dessous contient toujours les « perspectives ».


## <a name="prerequisites"></a>Conditions préalables

Si vous n’avez pas encore installé l’infrastructure du langage commun Azure, consultez [installer l’infrastructure du langage commun Azure](../xplat-cli-install.md). Si vous ne maîtrisez pas Azure infrastructure du langage commun, vous trouverez d’autres informations, voir [utiliser l’infrastructure du langage commun Azure pour Mac, Linux et Windows avec le Gestionnaire de ressources Azure](../xplat-cli-azure-resource-manager.md).


Dans Windows, installez npm à partir du [site Web Node.js](https://nodejs.org/). Après avoir terminé l’installation, à l’aide de CMD.exe avec des privilèges d’exécuter en tant qu’administrateur, exécutez la commande suivante à partir du dossier dans lequel npm est installé :

```console
npm install azure-cli --global
```

Ensuite, accédez à n’importe quel dossier/emplacement souhaité, puis tapez à la ligne de commande :

```console
azure help
```

## <a name="log-in-to-azure"></a>Se connecter à Azure

La première étape consiste à vous connecter à votre compte Azure.

```console
azure login
```

Après avoir exécuté cette commande, vous devez connecter via les instructions à l’écran. Ensuite, vous consultez votre compte, TenantId et par défaut ID d’abonnement. Toutes les commandes fonctionnent dans le cadre de votre abonnement par défaut.

Pour répertorier les détails de votre abonnement actuel, utilisez la commande suivante.

```console
azure account show
```

Pour modifier le contexte de travail à un autre abonnement, utilisez la commande suivante.

```console
azure account set "subscription ID or subscription name"
```

Pour utiliser les commandes Azure le Gestionnaire de ressources et Azure moniteur, vous devez être en mode Azure le Gestionnaire de ressources.

```console
azure config mode arm
```

Pour afficher une liste de toutes les commandes Azure moniteur pris en charge, effectuez les opérations suivantes.

```console
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>Afficher les journaux d’audit pour un abonnement

Pour afficher une liste des journaux d’audit, effectuez les opérations suivantes.

```console
azure insights logs list [options]
```

Essayez les procédures suivantes pour afficher toutes les options disponibles.

```console
azure insights logs list -help
```

Voici un exemple dans les journaux de liste en un resourceGroup

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemple pour les journaux de liste par l’appelant

```console
azure insights logs list --caller "myname@company.com"
```

Exemple de liste ouvre une session en appelant sur un type de ressource, au sein d’une date de début et de fin

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Utiliser des alertes
Vous pouvez utiliser les informations dans la section pour utiliser des alertes.

### <a name="get-alert-rules-in-a-resource-group"></a>Obtenir des règles d’alerte dans un groupe de ressources

```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Créer une règle d’alerte métrique

```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Créer une règle d’alerte journal

```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Créer une règle d’alerte webtest

```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Supprimer une règle d’alerte

```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Profils du journal
Utilisez les informations dans cette section pour travailler avec les profils de journal.

### <a name="get-a-log-profile"></a>Obtenir un profil de journal

```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Ajouter un profil de journal sans rétention

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Ajouter un profil de journal avec rétention

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Ajouter un profil de journal avec rétention et EventHub

```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostics
Utilisez les informations dans cette section pour travailler avec les paramètres de diagnostic.

### <a name="get-a-diagnostic-setting"></a>Obtenir un facteur de diagnostic

```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Désactiver un paramètre de diagnostic

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Activer un paramètre de diagnostic sans rétention

```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Échelle automatique
Utilisez les informations dans cette section pour travailler avec les paramètres d’échelle. Vous devez modifier ces exemples.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtenir les paramètres d’échelle pour un groupe de ressources

```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtenir les paramètres d’échelle par un nom dans un groupe de ressources

```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Définir les paramètres d’auotoscale

```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
