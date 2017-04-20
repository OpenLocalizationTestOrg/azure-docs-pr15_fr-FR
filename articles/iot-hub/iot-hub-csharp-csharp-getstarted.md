<properties
    pageTitle="Azure IoT concentrateur pour la prise en main c# | Microsoft Azure"
    description="Concentrateur IoT Azure avec c# prise en main didacticiel. Utilisez Azure IoT concentrateur et c# avec les kits de développement Microsoft Azure IoT à mettre en œuvre une solution Internet des objets."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Prise en main Azure IoT concentrateur pour .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications console Windows :

* **CreateDeviceIdentity**, qui crée une identité de l’appareil et une clé de sécurité associées pour connecter votre périphérique simulé.
* **ReadDeviceToCloudMessages**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **SimulatedDevice**, qui se connecte à votre concentrateur IoT avec l’identité de l’appareil créée précédemment et envoie un message de télémétrie par seconde en utilisant le protocole AMQP.

> [AZURE.NOTE] Pour plus d’informations sur les différents kits de développement logiciel que vous pouvez utiliser pour créer les deux applications à s’exécuter sur des appareils mobiles et votre solution back-end, voir [IoT concentrateur SDK][lnk-hub-sdks].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Vous venez de créer votre concentrateur IoT, et vous avez la chaîne de connexion et le nom d’hôte dont vous avez besoin pour effectuer le reste de ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de l’appareil

Dans cette section, vous créez une application de console Windows qui crée une identité de périphérique dans le Registre identité dans votre hub IoT. Un périphérique ne peut pas se connecter à IoT concentrateur, à moins qu’une entrée dans le Registre identité appareil. Pour plus d’informations, voir la section « Registre identité de périphérique » du [Guide du développeur IoT concentrateur][lnk-devguide-identity]. Lorsque vous exécutez cette application console, il génère un ID d’appareil unique et une clé que votre appareil peut utiliser pour identifier lorsqu’il envoie des messages de l’appareil-nuage à IoT concentrateur.

1. Dans Visual Studio, ajoutez un projet Visual c# bureau classique de Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Assurez-vous que la version .NET Framework est 4.5.1 ou ultérieur. Nommez le projet **CreateDeviceIdentity**.

    ![Nouveau projet Visual c# bureau classique de Windows][10]

2. Dans l’Explorateur, droit sur le projet **CreateDeviceIdentity** , puis cliquez sur **Gérer les Packages Nuget**.

3. Dans la fenêtre **Gestionnaire de Package Nuget** , sélectionnez **Parcourir**, recherchez **microsoft.azure.devices**, sélectionnez **installer** pour installer le package **Microsoft.Azure.Devices** et acceptez les conditions d’utilisation. Cette procédure téléchargements, installations et ajoute une référence dans le [Kit de développement logiciel Microsoft Azure IoT Service] [ lnk-nuget-service-sdk] Nuget package et ses dépendances.

    ![Fenêtre du Gestionnaire de Package NuGet][11]

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le hub IoT que vous avez créé dans la section précédente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Cette méthode crée une identité appareil avec ID **myFirstDevice**. (Si ce code appareil existe déjà dans le Registre, le code extrait simplement les informations de périphérique existant.) L’application affiche ensuite la clé primaire pour cette identité. Vous utilisez cette clé dans le périphérique simulé pour vous connecter à votre plateforme IoT.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Exécuter cette application, puis prenez note de la clé du périphérique.

    ![Clé de périphérique générée par l’application][12]

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur stocke uniquement des identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de l’équipement et clés à utiliser en tant que les informations d’identification de sécurité et un indicateur activé ou désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique individuel. Si votre application a besoin stocker les autres métadonnées spécifiques à l’appareil, elle doit utiliser un magasin spécifique à l’application. Pour plus d’informations, voir le [Guide du développeur IoT concentrateur][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de l’appareil-nuage

Dans cette section, vous créez une application de console Windows qui lit les messages de l’appareil-nuage de IoT concentrateur. Un concentrateur IoT expose un [Azure événement Hubs][lnk-event-hubs-overview]-point de terminaison compatible pour pouvoir lire les messages de l’appareil-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas adapté à un déploiement haut débit. Pour savoir comment traiter les messages appareil-nuage à l’échelle, voir le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel. Pour plus d’informations sur la façon de traiter les messages à partir de l’événement Hubs, voir la [Prise en main événement Hubs] [ lnk-eventhubs-tutorial] didacticiel. (Ce didacticiel s’applique aux points de terminaison IoT concentrateur événement concentrateur compatible).

> [AZURE.NOTE] Le point de terminaison compatible événement concentrateur pour la lecture des messages de l’appareil-nuage toujours utilise le protocole AMQP.

1. Dans Visual Studio, ajoutez un projet Visual c# bureau classique de Windows à la solution actuelle, en utilisant le modèle de projet **Application Console** . Assurez-vous que la version .NET Framework est 4.5.1 ou ultérieur. Nommez le projet **ReadDeviceToCloudMessages**.

    ![Nouveau projet Visual c# bureau classique de Windows][10]

2. Dans l’Explorateur, droit sur le projet **ReadDeviceToCloudMessages** , puis cliquez sur **Gérer les Packages Nuget**.

3. Dans la fenêtre **Gestionnaire de Package Nuget** , recherchez **WindowsAzure.ServiceBus**, sélectionnez **installer**et acceptez les conditions d’utilisation. Cette procédure téléchargements, installations et ajoute une référence à [Bus des services Azure][lnk-servicebus-nuget], avec toutes ses dépendances. Ce package permet à l’application pour vous connecter au point de terminaison événement concentrateur compatible avec votre concentrateur IoT.

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le hub IoT que vous avez créé dans la section « Créer un concentrateur IoT ».

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Cette méthode utilise une instance de **EventHubReceiver** pour recevoir des messages à partir de toutes le IoT concentrateur appareil-nuage recevoir partitions. Avez-vous remarqué la façon dont vous passez un `DateTime.Now` paramètre lorsque vous créez l’objet **EventHubReceiver** , afin qu’elle reçoit uniquement les messages envoyés après son démarrage. Ce filtre est utile dans un environnement de test afin que vous puissiez voir l’ensemble actuel des messages. Dans un environnement de production votre code devez vous assurer qu’il traite tous les messages. Pour plus d’informations, voir [comment traiter les messages d’appareil-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiel.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application de console Windows qui simule un appareil qui envoie des messages de l’appareil-nuage à un concentrateur IoT.

1. Dans Visual Studio, ajoutez un projet Visual c# bureau classique de Windows à la solution actuelle, en utilisant le modèle de projet **Application Console** . Assurez-vous que la version .NET Framework est 4.5.1 ou ultérieur. Nommez le projet **SimulatedDevice**.

    ![Nouveau projet Visual c# bureau classique de Windows][10]

2. Dans l’Explorateur, droit sur le projet **SimulatedDevice** , puis cliquez sur **Gérer les Packages Nuget**.

3. Dans la fenêtre **Gestionnaire de Package Nuget** , sélectionnez **Parcourir**, recherchez **Microsoft.Azure.Devices.Client**, sélectionnez **installer** pour installer le package **Microsoft.Azure.Devices.Client** et acceptez les conditions d’utilisation. Cette procédure téléchargements, installations et ajoute une référence à [Azure IoT - package appareil SDK Nuget] [ lnk-device-nuget] et ses dépendances.

4. Ajoutez le code suivant `using` instruction en haut du fichier **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Ajoutez les champs suivants à la classe **Program** . Remplacer les valeurs d’espace réservé avec le nom d’hôte du concentrateur IoT récupérée dans la section « Créer un concentrateur IoT », et la clé du périphérique extraites dans la section « Créer une identité appareil ».

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Cette méthode envoie un nouveau message appareil-nuage toutes les secondes. Le message contient un objet JSON numéro de série, avec l’ID de périphérique et un numéro généré aléatoirement pour simuler un capteur de vitesse vent.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Par défaut, la méthode **Create** crée une instance de **DeviceClient** qui utilise le protocole AMQP pour communiquer avec IoT concentrateur. Pour utiliser le protocole HTTP, utilisez la substitution de la méthode **Create** qui vous permet de spécifier le protocole. Si vous utilisez le protocole HTTP, vous devez également ajouter le package Nuget **Microsoft.AspNet.WebApi.Client** à votre projet pour inclure l’espace de noms **System.Net.Http.Formatting** .

Ce didacticiel passe en revue les étapes de création d’un client d’appareil IoT concentrateur. Vous pouvez également utiliser le [Service connecté pour Azure IoT concentrateur] [ lnk-connected-service] extension Visual Studio pour ajouter le code nécessaire à votre application cliente appareil.


> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Dans Visual Studio, dans l’Explorateur de solutions, avec le bouton droit de votre solution, puis cliquez sur **définir au démarrage de projets**. Sélectionnez **plusieurs projets de démarrage**, puis **Démarrer** en tant que l’action pour les projets à la fois le **ReadDeviceToCloudMessages** et **SimulatedDevice** .

    ![Propriétés de projet de démarrage][41]

2.  Appuyez sur **F5** pour démarrer les deux applications en cours d’exécution. Le résultat de la console de l’application **SimulatedDevice** affiche tous les messages qu'envoie votre périphérique simulé à votre concentrateur IoT. Le résultat de la console de l’application **ReadDeviceToCloudMessages** affiche tous les messages qui reçoit votre concentrateur IoT.

    ![Sortie de la console à partir des applications][42]

3. La vignette de **l’utilisation** du [portail Azure] [ lnk-portal] affiche le nombre de messages envoyés au concentrateur :

    ![Vignette de l’utilisation du portail Azure][43]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un concentrateur IoT dans le portail Azure et puis créée une identité appareil dans identité Registre son. Vous avez utilisé cette identité appareil pour activer l’application périphérique simulé envoyer des messages appareil-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le hub. 

Pour continuer la prise en main IoT concentrateur et Explorer d’autres scénarios IoT, voir :

- [Connecter votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des appareils][lnk-device-management]
- [Prise en main du Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour découvrir comment étendre votre solution IoT et traiter des messages de périphérique-nuage à l’échelle, consultez le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
