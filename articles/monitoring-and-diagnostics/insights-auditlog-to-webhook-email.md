<properties
    pageTitle="Configurer un webhook sur les alertes de journal d’activité Azure | Microsoft Azure"
    description="Découvrez comment utiliser des alertes de journal d’activité pour appeler webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurer un webhook sur un alertes Azure journal d’activité

Webhooks vous autorise à acheminer une notification d’alerte Azure à d’autres systèmes pour les actions postérieures à la transformation ou personnalisées. Vous pouvez utiliser un webhook sur une alerte pour acheminer aux services d’envoyer des SMS, enregistrer les bogues, avertir une équipe via les services de messagerie/conversation ou effectuer diverses autres actions. Cet article décrit comment définir une webhook sur une alerte Azure le journal d’activité et ce à quoi ressemble la charge utile pour la publication HTTP à un webhook. Pour plus d’informations sur la configuration et le schéma d’une alerte métrique Azure, [consultez cette page à la place](insights-webhooks-alerts.md). Vous pouvez également configurer une alerte de journal d’activité d’envoyer des messages lorsqu’elle est activée.

>[AZURE.NOTE] Cette fonctionnalité n’est actuellement dans l’aperçu, attendez-vous performances et la qualité variable.

Vous pouvez configurer une alerte de journal d’activité avec les [Applets de commande PowerShell Azure](insights-powershell-samples.md#create-alert-rules), [Infrastructure du langage commun disponibilité sur plusieurs plateformes](insights-cli-samples.md#work-with-alerts)ou [L’API REST Azure moniteur](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>La webhook d’authentification
Inscrivez webhook peut s’authentifier à l’aide d’une des méthodes suivantes :

1. **Autorisation basée sur un jeton** - la webhook URI est enregistré avec un ID de jeton, par exemple. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **D’autorisation de base** - la webhook URI est enregistré avec un nom d’utilisateur et mot de passe, par exemple. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de charge
L’opération POST contient la charge utile JSON suivant et le schéma pour toutes les alertes basées sur le journal d’activité. Ce schéma est semblable à celui utilisé par les alertes de métrique.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nom de l’élément       |Description|
|---                |---|
|état             |Utilisée pour les alertes métriques. Toujours défini sur « activé » pour les alertes de journal d’activité.|
|contexte            |Contexte de l’événement.|
|resourceProviderName|Le fournisseur de ressources de la ressource concerné.|
|conditionType      |Toujours « événement ».|
|nom               |Nom de la règle d’alerte.|
|ID                 |Nº ressource de l’alerte.|
|Description        |Description de l’alerte en tant que jeu lors de la création de l’alerte.|
|subscriptionId     |ID d’abonnement Azure.|
|horodatage          |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande.|
|resourceId         |Nº ressource de la ressource concernée.|
|resourceGroupName  |Nom du groupe de ressources pour la ressource concernée|
|propriétés         |Définir des `<Key, Value>` paires (c'est-à-dire `Dictionary<String, String>`) qui inclut des informations concernant l’événement.|
|événement              |Élément contenant les métadonnées relatives à l’événement.|
|autorisation      |Les propriétés RBAC de l’événement. Il s’agit généralement l’action « », « rôle » et « portée ».|
|catégorie           |Catégorie de l’événement. Incluent des valeurs prises en charge : administration, d’alerte, sécurité, ServiceHealth, recommandations.|
|appelant             |Adresse de messagerie de l’utilisateur ayant effectué l’opération, déclaration UPN ou réclamer nom principal de service basée sur la disponibilité. Peut être null pour certains appels système.|
|ID de corrélation      |En règle générale, un GUID sous forme de chaîne. Événements avec correlationId appartiennent à la même action plus grande et généralement partagent un ID de corrélation.|
|eventDescription   |Description du texte statique de l’événement.|
|eventDataId        |Identificateur unique de l’événement.|
|source d’événement        |Nom de l’infrastructure ayant généré l’événement ou service Azure.|
|httpRequest        |Inclut généralement le « clientRequestId », « clientIpAddress » et la « méthode » (méthode HTTP place, par exemple).|
|niveau              |Une des valeurs suivantes : « Critique », « Erreur », « Avertissement », « Information » et « Commentaires ».|
|operationId        |En règle générale, un GUID partagé entre les événements correspondant à une seule opération.|
|NomOpération      |Nom de l’opération.|
|propriétés         |Propriétés de l’événement.|
|état             |Chaîne. État de l’opération. Incluent des valeurs communes : « Démarré », « En cours », « Réussite », « A échoué », « Actif », « Résolu ».|
|Sous-état          |Inclut généralement le code d’état HTTP de l’appel reste correspondant. Il peut-être également inclure d’autres chaînes décrivant un sous-état. Incluent des valeurs sous-état communes : OK (Code d’état HTTP : 200), créé (Code d’état HTTP : 201), acceptée (Code d’état HTTP : 202), le contenu ne (Code d’état HTTP : 204), demande incorrecte (Code d’état HTTP : 400), introuvable (Code d’état HTTP : 404), conflit (Code d’état HTTP : 409), erreur interne du serveur (Code d’état HTTP : 500), Service indisponible (Code d’état HTTP : 503), délai de la passerelle (Code d’état HTTP : 504)|

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)
- [Exécuter des scripts Automation Azure (procédures opérationnelles) sur les alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utiliser une logique de l’application pour envoyer un SMS via Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple concerne métriques alertes, mais peut être modifié pour travailler avec une alerte de journal d’activité.
- [Utiliser l’application logique pour envoyer un message à partir d’une alerte Azure marge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple concerne métriques alertes, mais peut être modifié pour travailler avec une alerte de journal d’activité.
- [Utiliser l’application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple concerne métriques alertes, mais peut être modifié pour travailler avec une alerte de journal d’activité.
