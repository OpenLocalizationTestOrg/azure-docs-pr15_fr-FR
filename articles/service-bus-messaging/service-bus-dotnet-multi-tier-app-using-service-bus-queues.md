<properties
    pageTitle="Application à plusieurs niveaux .NET | Microsoft Azure"
    description="Un didacticiel .NET qui vous permet de développer une application à plusieurs niveaux dans Azure qui utilise des files d’attente Bus des services pour communiquer entre les niveaux."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Application d’à plusieurs niveaux .NET à l’aide de files d’attente Bus des services Azure

## <a name="introduction"></a>Introduction

Il est facile à l’aide de Visual Studio et le Kit de développement Azure gratuit pour .NET de développement pour Microsoft Azure. Ce didacticiel vous explique comment procéder pour créer une application qui utilise plusieurs ressources Azure en cours d’exécution dans votre environnement local. Les étapes ne présupposent qu’aucune expérience préalable à l’aide d’Azure.

Vous allez apprendre les éléments suivants :

-   Comment permettre à votre ordinateur pour le développement Azure avec un seul téléchargement et installation.
-   Découvrez comment utiliser Visual Studio pour développer pour Azure.
-   Comment créer une application à plusieurs niveaux dans Azure à l’aide de rôles web et collaborateur.
-   Comment communiquer entre les différents niveaux à l’aide de files d’attente Bus des services.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

Dans ce didacticiel, vous allez créer et exécuter l’application à plusieurs niveaux dans un service cloud Azure. Le serveur frontal sera un rôle web ASP.NET MVC et le serveur principal sera un rôle de travail qui utilise une file d’attente Bus des services. Vous pouvez créer la même application à plusieurs niveaux avec le serveur frontal comme un projet web qui est déployé sur un site Web Azure au lieu d’un service cloud. Pour obtenir des instructions sur la procédure à suivre différemment sur un serveur frontal de site Web Azure, consultez la section [étapes suivantes](#nextsteps) . Vous pouvez également essayer le didacticiel [application cloud/le locaux hybride .NET](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

La capture d’écran suivante montre l’application terminée.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Vue d’ensemble du scénario : communication entre les rôles entre

Pour envoyer un ordre de traitement, le composant de l’interface utilisateur frontal, en cours d’exécution dans le rôle web, doit interagir avec la logique intermédiaire en cours d’exécution dans le rôle de travail. Cet exemple utilise Bus des services dans la messagerie pour la communication entre les niveaux.

Utilisation de la messagerie traité entre les niveaux du milieu et web sépare les deux composants. Contrairement à la messagerie directe (autrement dit, TCP ou HTTP), la couche web ne se connecte pas au niveau intermédiaire directement ; il pousse à la place les unités de travail, sous forme de messages, en Bus des services, qui les conserve fiable jusqu'à ce que le niveau intermédiaire est prêt à utiliser et à les traiter.

Service Bus fournit deux entités pour prendre en charge la messagerie traité : files d’attente et rubriques. Des files d’attente, chaque message envoyé à la file d’attente est utilisé par un seul destinataire. Rubriques prend en charge le modèle de publication et d’abonnement dans lequel chaque message publié est rendu disponible pour un abonnement qui utilisent la rubrique. Chaque abonnement conserve logiquement sa propre file d’attente de messages. Abonnements peuvent également être configurés avec des règles de filtrage restreint l’ensemble des messages passé à la file d’attente d’abonnement à ceux qui correspondent au filtre. L’exemple suivant utilise des files d’attente Bus des services.

![][1]

Ce mécanisme de communication comporte plusieurs avantages par rapport à la messagerie directe :

-   **Découplage temporel.** Avec le modèle de messagerie asynchrone, producteur et les consommateurs ne sont pas nécessairement en ligne en même temps. Service Bus fiable stocke les messages jusqu'à ce que la partie (consommateur) est prête à recevoir les. Cela active les composants de l’application distribuée soit déconnecté, soit volontaire, par exemple, pour la maintenance, ou en raison d’un blocage composant, sans ayant un impact sur l’ensemble du système. En outre, l’application consommateur devrez uniquement à venir en ligne à certaines heures de la journée.

-   **Charger un audit.** Dans de nombreuses applications, la charge du système varie au fil du temps, alors que le temps de traitement requis pour chaque unité de travail est généralement constant. Echanger producteur de message et les consommateurs avec une file d’attente signifie que l’application consommateur (collaborateur) ne doit être configuré pour prendre en charge la charge moyenne plutôt que de charge pointe. La profondeur de la file d’attente s’agrandit et contrats comme varie en fonction de la charge entrante. Cela directement économise de l’argent en termes d’infrastructure requise pour la charge de l’application de service.

-   **Équilibrage de charge.** Que la charge augmente, plus les processus de travail peuvent être ajoutées à lire à partir de la file d’attente. Chaque message est traité par l’un des processus de travail. Par ailleurs, cette extraction d’équilibrage de charge permet une utilisation optimale des ordinateurs collaborateur même si les ordinateurs de travail diffèrent, en termes de puissance de traitement, comme elles extrait les messages à leur propre vitesse maximale. Ce modèle est souvent appelé le modèle *en patinage grand public* .

    ![][2]

Les sections suivantes décrivent le code qui met en œuvre cette architecture.

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Avant de pouvoir commencer le développement d’applications Azure, utilisez les outils et configurer votre environnement de développement.

1.  Installez le Kit de développement Azure pour .NET à [obtenir des outils et Kit de développement logiciel][].

2.  Cliquez sur **installer le Kit de développement** pour la version de Visual Studio que vous utilisez. Les étapes décrites dans ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou enregistrer le programme d’installation, cliquez sur **exécuter**.

5.  Dans **Programme d’installation de la plateforme Web**, cliquez sur **l’installation** et poursuivre l’installation.

6.  Une fois l’installation terminée, vous aurez accès à tous les éléments nécessaires pour commencer à développer l’application. Le Kit de développement inclut des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si vous n’avez pas installé Visual Studio, le Kit de développement installe également la version gratuite Express Visual Studio.

## <a name="create-a-namespace"></a>Créer un espace de noms

L’étape suivante consiste à créer un espace de noms de service et obtenir une clé de Signature accès partagé (sa). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais Bus des services. Une clé de sa est générée par le système lors de la création d’un espace de noms. La combinaison d’espace de noms et sa clé fournit les informations d’identification Bus des services authentifier l’accès à l’application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Créer un rôle web

Dans cette section, vous créez frontale de votre application. Tout d’abord, vous créez les pages qui affiche votre application.
Ensuite, ajoutez le code qui envoie des éléments à une file d’attente Bus des services et affiche les informations d’état sur la file d’attente.

### <a name="create-the-project"></a>Créer le projet

1.  En utilisant des privilèges d’administrateur, démarrez Microsoft Visual Studio. Pour démarrer Visual Studio en tant qu’administrateur, cliquez sur l’icône du programme **Visual Studio** , puis cliquez sur **Exécuter en tant qu’administrateur**. L’émulateur cluster Azure, décrite plus loin dans cet article, nécessite que Visual Studio être démarré en tant qu’administrateur.

    Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

2.  À partir de **Modèles installés**, sous **Visual c#**, cliquez sur le **Cloud** , puis sur **Service Cloud Azure**. Nommez le projet **MultiTierApp**. Cliquez ensuite sur **OK**.

    ![][9]

3.  À partir des rôles **.NET Framework 4.5** , double-cliquez sur **Rôle Web ASP.NET**.

    ![][10]

4.  Pointez sur **WebRole1** sous **solution Azure Cloud Service**, cliquez sur l’icône de crayon et renommez le rôle web **FrontendWebRole**. Cliquez ensuite sur **OK**. (Assurez-vous que vous entrez « Frontend » avec une minuscule « e, » pas « FrontEnd »).

    ![][11]

5.  Dans la boîte de dialogue **Nouveau projet ASP.NET** , dans la liste **Sélectionner un modèle** , cliquez sur **MVC**.

    ![][12]

6. Toujours dans la boîte de dialogue **Nouveau projet ASP.NET** , cliquez sur le bouton **Modifier l’authentification** . Dans la boîte de dialogue **Modifier l’authentification** , cliquez sur **Aucune authentification**, puis cliquez sur **OK**. Pour ce didacticiel, vous déployez une application qui ne doit pas une connexion de l’utilisateur.

    ![][16]

7. Dans la boîte de dialogue **Nouveau projet ASP.NET** , cliquez sur **OK** pour créer le projet.

6.  Dans l' **Explorateur de solutions**, dans le projet **FrontendWebRole** , cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.

7.  Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![][13]

    Notez que les assemblys de client requis sont désormais référencés et des nouveaux fichiers de code ont été ajoutées.

9.  Dans l' **Explorateur de solutions**, avec le bouton droit de **modèles** et cliquez sur **Ajouter**, puis cliquez sur **cours**. Dans la zone **nom** , tapez le nom **OnlineOrder.cs**. Puis cliquez sur **Ajouter**.

### <a name="write-the-code-for-your-web-role"></a>Écrire du code pour votre rôle web

Dans cette section, vous créez les différentes pages qui affiche votre application.

1.  Dans le fichier OnlineOrder.cs dans Visual Studio, remplacez la définition d’espace de noms existante par le code suivant :

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  Dans l' **Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**. Ajoutez les instructions suivantes **à l’aide** en haut du fichier pour inclure les espaces de noms pour le modèle que vous venez de créer, ainsi que de Bus des services.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Également dans le fichier HomeController.cs dans Visual Studio, remplacez la définition d’espace de noms existante par le code suivant. Ce code contient des méthodes permettant de gérer la présentation des éléments dans la file d’attente.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  Dans le menu **Générer** , cliquez sur **Générer la Solution** pour tester l’exactitude de votre travail jusqu'à présent.

5.  À présent, créez l’affichage pour la `Submit()` méthode que vous avez créée. Avec le bouton droit dans le `Submit()` méthode (la surcharge de `Submit()` qui vous permet d’accéder sans paramètres), puis sélectionnez **Ajouter une vue**.

    ![][14]

6.  Une boîte de dialogue s’affiche pour la création de la vue. Dans la liste **modèle** , choisissez **créer**. Dans la liste de **classe de modèle** , cliquez sur la classe **OnlineOrder** .

    ![][15]

7.  Cliquez sur **Ajouter**.

8.  À présent, modifiez le nom affiché de votre application. Dans **L’Explorateur de solutions**, double-cliquez sur la **Views\Shared\\_Layout.cshtml** fichier pour l’ouvrir dans l’éditeur Visual Studio.

9.  Remplacez toutes les occurrences de **Mon Application ASP.NET** **produits de LITWARE**.

10. Supprimer les liens **accueil**, **sur**et **Contact** . Supprimer le code en surbrillance :

    ![][28]

11. Enfin, modifiez la page de soumission pour inclure des informations sur la file d’attente. Dans l' **Explorateur de solutions**, double-cliquez sur le fichier **Views\Home\Submit.cshtml** pour l’ouvrir dans l’éditeur Visual Studio. Ajoutez la ligne suivante après `<h2>Submit</h2>`. Pour l’instant, les `ViewBag.MessageCount` est vide. Vous allez le renseigner ultérieurement.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Vous avez maintenant implémenté votre interface utilisateur. Vous pouvez appuyer sur **F5** pour exécuter votre application et vérifiez qu’elle s’affiche comme prévu.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Écrire du code pour l’envoi des éléments à une file d’attente Bus des services

À présent, ajoutez du code pour l’envoi des éléments à une file d’attente. Tout d’abord, vous créez une classe qui contient vos informations de connexion de Service Bus file d’attente. Ensuite, initialisation votre connexion à partir de Global.aspx.cs. Enfin, mettre à jour le code de soumission que vous avez créée en HomeController.cs soumettre des éléments à une file d’attente Bus des services.

1.  Dans l' **Explorateur de solutions**, cliquez sur **FrontendWebRole** (clic droit du projet, pas le rôle). Cliquez sur **Ajouter**, puis cliquez sur **cours**.

2.  Nom de la classe **QueueConnector.cs**. Cliquez sur **Ajouter** pour créer la classe.

3.  À présent, ajouter du code qui encapsule les informations de connexion et initialise la connexion à une file d’attente Bus des services. Remplacer tout le contenu de QueueConnector.cs par le code suivant, entrez des valeurs pour `your Service Bus namespace` (votre nom d’espace de noms) et `yourKey`, qui est **clé primaire** que vous avez précédemment obtenu à partir du portail Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  À présent, vérifiez que votre méthode **initialisation** est appelée. Dans l' **Explorateur de solutions**, double-cliquez sur **Global.asax\Global.asax.cs**.

5.  Ajoutez la ligne suivante du code à la fin de la méthode **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Enfin, mettez à jour le code web que vous avez créée, pour envoyer des éléments dans la file d’attente. Dans l' **Explorateur de solutions**, double-cliquez sur **Controllers\HomeController.cs**.

7.  Mise à jour la `Submit()` méthode (la surcharge qui ne prend aucun paramètre) comme suit pour obtenir le nombre de messages pour la file d’attente.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Mise à jour la `Submit(OnlineOrder order)` méthode (la surcharge qui prend un paramètre) comme suit pour envoyer les informations d’ordre vers la file d’attente.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Vous pouvez maintenant exécuter l’application à nouveau. Chaque fois que vous envoyez une commande, le nombre de messages augmente.

    ![][18]

## <a name="create-the-worker-role"></a>Créer le rôle de travail

Vous allez maintenant créer le rôle de collaborateur qui traite les envois de commande. Cet exemple utilise le modèle de projet Visual Studio **Rôle de travail avec file d’attente de Bus de Service** . Vous avez déjà obtenu les informations d’identification requises à partir du portail.

1. Vérifiez que vous avez connectés Visual Studio à votre compte Azure.

2.  Dans Visual Studio, dans **L’Explorateur de** clic droit sur le dossier **rôles** sous le projet **MultiTierApp** .

3.  Cliquez sur **Ajouter**, puis cliquez sur **Nouveau projet de rôle de collaborateur**. La boîte de dialogue **Ajouter un nouveau projet de rôle** s’affiche.

    ![][26]

4.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle** , cliquez sur **Rôle de travail avec file d’attente de Bus de Service**.

    ![][23]

5.  Dans la zone **nom** , nommez le projet **OrderProcessingRole**. Puis cliquez sur **Ajouter**.

6.  Copiez la chaîne de connexion que vous avez obtenu à l’étape 9 de la section « Créer un espace de noms Bus des services » dans le Presse-papiers.

7.  Dans l' **Explorateur de solutions**, avec le bouton droit **OrderProcessingRole** créée à l’étape 5 (Assurez-vous que vous avec le bouton droit **OrderProcessingRole** sous **rôles**et non la classe). Puis cliquez sur **Propriétés**.

8.  Sous l’onglet **paramètres** de la boîte de dialogue **Propriétés** , cliquez dans la zone **valeur** pour **Microsoft.ServiceBus.ConnectionString**et collez la valeur de point de terminaison que vous avez copié à l’étape 6.

    ![][25]

9.  Créer une classe **OnlineOrder** pour représenter les commandes, comme les traiter à partir de la file d’attente. Vous pouvez réutiliser un cours que vous avez déjà créé. Dans l' **Explorateur de solutions**, cliquez sur la classe **OrderProcessingRole** (l’icône de cours, pas le rôle avec le bouton droit). Cliquez sur **Ajouter**, puis cliquez sur **Un élément existant**.

10. Recherchez le sous-dossier pour **FrontendWebRole\Models**et puis double-cliquez sur **OnlineOrder.cs** pour l’ajouter à ce projet.

11. Dans **WorkerRole.cs**, modifiez la valeur de la variable **Queue** de `"ProcessingQueue"` à `"OrdersQueue"` comme indiqué dans le code suivant.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Ajoutez les éléments suivants à l’aide d’instruction en haut du fichier WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. Dans la `Run()` fonctionner, à l’intérieur du `OnMessage()` appeler, remplacez le contenu de la `try` clause par le code suivant.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Vous avez terminé l’application. Vous pouvez tester l’application complète par clic droit sur le projet MultiTierApp dans l’Explorateur de solutions, sélectionnez **définir comme projet de démarrage**et en appuyant sur F5. Notez que le nombre de messages ne s’incrémente pas, car le rôle de travail traite les éléments à partir de la file d’attente et les marque comme étant terminée. Vous pouvez voir la sortie de traçage de votre rôle de collaborateur en affichant l’interface utilisateur émulateur Azure calculer. Vous pouvez le faire par clic droit sur l’icône émulateur dans la zone de notification de votre barre des tâches et en sélectionnant **Afficher l’interface utilisateur émulateur de calcul**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Étapes suivantes  

Pour en savoir plus sur Bus des services, consultez les ressources suivantes :  

* [Bus des services Azure][sbmsdn]  
* [Page du service Bus des services][sbwacom]  
* [Comment utiliser les files d’attente de Bus de Service][sbwacomqhowto]  

Pour plus d’informations sur les scénarios à plusieurs niveaux, voir :  

* [Application d’à plusieurs niveaux de .NET à l’aide des objets BLOB files d’attente et des Tables de stockage][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obtenir des outils et Kit de développement]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  