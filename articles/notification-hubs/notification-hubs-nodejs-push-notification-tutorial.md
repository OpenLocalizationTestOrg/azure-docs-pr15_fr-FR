<properties
    pageTitle="Envoi des notifications push avec Hubs de Notification Azure et Node.js"
    description="Découvrez comment utiliser Hubs de Notification pour envoyer les notifications push à partir d’une application Node.js."
    keywords="notifications Push, push notifications,node.js push, push ios"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Envoi des notifications push avec Hubs de Notification Azure et Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Vue d’ensemble

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Ce guide vous montrent comment envoyer les notifications push à l’aide de Azure Notification Hubs directement à partir d’une application Node.js. 

Les scénarios présentés incluent l’envoi de notifications de transmission pour les applications sur les plateformes suivantes :

* Android
* iOS
* Windows Phone
* Plateforme Windows universel 

Pour plus d’informations sur les centraux notification, consultez la section [Étapes suivantes](#next) .

##<a name="what-are-notification-hubs"></a>Que sont les Hubs Notification ?

Azure Hubs de Notification fournissent une infrastructure facile à utiliser, multi-plateforme, format SVG pour l’envoi de notifications de transmission pour les appareils mobiles. Pour plus d’informations sur l’infrastructure de service, consultez la page [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Créer une Application Node.js

La première étape de ce didacticiel crée une nouvelle application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, voir [créer et déployer une application Node.js pour le Site Web Azure][nodejswebsite], [Service de nuage Node.js] [ Node.js Cloud Service] à l’aide de Windows PowerShell, ou un [Site Web avec WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurez votre Application pour utiliser Hubs de Notification

Pour utiliser Azure Notification Hubs, vous devez télécharger et utiliser le Node.js [package azure](https://www.npmjs.com/package/azure), qui inclut un jeu intégré des bibliothèques d’assistance communiquer avec les services de reste de notification push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Nœud Package Manager (NPM) permet d’obtenir le package

1.  Utilisez une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Linux) et accédez au dossier où vous avez créé votre application vide.

2.  Dans la fenêtre de commande, tapez **npm installer azure petite** .

3.  Vous pouvez exécuter manuellement la commande **** ou **dir** pour vérifier si une **nœud\_modules** dossier a été créé. Dans ce dossier, recherchez le package **azure** , qui contient les bibliothèques que vous devez accéder au Hub de Notification.

>[AZURE.NOTE] Vous pouvez en savoir plus sur l’installation de NPM sur le [blog NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm)officiel. 

### <a name="import-the-module"></a>Importer le module

À l’aide d’un éditeur de texte, ajoutez le code suivant en haut du fichier **server.js** de l’application :

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurer une connexion concentrateur de Notification Azure

L’objet **NotificationHubService** vous permet de travailler avec hubs de notification. Le code suivant crée un objet **NotificationHubService** pour le hub de notification nommé **hubname**. Ajoutez-le dans la partie supérieure du fichier **server.js** , après l’instruction pour importer le module azure :

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

La valeur **connectionstring** connexion peut être obtenue à partir du [Portail Azure] en effectuant les étapes suivantes :

1. Dans le volet de navigation gauche, cliquez sur **Parcourir**.

2. Sélectionnez **Hubs de Notification**et choisissez le hub que vous souhaitez utiliser pour l’échantillon. Vous pouvez faire référence vers le [didacticiel Windows Store prise en main](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) si vous avez besoin d’aide pour créer un nouveau concentrateur de Notification.

3. Sélectionnez **paramètres**.

4. Cliquez sur **stratégies d’accès**. Vous verrez les deux chaînes de connexion accès partagé et complète.

![Portail Azure - Hubs de Notification](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Vous pouvez également extraire la chaîne de connexion à l’aide de l’applet de commande **Get-AzureSbNamespace** fourni par [PowerShell Azure](../powershell-install-configure.md) ou la commande **espace de noms petite azure afficher** avec l' [Interface de ligne Azure (Azure commande)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Architecture générale

L’objet **NotificationHubService** expose les instances d’objet suivants pour l’envoi des notifications de transmission aux applications et aux appareils spécifiques :

* **Android** - utiliser l’objet **GcmService** , qui est disponible dans **notificationHubService.gcm**
* **iOS** - utiliser l’objet **ApnsService** , qui est accessible en **notificationHubService.apns**
* **Windows Phone** - utiliser l’objet **MpnsService** , qui est disponible dans **notificationHubService.mpns**
* **Universel plateforme Windows** - utiliser l’objet **WnsService** , qui est disponible dans **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Comment : envoyer des notifications push aux applications Android

L’objet **GcmService** fournit une méthode **Envoyer** qui peut être utilisée pour envoyer les notifications push aux applications Android. La méthode **send** accepte les paramètres suivants :

* **Balises** - l’identificateur de balise. Si aucune balise n’est fourni, la notification est envoyée aux clients al.
* **Charge utile** - JSON ou la charge utile de chaîne brute du message.
* **Rappel** - la fonction de rappel.

Pour plus d’informations sur le format de charge utile, consultez la section **charge utile** du document [Mise en œuvre GCM serveur](http://developer.android.com/google/gcm/server.html#payload) .

Le code suivant utilise l’instance **GcmService** exposé par la **NotificationHubService** pour envoyer une notification push pour tous les clients enregistrés.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Comment : envoyer des notifications push aux applications iOS

Même comme avec les applications Android décrite ci-dessus, l’objet **ApnsService** fournit une méthode **Envoyer** qui peut être utilisée pour envoyer les notifications push aux applications iOS. La méthode **send** accepte les paramètres suivants :

* **Balises** - l’identificateur de balise. Si aucune balise n’est fourni, la notification est envoyée à tous les clients.
* **Charge utile** - JSON du message ou la charge utile de chaîne.
* **Rappel** - la fonction de rappel.

Pour plus d’informations le format de charge utile, consultez la section **Charge utile de Notification** du document [Local et Guide de programmation de Notification Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Le code suivant utilise l’instance **ApnsService** exposé par la **NotificationHubService** pour envoyer un message d’alerte à tous les clients :

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Comment : envoyer des notifications push aux applications Windows Phone

L’objet **MpnsService** fournit une méthode **Envoyer** qui peut être utilisée pour envoyer les notifications push pour les applications Windows Phone. La méthode **send** accepte les paramètres suivants :

* **Balises** - l’identificateur de balise. Si aucune balise n’est fourni, la notification est envoyée à tous les clients.
* **Charge utile** - charge XML du message.
* **TargetName**  -  `toast` pour les notifications de toast. `token`pour les notifications de la vignette.
* **Classe de notification** - la priorité de la notification. Consultez la section **Éléments d’en-tête HTTP** du document [notifications à partir d’un serveur de transmission](http://msdn.microsoft.com/library/hh221551.aspx) pour les valeurs valides.
* **Options** - en-têtes de demande facultatif.
* **Rappel** - la fonction de rappel.

Pour obtenir la liste des options valides **TargetName**, **classe de notification** et d’en-tête, consultez la page [notifications à partir d’un serveur de transmission](http://msdn.microsoft.com/library/hh221551.aspx) .

Le code suivant utilise l’instance **MpnsService** exposé par la **NotificationHubService** pour envoyer une notification push toast :

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Comment : envoyer des notifications push aux applications universel Windows plateforme (UWP)

L’objet **WnsService** fournit une méthode **Envoyer** qui peut être utilisée pour envoyer les notifications push aux applications de la plateforme Windows universel.  La méthode **send** accepte les paramètres suivants :

* **Balises** - l’identificateur de balise. Si aucune balise n’est fourni, la notification est envoyée à tous les clients enregistrés.
* **Charge utile** - la charge utile du message XML.
* **Type** : le type de notification.
* **Options** - en-têtes de demande facultatif.
* **Rappel** - la fonction de rappel.

Pour obtenir la liste des types valides et d’en-têtes de requête, voir [transmission des en-têtes de demande et de réponse de service de notification](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Le code suivant utilise l’instance **WnsService** exposé par la **NotificationHubService** pour envoyer une notification d’émission toast à une application UWP :

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Étapes suivantes

Extraits de code exemple ci-dessus vous permettent de créer aisément infrastructure du service pour remettre les notifications de transmission à un large éventail de périphériques. À présent que vous avez appris les notions de base de l’utilisation de la Notification Hubs avec node.js, suivez ces liens pour en savoir plus sur la façon dont vous pouvez étendre ces fonctionnalités supplémentaires.

-   Voir la référence MSDN pour [Notification Azure Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visitez le référentiel [Azure SDK pour nœud] sur GitHub pour plus d’exemples et détails d’implémentation.

  [Azure SDK pour nœud]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site Web avec WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portail Azure]: https://portal.azure.com
