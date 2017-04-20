<properties 
    pageTitle="Service Bus demandée didacticiel reste messagerie | Microsoft Azure"
    description="Didacticiel reste messagerie traité."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-rest-tutorial"></a>Bus des services demandé de messagerie reste didacticiel

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce didacticiel montre comment créer une base file d’attente de Bus des services Azure basée sur REST et rubrique/abonnement.

## <a name="create-a-namespace"></a>Créer un espace de noms

La première étape consiste à créer un espace de noms de service et pour obtenir une clé de [Signature de Access partagé](service-bus-sas-overview.md) (sa). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais Bus des services. Une clé de sa est générée automatiquement par le système lors de la création d’un espace de noms de service. La combinaison d’espace de noms de service et sa clé fournit des informations d’identification Bus des services authentifier l’accès à l’application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-console-client"></a>Créer un client de la console

Files d’attente Bus des services permettent de stocker les messages dans un premier, sorti file d’attente. Rubriques et abonnements mise en œuvre d’un modèle de publication/abonnement ; vous créez une rubrique, puis créez une ou plusieurs abonnements associés à ce sujet. Lorsque les messages sont envoyés à la rubrique, ils sont immédiatement envoyées aux abonnés de cette rubrique.

Le code dans ce didacticiel effectue les opérations suivantes.

- Utilise votre espace de noms et la clé de [Signature de Access partagé](service-bus-sas-overview.md) (sa) pour accéder à vos ressources d’espace de noms Bus des services.

- Crée une file d’attente, envoie un message à la file d’attente et lit le message dans la file d’attente.

- Crée un sujet, un abonnement à cette rubrique et envoie et lit le message de l’abonnement.

- Extrait la file d’attente, rubrique et informations sur l’abonnement, y compris les règles d’abonnement, à partir de Bus des services.

- Supprime les ressources file d’attente, rubrique et d’abonnement.

Car le service est un service Web REST style, il n’existe aucun type spécial impliqués, car l’ensemble de l’échange implique chaînes. Cela signifie que le projet Visual Studio ne doit pas référencer les bibliothèques de Bus des services.

Après avoir recueilli l’espace de noms et les informations d’identification dans la première étape, vous créez ensuite une application de console Visual Studio simple.

### <a name="create-a-console-application"></a>Créer une application console

1. Démarrez Visual Studio en tant qu’administrateur en double-cliquant sur le programme dans le menu **Démarrer** , puis cliquez sur **Exécuter en tant qu’administrateur**.

1. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et cliquez sur **Nouveau**, puis cliquez sur **le projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual c#** (si **Visual c#** n’apparaît pas, regardez sous **Autres langages**), sélectionnez le modèle **d’Application Console** et nommez-la **Microsoft.ServiceBus.Samples**. Utilisez l’emplacement par défaut. Cliquez sur **OK** pour créer le projet.

1. Dans Program.cs, vérifiez que votre `using` instructions apparaissent comme suit :

    ```
    using System;
    using System.Globalization;
    using System.IO;
    using System.Net;
    using System.Security.Cryptography;
    using System.Text;
    using System.Xml;
    ```

1. Si nécessaire, renommez l’espace de noms pour le programme à partir de la valeur par défaut de Visual Studio pour `Microsoft.ServiceBus.Samples`.

1. À l’intérieur de la `Program` de classes, ajoutez les variables globales suivantes :
    
    ```
    static string serviceNamespace;
    static string baseAddress;
    static string token;
    const string sbHostName = "servicebus.windows.net";
    ```

1. À l’intérieur de `Main()`, collez le code suivant :

    ```
    Console.Write("Enter your service namespace: ");
    serviceNamespace = Console.ReadLine();
    
    Console.Write("Enter your SAS key: ");
    string SASKey = Console.ReadLine();
    
    baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
    try
    {
        token = GetSASToken("RootManageSharedAccessKey", SASKey);
    
        string queueName = "Queue" + Guid.NewGuid().ToString();
    
        // Create and put a message in the queue
        CreateQueue(queueName, token);
        SendMessage(queueName, "msg1");
        string msg = ReceiveAndDeleteMessage(queueName);
    
        string topicName = "Topic" + Guid.NewGuid().ToString();
        string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
        CreateTopic(topicName);
        CreateSubscription(topicName, subscriptionName);
        SendMessage(topicName, "msg2");
    
        Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
    
        // Get an Atom feed with all the queues in the namespace
        Console.WriteLine(GetResources("$Resources/Queues"));
    
        // Get an Atom feed with all the topics in the namespace
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the subscriptions for the topic we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions"));
    
        // Get an Atom feed with all the rules for the topic and subscription we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
    
        // Delete the queue we created
        DeleteResource(queueName);
    
        // Delete the topic we created
        DeleteResource(topicName);
    
        // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Queues"));
    }
    catch (WebException we)
    {
        using (HttpWebResponse response = we.Response as HttpWebResponse)
        {
            if (response != null)
            {
                Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
            }
            else
            {
                Console.WriteLine(we.ToString());
            }
        }
    }
    
    Console.WriteLine("\nPress ENTER to exit.");
    Console.ReadLine();
    ```

## <a name="create-management-credentials"></a>Créer des informations d’identification de gestion

L’étape suivante consiste à écrire une méthode qui traite de l’espace de noms et la clé de sa que vous entré à l’étape précédente et retourne un jeton associations de sécurité. Cet exemple crée un jeton de sa valide pour une heure.

### <a name="create-a-getsastoken-method"></a>Créer une méthode GetSASToken()

Collez le code suivant après la `Main()` méthode, dans le `Program` cours :

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## <a name="create-the-queue"></a>Créer la file d’attente

L’étape suivante consiste à écrire une méthode qui utilise la commande style REST HTTP placer pour créer une file d’attente.

Collez le code suivant juste après le `GetSASToken()` code que vous avez ajouté à l’étape précédente :

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="send-a-message-to-the-queue"></a>Envoyer un message à la file d’attente

Dans cette étape, vous ajoutez une méthode qui utilise la commande style REST HTTP POST pour envoyer un message à la file d’attente que vous avez créé à l’étape précédente.

1. Collez le code suivant juste après le `CreateQueue()` code que vous avez ajouté à l’étape précédente :

    ```
    // Sends a message to the "queueName" queue, given the name and the value to enqueue
    // Uses an HTTP POST request.
    private static void SendMessage(string queueName, string body)
    {
        string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
        Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
        WebClient webClient = new WebClient();
        webClient.Headers[HttpRequestHeader.Authorization] = token;
    
        webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
    }
    ```

1. Propriétés du message traité standard sont placées dans un `BrokerProperties` en-tête HTTP. Les propriétés intermédiaire doivent être sérialisées au format JSON. Pour spécifier une valeur de **la propriété TimeToLive** de 30 secondes et ajouter une étiquette de message « M1 » au message, ajoutez le code suivant immédiatement avant la `webClient.UploadData()` appeler indiqué dans l’exemple précédent :

    ```
    // Add brokered message properties "TimeToLive" and "Label"
    webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
    ```

    Notez que les propriétés de message traité ont été et seront ajoutées. Par conséquent, la demande d’envoi doit spécifier une version de l’API qui prend en charge toutes les propriétés de message traité qui font partie de la demande. Si la version de l’API spécifiée ne prend pas en charge une propriété message traité, cette propriété est ignorée.

1. Propriétés de message personnalisées sont définies comme un ensemble de paires clé-valeur. Chaque propriété personnalisée est stockée dans sa propre en-tête TPPT. Pour ajouter les propriétés personnalisées « Priorité » et « Client », ajoutez le code suivant immédiatement avant la `webClient.UploadData()` appeler indiqué dans l’exemple précédent :

    ```
    // Add custom properties "Priority" and "Customer".
    webClient.Headers.Add("Priority", "High");
    webClient.Headers.Add("Customer", "12345");
    ```

## <a name="receive-and-delete-a-message-from-the-queue"></a>Recevoir et supprimer un message dans la file d’attente

L’étape suivante consiste à ajouter une méthode qui utilise la commande style REST HTTP DELETE pour recevoir et supprimer un message dans la file d’attente.

Collez le code suivant juste après le `SendMessage()` code que vous avez ajouté à l’étape précédente :

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## <a name="create-a-topic-and-subscription"></a>Créer une rubrique et abonnement

L’étape suivante consiste à écrire une méthode qui utilise la commande style REST HTTP placer pour créer un sujet. Ensuite, vous écrivez une méthode qui crée un abonnement à cette rubrique.

### <a name="create-a-topic"></a>Créer un sujet

Collez le code suivant juste après le `ReceiveAndDeleteMessage()` code que vous avez ajouté à l’étape précédente :

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### <a name="create-a-subscription"></a>Créer un abonnement

Le code suivant crée un abonnement à la rubrique que vous avez créé à l’étape précédente. Ajoutez le code suivant directement après la `CreateTopic()` définition :

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="retrieve-message-resources"></a>Récupérer des ressources de message

Dans cette étape, vous ajoutez code qui Récupère les propriétés du message, puis supprime les ressources de messagerie que vous avez créé lors des étapes précédentes.

### <a name="retrieve-an-atom-feed-with-the-specified-resources"></a>Récupérer un Atom flux avec les ressources spécifiées

Ajoutez le code suivant directement après la `CreateSubscription()` méthode vous avez ajouté à l’étape précédente :

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### <a name="delete-messaging-entities"></a>Supprimer des entités de messagerie

Juste après le code que vous avez ajouté à l’étape précédente, ajoutez le code suivant :

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### <a name="format-the-atom-feed"></a>Mettre en forme le flux Atom

La `GetResources()` méthode contient un appel à un `FormatXml()` méthode qui reformate la Atom récupérée flux pour être plus lisible. Voici la définition de `FormatXml()`; Ajoutez le code suivant directement après la `DeleteResource()` code que vous avez ajouté dans la section précédente :

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## <a name="build-and-run-the-application"></a>Créer et exécuter l’application

Vous pouvez désormais créer et exécuter l’application. Dans le menu **Générer** dans Visual Studio, cliquez sur **Générer la Solution**, ou appuyez sur **Ctrl + Maj + B**.

### <a name="run-the-application"></a>Exécuter l’application

S’il n’existe aucune erreur, appuyez sur F5 pour exécuter l’application. Lorsque vous y êtes invité, entrez votre espace de noms, nom de la clé associations de sécurité et sa valeur de clé que vous avez obtenu à l’étape 1.

### <a name="example"></a>Exemple

L’exemple suivant est l’intégralité du code, tel qu’il doit apparaître après avoir suivi les différentes étapes de ce didacticiel.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour en savoir plus :

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Principes de base de Bus des services Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Didacticiel de reste relais Bus des services](../service-bus-relay/service-bus-relay-rest-tutorial.md)

