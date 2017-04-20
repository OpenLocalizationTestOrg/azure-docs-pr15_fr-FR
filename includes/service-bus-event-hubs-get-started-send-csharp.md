## <a name="send-messages-to-event-hubs"></a>Envoyer des messages à Hubs d’événement

Dans cette section, vous allez écrire une application de console Windows qui envoie des événements à votre plateforme d’événement.

1. Dans Visual Studio, créez un nouveau projet application de bureau Visual c# à l’aide du modèle de projet **Application Console** . Nommez le projet **expéditeur**.

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp1.png)

2. Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour la Solution**. 

3. Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Vérifiez que le nom du projet (**expéditeur**) est spécifié dans la zone **ou les versions** . Cliquez sur **l’installation**et acceptez les conditions d’utilisation. 

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp2.png)

    Visual Studio télécharge, installations et ajoute une référence dans le [package de NuGet Bus des services Azure bibliothèque](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Ajoutez les champs suivants à la classe **Program** , en remplaçant les valeurs d’espace réservé par le nom du concentrateur de l’événement que vous avez créé dans la section précédente et la chaîne de connexion au niveau de l’espace de noms que vous avez précédemment enregistrés.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Ajoutez la méthode suivante à la classe **Program** :

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Cette méthode envoie en continu les événements à votre plateforme d’événement avec un délai de 200 ms.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
