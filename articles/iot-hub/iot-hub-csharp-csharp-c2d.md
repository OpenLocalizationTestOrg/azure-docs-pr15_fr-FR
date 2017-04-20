<properties
    pageTitle="Envoyer des messages de cloud vers le périphérique avec concentrateur IoT | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages cloud vers le périphérique à l’aide d’Azure IoT concentrateur avec c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Didacticiel : Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT et .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui vous aide à activer la communication bidirectionnelle fiable et sécurisée entre millions d’appareils IoT et mettre fin à une application précédent. Le didacticiel [prise en main IoT concentrateur] montre comment créer un concentrateur IoT, mise en service d’une identité appareil qu’il contient et un périphérique simulé qui envoie des messages de l’appareil-nuage de code.

Ce didacticiel s’appuie sur la [prise en main IoT concentrateur]. Il vous explique comment procéder pour :

- À partir de votre application cloud principale, envoyer des messages de cloud vers le périphérique à un seul périphérique via IoT concentrateur.
- Recevoir des messages de cloud vers le périphérique sur un appareil.
- À partir de votre cloud application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un appareil IoT concentrateur.

Vous pouvez trouver plus d’informations sur les messages de cloud vers le périphérique dans le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous allez exécuter les deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans [prise en main IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit des messages cloud vers le périphérique.
* **SendCloudToDevice**, qui envoie un message cloud vers le périphérique à l’appareil simulé via IoT concentrateur et puis reçoit son accusé de réception.

> [AZURE.NOTE] Concentrateur IoT prend en charge SDK pour les nombreuses langues (y compris C, Java et Javascript) via Azure IoT appareil SDK et les plateformes d’appareil. Pour obtenir des instructions détaillées sur la connexion de votre appareil au code de ce didacticiel et généralement à Azure IoT concentrateur, voir le [Centre de développement IoT Azure].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous pouvez modifier l’application périphérique simulé vous avez créé dans [prise en main IoT concentrateur] pour recevoir des messages de cloud vers le périphérique à partir du hub IoT.

1. Dans Visual Studio, dans le projet **SimulatedDevice** , ajoutez la méthode suivante à la classe **Program** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    La `ReceiveAsync` méthode asynchrone retourne le message reçu au moment où il est reçu par le périphérique. Elle retourne la valeur *null* après un délai pouvant être précisés (dans ce cas, la valeur par défaut d’une minute est utilisé). Dans ce cas, le code doit continuer en attente pour les nouveaux messages. Il s’agit de la raison pour laquelle la `if (receivedMessage == null) continue` ligne.

    L’appel vers `CompleteAsync()` vous avertit IoT concentrateur que le message a été correctement traité. Le message peut être supprimé en toute sécurité de la file d’attente. Si quelque chose s’est produit qui a empêché l’application d’appareil terminer le traitement du message, concentrateur IoT il propose à nouveau. Il est important que logique de traitement de message dans l’application d’appareil *idempotent*, puis afin que reçoivent le même message plusieurs fois génère le même résultat. Une application peut également temporairement abandon d’un message, qui se traduit par concentrateur IoT en conservant le message dans la file d’attente de consommation future. Ou bien, l’application peut rejeter un message, qui supprime définitivement le message dans la file d’attente. Pour plus d’informations sur la politique de message cloud vers le périphérique, consultez le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Lorsque vous utilisez HTTP au lieu de MQTT ou AMQP comme transport, la `ReceiveAsync` méthode retourne immédiatement. Le modèle pris en charge pour les messages de cloud vers le périphérique commence par HTTP est appareils connectés de façon intermittente qui recherchent des messages rarement (moins de 25 minutes). Émission HTTP plus reçoit les résultats dans IoT Hub la limitation de requêtes. Pour plus d’informations sur les différences entre prise en charge MQTT, AMQP et HTTP et concentrateur IoT la limitation, voir le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

2. Ajoutez la méthode suivante dans la méthode **Main** , juste avant le `Console.ReadLine()` ligne :

        ReceiveC2dAsync();

> [AZURE.NOTE] Pour facilité plus de, ce didacticiel ne pas implémenter n’importe quelle stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message cloud vers le périphérique

Dans cette section, vous allez écrire une application de console Windows qui envoie des messages de cloud vers le périphérique à l’application de périphérique simulé.

1. Dans la solution Visual Studio actuelle, créer un projet Visual c# Desktop App à l’aide du modèle de projet **Application Console** . Nommez le projet **SendCloudToDevice**.

    ![Nouveau projet dans Visual Studio][20]

2. Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour Solution...**. 

    Cette action ouvre la fenêtre **Gérer les Packages NuGet** .

3. Recherchez `Microsoft Azure Devices`, cliquez sur **l’installation**et acceptez les conditions d’utilisation. 

    Cette téléchargements, installations et ajoute une référence à [Azure IoT - package Service SDK NuGet].

4. Ajoutez le code suivant `using` instruction en haut du fichier **Program.cs** :

        using Microsoft.Azure.Devices;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT concentrateur de [prise en main IoT concentrateur]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Cette méthode envoie un nouveau message cloud vers le périphérique à l’appareil de l’ID `myFirstDevice`. Modifier ce paramètre en conséquence, au cas où que vous avez modifiée de celui utilisé dans [prise en main IoT concentrateur].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. À partir de Visual Studio, avec le bouton droit de votre solution, puis sélectionnez **définir au démarrage de projets...**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ProcessDeviceToCloudMessages**, **SimulatedDevice**et **SendCloudToDevice**.

9.  Appuyez sur **F5**. Les trois applications doivent commencer. Sélectionnez les fenêtres **SendCloudToDevice** , puis appuyez sur **entrée**. Vous devriez voir le message reçu par l’application simulée.

    ![Message de réception de l’application][21]

## <a name="receive-delivery-feedback"></a>Recevoir des commentaires de remise
Il est possible d’accusés de remise (ou d’expiration) demande IoT concentrateur pour chaque message cloud vers le périphérique. Cela permet le niveau principal cloud informer facilement une logique de nouvelle tentative ou de rémunération. Pour plus d’informations sur le cloud vers le périphérique commentaires, voir le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

Dans cette section, vous allez modifier l’application **SendCloudToDevice** pour obtenir des commentaires et recevez IoT concentrateur.

1. Dans Visual Studio, dans le projet **SendCloudToDevice** , ajoutez la méthode suivante à la classe **Program** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir des messages de cloud vers le périphérique de l’application d’appareil.

2. Ajoutez la méthode suivante dans la méthode **Main** , juste après le `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` ligne :

        ReceiveFeedbackAsync();

3. Pour obtenir des commentaires pour la remise de votre message cloud vers le périphérique, vous devez spécifier une propriété dans la méthode **SendCloudToDeviceMessageAsync** . Ajoutez la ligne suivante, juste après le `var commandMessage = new Message(...);` ligne :

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Exécuter les applications en appuyant sur **F5**. Vous devriez voir les trois applications Démarrer. Sélectionnez les fenêtres **SendCloudToDevice** , puis appuyez sur **entrée**. Vous devriez voir du message reçu par l’application simulée et au bout de quelques secondes, le message de commentaires reçu par votre application **SendCloudToDevice** .

    ![Message de réception de l’application][22]

> [AZURE.NOTE] Pour facilité plus de, ce didacticiel ne pas implémenter n’importe quelle stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages de cloud vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT concentrateur, voir [Azure IoT Suite].

Pour plus d’informations sur le développement de solutions avec concentrateur IoT, consultez le [Guide du développeur IoT concentrateur].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - package Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Prise en main IoT concentrateur]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement IoT Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Suite IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/