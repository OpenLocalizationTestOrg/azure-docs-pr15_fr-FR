<properties
    pageTitle="Notification Hubs - architecture d’entreprise Push"
    description="Conseils sur l’utilisation de Hubs de Notification Azure dans un environnement d’entreprise"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Guide architecture d’entreprise push

Les entreprises se tournent aujourd'hui progressivement vers la création d’applications mobiles pour leurs utilisateurs finaux (externe) ou pour les employés (internes). Ils disposent des systèmes d’arrière-plan existants en place qu’il s’agisse d’ordinateurs centraux ou certaines applications métiers qui doivent être intégrées à l’architecture de l’application mobile. Ce guide traite de la meilleure façon de faire cette intégration recommander des solutions possibles pour des scénarios courants.

Une exigence fréquente est pour l’envoi de notification push aux utilisateurs via l’application mobile lorsqu’un événement d’intérêt se produit dans les systèmes de serveur principal. Par exemple un client de la banque disposant de l’application de la banque bancaires sur son iPhone souhaite recevoir une notification lorsqu’un débit est effectué au-dessus d’un certain montant à partir de son compte ou un intranet où un employé de service Finances disposant d’une application approbation budgétaire sur son Windows Phone souhaite recevoir une notification lorsqu’il reçoit une demande d’approbation.

Le compte bancaire ou la transformation d’approbation sont susceptible d’être exécuté de certains système principal qui doit déclencher un push à l’utilisateur. Il peut y avoir plusieurs tels systèmes principaux qui doivent toutes les build le même type d’une logique à mettre en œuvre push lorsqu’un événement déclenche une notification. La complexité ici se trouve dans l’intégration des systèmes principaux plusieurs avec un système push unique, où les utilisateurs finaux peuvent abonné à différentes notifications et même peut-être plusieurs applications mobiles par exemple, dans le cas d’applications mobiles intranet où une application mobile peut à recevoir des notifications de plusieurs tels systèmes principaux. Les systèmes principaux ne savez ou savoir également de sémantique/technologie push pour une solution commune ici a été traditionnellement d’introduire un composant qui consulte les systèmes de serveur principal pour tous les événements d’intérêt et qui est chargé d’envoyer les messages envoyés au client.
Ici, nous allons aborder une solution plus efficace à l’aide de Bus des services Azure - modèle rubrique/abonnement qui permet de réduire la complexité tout en effectuant la solution scalable.

Voici l’architecture générale de la solution (GRG avec plusieurs applications mobiles mais appliquent lorsqu’il y a une application mobile)

## <a name="architecture"></a>Architecture

![][1]

La clé dans ce diagramme architectural est Bus des services Azure qui fournit un modèle (plus dessus au [Service Bus publication/Sub de programmation]) de programmation rubriques/abonnements. Le combiné, c'est-à-dire dans ce cas, le système principal Mobile (généralement [Azure le Service Mobile], ce qui lancera une diffusion sur les applications mobiles) ne reçoit pas de messages directement à partir des systèmes serveur principal, mais à la place nous avons une couche d’abstraction intermédiaires fournie par [Bus des services Azure] qui permet de serveur principal mobile pour recevoir des messages à partir d’un ou plusieurs systèmes principaux. Une rubrique Bus de Service doit être créé pour chacune des systèmes principaux, par exemple compte, h, Finance qui sont fondamentalement « rubriques « d’intérêt qui génèrent des messages à envoyer en tant que notification push. Les systèmes et envoyer des messages à ces rubriques. Un serveur principal Mobile peuvent s’abonner à un ou plusieurs de ces rubriques en créant un abonnement Bus des services. Le serveur principal mobile pour recevoir une notification à partir du système principal correspondant ainsi profiter. Serveur principal mobile continue à recevoir des messages sur leurs abonnements et dès qu’un message arrive, il se transforme en et envoie en tant que notification à son concentrateur de notification. Hubs notification livre ensuite éventuellement le message à l’application mobile. Pour synthétiser les composants clés, nous avons :

1. Serveur principal (systèmes métier/hérité)
    - Crée Service Bus sujet
    - Envoie le Message
2. Serveur principal mobile
    - Crée l’abonnement au Service
    - Message d’erreur (de système d’arrière-plan)
    - Envoie une notification aux clients (via Azure Notification concentrateur)
3. Application mobile
    - Reçoit et afficher une notification

###<a name="benefits"></a>Avantages :

1. Le découplage entre le récepteur (application/service mobile via un concentrateur de Notification) et l’expéditeur (systèmes principaux) permet de systèmes principaux supplémentaires intégrées en apportant les modifications.
2. Ceci permet également le scénario de plusieurs applications mobiles de recevoir des événements à partir d’un ou plusieurs systèmes principaux.  

## <a name="sample"></a>Exemple :

###<a name="prerequisites"></a>Conditions préalables
Vous devez effectuer les didacticiels suivants pour vous familiariser avec les étapes de configuration, ainsi que la création de courantes & concepts :

1. [Programmation Service Bus publication/Sub] - détails de l’utilisation des services Bus rubriques/récurrents, ce qui explique comment créer un espace de noms afin qu’il contienne rubriques/abonnements, comment envoyer et recevoir des messages d’eux.
2. [Notification Hubs - didacticiel Windows universel] - cette rubrique explique comment configurer une application du Windows Store et utiliser Hubs de Notification pour inscrire et recevoir des notifications.

###<a name="sample-code"></a>Exemples de code

L’exemple complet de code est disponible dans les [Exemples de concentrateur de Notification]. Il est divisée en trois composants :

1. **EnterprisePushBackendSystem**

    un. Ce projet utilise le package Nuget *WindowsAzure.ServiceBus* et est basé sur le [Service Bus publication/Sub de programmation].

    b. Il s’agit d’une simple c# console application pour simuler un système métier qui lance le message est remis à l’application mobile.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`permet de créer la rubrique Bus de Service dans lequel nous vous envoie des messages.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`est utilisé pour envoyer les messages à cette rubrique Bus de Service. Nous sommes ici envoyant simplement un ensemble de messages aléatoires à la rubrique régulièrement dans le cadre de l’exemple. En règle générale, il y a un système d’arrière-plan qui vous envoie des messages lorsqu’un événement se produit.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    un. Ce projet utilise les packages *WindowsAzure.ServiceBus* et *Microsoft.Web.WebJobs.Publish* Nuget et est basé sur le [Service Bus publication/Sub de programmation].

    b. Il s’agit d’une autre application de console c# qui nous s’exécutera comme un [WebJob Azure] car il doit s’exécuter en continu pour recevoir des messages à partir des systèmes métier/serveur principal. Il s’agit partie de votre serveur principal Mobile.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`permet de créer un abonnement Bus des services pour la rubrique où le système principal envoie des messages. Selon le scénario d’entreprise, ce composant crée un ou plusieurs abonnements aux rubriques correspondantes (par exemple, certaines peuvent recevoir les messages à partir du système de ressources humaines, d’autres informations d’intérêts système et ainsi de suite)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification est utilisée pour lire le message à partir de la rubrique à l’aide de son abonnement et que si la lecture réussit ensuite une notification (dans l’exemple de scénario une notification toast native Windows) pour être envoyé à l’application mobile à l’aide d’Azure Notification Hubs.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Pour la publication de ce comme un **WebJob**, cliquez avec le bouton droit sur la solution dans Visual Studio et sélectionnez **Publier en tant que WebJob**

    ![][2]

    f. Sélectionnez votre profil de publication et créez un nouveau site Web Azure s’il n’existe déjà qui héberge cette WebJob et une fois que le site Web puis **Publier**.

    ![][3]

    g. Configurer le travail pour être « Exécuter en continu » de sorte que lorsque vous ouvrez une session sur le [Portail classique Azure] vous devriez voir ce qui suit :

    ![][4]


3. **EnterprisePushMobileApp**

    un. Il s’agit d’une application du Windows Store qui recevra toast notifications à partir de la WebJob en cours d’exécution dans le cadre de votre serveur principal Mobile et l’afficher. Ceci est basé sur [Notification Hubs - didacticiel Windows universel].  

    b. Vérifiez que votre application est activée pour recevoir des notifications toast.

    c. Vous assurer que démarrent centraux Notification suivants du code d’inscription est appelé à l’application (après le remplacement de la *HubName* et *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemple :

1. Assurez-vous que votre WebJob s’exécute correctement et doit « Exécuter en permanence ».
2. Exécuter **EnterprisePushMobileApp** qui permet de démarrer l’application Windows Store.
3. Exécutez l’application de console **EnterprisePushBackendSystem** qui va simuler le système principal métier et commence à envoyer des messages et vous devriez voir les notifications toast qui apparaissent comme suit :

    ![][5]

4. Les messages ont été envoyés à l’origine vers les sujets Bus de Service a été contrôlé par abonnements Bus des services dans votre projet Web. Une fois qu’un message a été reçu, une notification a été créée et envoyée à l’application mobile. Vous pouvez consulter les journaux WebJob pour confirmer le traitement lorsque vous passez le lien les journaux [d’Azure classique portail] pour votre projet Web :

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemples de concentrateur de notification]: https://github.com/Azure/azure-notificationhubs-samples
[Service Mobile Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Bus des services Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programmation Service Bus publication/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notification Hubs - didacticiel Windows universel]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portail classique Azure]: https://manage.windowsazure.com/
