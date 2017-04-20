<properties
    pageTitle="Liaison de fonctions Notification concentrateur Azure | Microsoft Azure"
    description="Comprendre comment utiliser la liaison Azure Notification concentrateur dans Azure fonctions."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctionne, fonctions, traitement des événements, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Liaison de sortie Azure concentrateur de Notification de fonctions

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et liaisons Azure Notification concentrateur de code dans des fonctions Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Les fonctions peuvent envoyer des notifications push à l’aide d’un concentrateur de Notification Azure configuré avec quelques lignes de code. Toutefois, le Hub de Notification Azure doit être configuré pour les Services (solution plateforme Notifications) que vous voulez utiliser. Pour plus d’informations sur la configuration d’un concentrateur de Notification Azure et développement d’une application cliente qu’inscrire pour recevoir des notifications, voir [mise en route avec Hubs de Notification](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) , puis cliquez sur votre plateforme client cible dans la partie supérieure.

Les notifications que vous envoyez peuvent être natives notifications ou les notifications de modèle. Notifications natives ciblent une plateforme de notification spécifique tel que configuré dans le `platform` propriété de la liaison de sortie. Une notification de modèle peut servir à plusieurs plateformes cibles.   

## <a name="notification-hub-output-binding-properties"></a>Propriétés de liaison de sortie notification concentrateur

Le fichier function.json fournit les propriétés suivantes :

- `name`: Nom variable utilisé dans le code de la fonction du message de notification concentrateur.
- `type`: doit être définie sur *« notificationHub »*.
- `tagExpression`: Expressions balise permettent de spécifier que les notifications envoyé à un ensemble d’appareils qui se sont enregistrés pour recevoir des notifications qui correspondent à l’expression.  Pour plus d’informations, consultez [expressions routage et balise](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nom de la ressource de concentrateur de notification dans le portail Azure.
- `connection`: Cette chaîne de connexion doit être une chaîne de connexion de **Paramètre de l’Application** la valeur *DefaultFullSharedAccessSignature* pour votre concentrateur de notification.
- `direction`: doit être définie sur *« arrière »*. 
- `platform`: La propriété platform Indique la plate-forme notification vos cibles de notification. Doit être une des valeurs suivantes :
    - `template`: Il s’agit de la plateforme par défaut si la propriété plateforme est omise de la liaison de sortie. Notifications de modèle peuvent servir à cibler n’importe quelle plate-forme configuré sur le Hub de Notification Azure. Pour plus d’informations sur l’utilisation des modèles pour envoyer des notifications de plateforme croisée avec un concentrateur de Notification Azure en général, voir [modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Service de Notification Push Apple. Pour plus d’informations sur la configuration du concentrateur de notification pour APNS et la réception de la notification dans une application client, voir [envoi des notifications push pour iOS avec Hubs de Notification Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Appareil Amazon de messagerie](https://developer.amazon.com/device-messaging). Pour plus d’informations sur la configuration du concentrateur de notification pour ADM et la réception de la notification dans une application Kindle, voir [Prise en main avec Hubs de Notification pour les applications Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Messagerie Google Cloud](https://developers.google.com/cloud-messaging/). Messagerie Cloud firebase, ce qui correspond à la nouvelle version de GCM, est également prise en charge. Pour plus d’informations sur la configuration du concentrateur de notification pour GCM/FCM et la réception de la notification dans une application client Android, voir [envoi des notifications push pour Android avec Hubs de Notification Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Services de Notification Push Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) ciblage plateformes de Windows. Windows Phone 8.1 et versions ultérieur est également prise en charge par WNS. Pour plus d’informations sur la configuration du concentrateur de notification pour WNS et la réception de la notification dans une application universel Windows plateforme (UWP), voir [prise en main de Notification Hubs pour Windows universel plateforme applications](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Service de Notification Push Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Cette plate-forme prend en charge Windows Phone 8 et plates-formes de versions antérieures de Windows Phone. Pour plus d’informations sur la configuration du concentrateur de notification pour MPNS et la réception de la notification dans une application Windows Phone, consultez [envoi des notifications push avec Hubs de Notification Azure sur Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Exemple function.json :

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Paramètres de chaîne de connexion de concentrateur notification

Pour utiliser une sortie de concentrateur de Notification de liaison, vous devez configurer la chaîne de connexion pour le hub. Cela peut être terminé sous l’onglet *intégrer* en sélectionnant votre concentrateur de notification ou en créer un nouveau. 

Vous pouvez ajouter manuellement une chaîne de connexion pour un concentrateur existant en ajoutant une chaîne de connexion pour la *DefaultFullSharedAccessSignature* à votre concentrateur de notification. Cette chaîne de connexion fournit votre autorisation d’accès de fonction pour envoyer des messages de notification. La valeur de chaîne de connexion *DefaultFullSharedAccessSignature* sont accessibles à partir du bouton **clés** dans la carte principal de la ressource de concentrateur de notification dans le portail Azure. Pour ajouter manuellement une chaîne de connexion pour votre plateforme, procédez comme suit : 

1. Dans la carte de **fonction application** du portail Azure, cliquez sur **paramètres de l’application fonction > accédez à paramètres du Service d’application**.

2. Dans la carte de **paramètres** , cliquez sur **Paramètres de l’Application**.

3. Faites défiler jusqu'à la section **chaînes de connexion** et ajouter une entrée nommée pour valeur *DefaultFullSharedAccessSignature* pour votre concentrateur de notification. Modifier le type **personnalisé**.
4. Faire référence à votre nom de chaîne de connexion dans les liaisons de sortie. Semblable à **MyHubConnectionString** utilisée dans l’exemple ci-dessus.

## <a name="native-notification-examples"></a>Exemples de notification natif

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Notifications natives APNS avec c# déclencheurs en file d’attente

Cet exemple montre comment utiliser des types définis dans la [Bibliothèque de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification APNS native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notifications natives GCM avec c# déclencheurs en file d’attente

Cet exemple montre comment utiliser des types définis dans la [Bibliothèque de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification GCM native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Notifications natives WNS avec c# déclencheurs en file d’attente

Cet exemple montre comment utiliser des types définis dans la [Bibliothèque de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) pour envoyer une notification toast WNS native. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Exemples de notification de modèle

#### <a name="template-example-for-nodejs-timer-triggers"></a>Exemple de modèle pour déclencheurs de minuteur Node.js 

Cet exemple envoie une notification d’un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient `location` et `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Exemple de modèle pour déclencheurs minuteur F #

Cet exemple envoie une notification d’un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient `location` et `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Exemple de modèle à l’aide d’un paramètre de sortie 

Cet exemple envoie une notification d’un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient une `message` espace réservé dans le modèle.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Exemple de modèle avec la fonction asynchrone

Si vous utilisez le code asynchrone, les paramètres de sortie ne sont pas autorisées. Dans ce cas utiliser `IAsyncCollector` pour renvoyer votre notification de modèle. Le code suivant est un exemple asynchrone du code ci-dessus. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Exemple de modèle à l’aide de JSON 

Cet exemple envoie une notification d’un [enregistrement de modèle](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) qui contient une `message` espace réservé dans le modèle à l’aide d’une chaîne JSON valide.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Exemple de modèle à l’aide des types de bibliothèques Hubs de Notification

Cet exemple montre comment utiliser des types définis dans la [Bibliothèque de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
