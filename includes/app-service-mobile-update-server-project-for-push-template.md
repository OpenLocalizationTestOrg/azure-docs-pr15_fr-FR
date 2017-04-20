Dans cette section, vous mettez à jour code dans votre projet principal applications Mobile existant pour envoyer une notification push chaque fois qu’un nouvel élément est ajouté. Ceci est optimisé par la fonctionnalité de [modèle](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) des Hubs de Notification, l’activation de disponibilité sur plusieurs plateformes push. Les différents clients sont enregistrés pour les notifications push à l’aide de modèles et une seule push universel peut accéder à toutes les plateformes clients.

Choisissez la procédure ci-dessous qui correspond à votre type de projet principal&mdash; [.NET principal](#dotnet) ou [Node.js principal](#nodejs).

### <a name="dotnet"></a>Projet principal .NET
1. Dans Visual Studio, avec le bouton droit de la project server et cliquez sur **Gérer les Packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs`, puis cliquez sur **installer**. Cette procédure installe la bibliothèque Hubs de Notification d’envoi des notifications à partir de votre serveur principal.

3. Dans le projet serveur, ouvrez **contrôleurs** > **TodoItemController.cs**et ajoutez les éléments suivants à l’aide des instructions :

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. Dans la méthode **PostTodoItem** , ajoutez le code suivant après l’appel à **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Envoie une notification de modèle qui contient l’élément. Texte lorsqu’un nouvel élément est inséré.

4. Publier à nouveau le projet serveur. 

### <a name="nodejs"></a>Node.js et project

1. Si vous n’avez pas déjà fait, [Téléchargez le projet principal de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou bien utilisez l' [éditeur en ligne dans le portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Remplacez le code existant dans todoitem.js avec les éléments suivants :

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    Cela envoie une notification de modèle qui contient la item.text lorsqu’un nouvel élément est inséré.

2. Lorsque vous modifiez le fichier sur votre ordinateur local, publier à nouveau le projet serveur.
