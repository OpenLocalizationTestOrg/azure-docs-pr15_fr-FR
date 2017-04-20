<properties
    pageTitle="Télécharger des fichiers à partir d’appareils utilisant un concentrateur IoT | Microsoft Azure"
    description="Suivez ce didacticiel pour savoir comment télécharger des fichiers à partir d’appareils à l’aide d’Azure IoT concentrateur avec c#."
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
     ms.date="06/21/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Didacticiel : Comment télécharger des fichiers à partir d’appareils dans le cloud avec concentrateur IoT

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui permet aux fiable et sécuriser les communications bidirectionnelle entre millions d’appareils IoT et une application back-end. Didacticiels précédents ([prise en main IoT concentrateur] et [Envoyer des messages de Cloud vers le périphérique avec concentrateur IoT]) illustrent le périphérique à nuage base et les fonctionnalités de messagerie cloud vers le périphérique de concentrateur IoT et le didacticiel [messages processus appareil-nuage] décrit permet de stocker fiable appareil-nuage messages dans le stockage blob Azure. Toutefois, dans certains scénarios vous ne pouvez pas facilement mapper des données que vos appareils envoyer dans les messages relativement petits appareil-nuage acceptant IoT concentrateur. Les exemples sont des fichiers volumineux qui contiennent des images, des vidéos, des données vibrations échantillonnées à une fréquence élevée, ou qui contiennent une forme de données prétraitées. Ces fichiers sont généralement traitées dans le cloud à l’aide d’outils tels que [Azure Data Factory] ou la pile [Hadoop] par lots. Quand les téléchargements de fichiers à partir d’un périphérique préférés à l’envoi d’événements, vous pouvez toujours utiliser la fonctionnalité de sécurité et de fiabilité IoT concentrateur.

Ce didacticiel s’appuie sur le code dans le didacticiel sur [Envoyer des messages de Cloud vers le périphérique avec IoT concentrateur] pour vous montrer comment utiliser les fonctionnalités de téléchargement de fichier de IoT concentrateur. Il vous montre comment fournir un appareil de façon sécurisée avec un Azure blob URI pour le téléchargement d’un fichier et comment utiliser les notifications de téléchargement de fichier IoT concentrateur pour déclencher le traitement du fichier dans votre principal de l’application.

À la fin de ce didacticiel, vous exécutez deux applications de console Windows :

* **SimulatedDevice**, une version modifiée de l’application créée dans le didacticiel [Envoyer des messages de Cloud vers le périphérique avec IoT concentrateur] qui télécharge un fichier de stockage à l’aide d’un URI SAS fourni par votre concentrateur IoT.
* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichier à partir de votre plateforme IoT.

> [AZURE.NOTE] Concentrateur IoT prend en charge plusieurs plateformes d’appareil et langues (y compris C, Java et Javascript) via Azure IoT périphérique SDK. Reportez-vous au [Centre de développement IoT Azure] pour obtenir des instructions étape par étape comment connecter votre périphérique au code indiqué dans ce didacticiel et généralement à Azure IoT concentrateur.

Pour suivre ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015,

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associer un compte de stockage Azure à IoT concentrateur

Étant donné que le périphérique simulé télécharge un fichier dans un blob, vous devez avoir un compte de [Stockage Azure] associé à IoT concentrateur. Lorsque vous associez un compte de stockage Azure à un concentrateur IoT, le hub peut générer un URI SAS qu’un périphérique peut utiliser en toute sécurité télécharger un fichier à un conteneur blob. Le service IoT concentrateur et le SDK appareil coordonne le processus qui génère SAS URI et rend disponible pour un appareil à utiliser pour télécharger un fichier.

Suivez les instructions de [téléchargements de fichiers de configurer à l’aide du portail Azure] [ lnk-configure-upload] pour associer un compte de stockage Azure à votre concentrateur IoT.

## <a name="upload-a-file-from-a-simulated-device"></a>Télécharger un fichier à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application périphérique simulé créée à [Envoyer des messages de Cloud vers le périphérique avec IoT concentrateur] pour recevoir des messages de cloud vers le périphérique à partir du hub IoT.

1. Dans Visual Studio, droit sur le projet **SimulatedDevice** , cliquez sur **Ajouter**, puis cliquez sur **Un élément existant**. Naviguez jusqu’au fichier d’image et l’inclure dans votre projet. Ce didacticiel suppose que l’image est nommé `image.jpg`.

2. Avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Vérifiez que le **Copier dans le répertoire de sortie** est défini sur **toujours copier**.

    ![][1]

3. Dans le fichier **Program.cs** , ajoutez les instructions suivantes en haut du fichier :

        using System.IO;

4. Ajoutez la méthode suivante à la classe **Program** :
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    La `UploadToBlobAsync` méthode crée dans la source de nom et le flux de fichier du fichier à télécharger et gère le téléchargement vers le stockage. L’application console affiche le temps que nécessaire à télécharger le fichier.

5. Ajoutez la méthode suivante dans la méthode **Main** , juste avant le `Console.ReadLine()` ligne :

        SendToBlobAsync();

> [AZURE.NOTE] Pour facilité plus de, ce didacticiel ne pas implémenter n’importe quelle stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous écrivez une application de console Windows recevant des messages de notification de téléchargement de fichier à partir de IoT concentrateur.

1. Dans la solution Visual Studio actuelle, créer un projet Visual c# Windows à l’aide du modèle de projet **Application Console** . Nommez le projet **ReadFileUploadNotification**.

    ![Nouveau projet dans Visual Studio][2]

2. Dans l’Explorateur, droit sur le projet **ReadFileUploadNotification** , puis cliquez sur **Gérer les Packages NuGet**.

    Cela permet d’afficher la fenêtre Gérer les Packages NuGet.

2. Recherchez `Microsoft.Azure.Devices`, cliquez sur **l’installation**et acceptez les conditions d’utilisation. 

    Cette téléchargements, installations et ajoute une référence à [Azure IoT - package Service SDK NuGet] dans le projet **ReadFileUploadNotification** .

3. Dans le fichier **Program.cs** , ajoutez les instructions suivantes en haut du fichier :

        using Microsoft.Azure.Devices;

4. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT concentrateur de [prise en main IoT concentrateur]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Ajoutez la méthode suivante à la classe **Program** :
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();

            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();

                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }

    Notez que le modèle de réception est le même que celui utilisé pour recevoir des messages de cloud vers le périphérique de l’application d’appareil.

6. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, avec le bouton droit de votre solution, puis sélectionnez **définir au démarrage de projets**. Sélectionnez **plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ReadFileUploadNotification** et **SimulatedDevice**.

2. Appuyez sur **F5**. Les deux applications doivent commencer. Vous devriez voir le téléchargement terminé dans une application de console et le message de notification de téléchargement en cours de réception par l’autre application console. Vous pouvez utiliser le [portail Azure] ou l’Explorateur de serveurs Visual Studio pour vérifier la présence du fichier téléchargé dans votre compte de stockage Azure.

  ![][50]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment exploiter les fonctionnalités de téléchargement de fichier du concentrateur IoT pour simplifier les téléchargements de fichiers à partir d’appareils. Vous pouvez continuer à Explorer IoT concentrateur fonctionnalités et des scénarios avec les articles suivants :

- [Créer un concentrateur IoT par programme][lnk-create-hub]
- [Introduction au SDK C][lnk-c-sdk]
- [Concentrateur IoT SDK][lnk-sdks]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portail Azure]: https://portal.azure.com/

[Données Azure usine]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Envoyer des messages de Cloud vers le périphérique avec concentrateur IoT]: iot-hub-csharp-csharp-c2d.md
[Traiter les messages appareil-nuage]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main IoT concentrateur]: iot-hub-csharp-csharp-getstarted.md
[Centre de développement IoT Azure]: http://www.azure.com/develop/iot

[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT - package Service SDK NuGet]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md


