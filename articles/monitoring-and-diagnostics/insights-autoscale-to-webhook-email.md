<properties
    pageTitle="Utiliser les actions échelle pour envoyer des messages électroniques et webhook notifications. | Microsoft Azure"
    description="Découvrez comment utiliser échelle actions à l’appel des URL web ou envoyer de notifications par courrier électronique dans le moniteur Azure. "
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
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Utiliser les actions d’échelle pour envoyer un e-mail et webhook notifications d’alerte dans le moniteur d’Azure

Cet article vous explique comment permet de paramétrer déclencheurs afin que vous pouvez appeler URL web spécifique ou envoyer des messages électroniques en fonction des actions échelle dans Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks vous permet d’acheminer les notifications d’alerte Azure à d’autres systèmes pour les notifications de traitement d’images ou personnalisées. Par exemple, le routage de l’alerte aux services qui peuvent gérer une requête web entrante à envoyer des que SMS, journal des bogues, avertir de services d’une équipe à l’aide de la conversation ou la messagerie, etc.. La webhook URI doit être un point de terminaison HTTP ou HTTPS valide.

## <a name="email"></a>Messagerie
Messagerie peut être envoyée à n’importe quelle adresse de messagerie valide. Les administrateurs et co-création de l’abonnement dans lequel la règle est en cours d’exécution est également communiquée.


## <a name="cloud-services-and-web-apps"></a>Services de cloud et Web Apps
Vous pouvez désactiver cette basé sur à partir du portail Azure pour Services en nuage et de batteries de serveurs (applications Web).

- Sélectionnez la mesure de **l’échelle par** .

![mettre à l’échelle par](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Jeux d’échelle Machine virtuelle
Pour une machine virtuelle plus récente créée avec le Gestionnaire de ressources (ensembles d’échelle Machine virtuelle), vous pouvez la configurer à l’aide de l’API REST, le Gestionnaire de ressources modèles, PowerShell et infrastructure du langage commun. Une interface de portail n’est pas encore disponible.
Lorsque vous utilisez le modèle de l’API REST ou le Gestionnaire de ressources, inclure l’élément notifications avec les options suivantes.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Champ                              |Obligatoire ? |Description|
|---                                |---        |---|
|opération                          |Oui        |la valeur doit être « Échelle »|
|sendToSubscriptionAdministrator    |Oui        |valeur doit être « true » ou « false »|
|sendToSubscriptionCoAdministrators |Oui        |valeur doit être « true » ou « false »|
|customEmails                       |Oui        |valeur peut être null [] ou tableau de chaînes de messages électroniques|
|webhooks                           |Oui        |valeur peut être null ou valide Uri|
|serviceUri                         |Oui        |un https valide Uri|
|propriétés                         |Oui        |valeur doit être vide {} ou peut contenir des paires clé-valeur|


## <a name="authentication-in-webhooks"></a>Authentification dans webhooks
Il existe deux types d’URI d’authentification :

1. Authentification de base de jeton, où vous enregistrez le webhook URI avec un ID de jeton comme paramètre de requête. Par exemple, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Authentification de base, où vous utilisez un ID d’utilisateur et mot de passe. Par exemple,https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Schéma de charge utile webhook notifications d’échelle
Lorsque la notification d’échelle est générée, les métadonnées suivante sont incluses dans la charge utile webhook :

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Champ  |Obligatoire ?|    Description|
|---|---|---|
|état |Oui    |L’état qui indique qu’une action échelle automatique a été générée|
|opération| Oui |Pour une augmentation des instances, il sera « Échelle arrière » et une diminution des instances, vous devez « Échelle In »|
|contexte|   Oui |Le contexte d’action échelle automatique|
|horodatage| Oui |Horodatage déclenchement de l’action échelle automatique|
|ID |Oui|   ID de gestionnaire de ressources du paramètre échelle automatique|
|nom   |Oui|   Le nom du paramètre échelle automatique|
|plus d’informations|   Oui |Explication de l’action ayant le service échelle automatique et la modification dans le nombre d’instances|
|subscriptionId|    Oui |ID de l’abonnement de la ressource cible qui est mis à l’échelle|
|resourceGroupName| Oui|    Nom du groupe de ressources de la ressource cible qui est mis à l’échelle|
|resourceName   |Oui|   Nom de la ressource cible qui est mis à l’échelle|
|type de ressource   |Oui|   Les trois valeurs prises en charge : « microsoft.classiccompute/domainnames/slots/roles » - Service Cloud rôles, « microsoft.compute/virtualmachinescalesets » - jeux d’échelle Machine virtuelle et « Microsoft.Web/serverfarms » - Web App|
|resourceId |Oui|ID de gestionnaire de ressources de la ressource cible qui est mis à l’échelle|
|portalLink |Oui    |Lien portail Azure vers la page de résumé de la ressource cible|
|oldCapacity|   Oui |Le nombre d’instances (ancien) en cours lorsque échelle automatique a effectué une action échelle|
|newCapacity|   Oui |Le nombre d’instances de nouveau échelle mise à l’échelle de la ressource|
|Propriétés|    N°| Facultatif. Jeu de < clé, valeur > paires (par exemple, dictionnaire < String, String >). Le champ propriétés est facultatif. Dans une interface utilisateur personnalisée ou un flux de travail logique en fonction de l’application, vous pouvez entrer des clés et des valeurs qui peuvent être passés à l’aide de la charge utile. Une autre façon pour passer les propriétés personnalisées à l’appel sortant webhook consiste à utiliser la webhook URI lui-même (en tant que paramètres de la requête)|
