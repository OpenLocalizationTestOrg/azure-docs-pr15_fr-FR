<properties 
    pageTitle="Quel est le Kit de développement WebJobs Azure" 
    description="Introduction au SDK WebJobs Azure. Explique ce que le Kit de développement, scénarios, il est utile pour et exemples de code." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>Quel est le Kit de développement WebJobs Azure

## <a id="overview"></a>Vue d’ensemble

Cet article explique ce qu’est le WebJobs SDK, passe en revue quelques scénarios courants, il est utile pour et donne un aperçu de comment l’utiliser dans votre code.

[WebJobs](websites-webjobs-resources.md) est une fonctionnalité de Service d’application Azure qui vous permet d’exécuter un programme ou un script dans le même contexte comme une application web, une application API ou une application mobile. L’objectif du [Kit de développement logiciel WebJobs](websites-webjobs-resources.md) est pour simplifier le code que vous écrivez pour les tâches courantes qu’un WebJob peuvent effectuer, par exemple de traitement d’images, traitement en file d’attente, RSS d’agrégation, gestion des fichiers et envoi de messages électroniques. Le SDK WebJobs possède des fonctionnalités intégrées pour travailler avec le stockage Azure et Bus des services, pour la planification des tâches et gestion des erreurs et de nombreux autres scénarios courants. Par ailleurs, il est conçu pour être extensible. [Kit de développement logiciel WebJobs est l’open source](https://github.com/Azure/azure-webjobs-sdk/), et il est d' [Ouvrir le référentiel source pour les extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

Le SDK WebJobs comprend les éléments suivants :

* **Packages NuGet**. Packages NuGet que vous ajoutez à un projet d’Application de Console Visual Studio fournissent une structure que votre code utilise par décoration vos méthodes avec les attributs WebJobs SDK.
  
* **Tableau de bord**. Partie du WebJobs SDK est inclus dans le Service d’application Azure et fournit surveillance enrichies et les diagnostics pour les programmes qui utilisent les packages NuGet. Vous n’êtes pas obligé d’écrire du code pour utiliser ces fonctionnalités de surveillance et de diagnostic.

## <a id="scenarios"></a>Scénarios

Voici quelques scénarios classiques, que vous pouvez gérer plus facilement avec le Kit de développement WebJobs Azure :

* Image de la transformation ou toute autre tâche intensive du processeur. Une fonctionnalité courante des sites Web est la possibilité de télécharger des images ou des vidéos. Fréquence à laquelle vous souhaitez manipuler le contenu après son téléchargement, mais vous ne voulez pas qu’un utilisateur patienter pendant que vous le faire.

* Traitement des files d’attente. Une pratique courante pour un site Web frontal web communiquer avec un service en aval consiste à utiliser des files d’attente. Lorsque le site Web doit travailler, il envoie un message dans une file d’attente. Un service en aval extrait les messages de la file d’attente et effectue le travail. Vous pouvez aussi utiliser files d’attente pour traitement de l’image : par exemple, une fois que l’utilisateur télécharge un certain nombre de fichiers, placez les noms de fichiers dans un message file d’attente doit prélever par le serveur principal pour traitement. Ou files d’attente vous permet d’améliorer la réactivité de site. Par exemple, au lieu d’écrire directement dans une base de données SQL, écrivez à une file d’attente, indiquer à l’utilisateur que vous avez terminé et permettent d’utiliser la base de données relationnelles principale service poignée à latence élevée. Pour obtenir un exemple de file d’attente de traitement avec les processus de l’image, consultez le [didacticiel WebJobs SDK prise en main](websites-dotnet-webjobs-sdk-get-started.md).

* Agrégation RSS. Si vous avez un site qui gère une liste de flux RSS, vous pourriez extraire dans l’ensemble des articles à partir du flux dans un processus en arrière-plan.

* Gestion des fichiers, par exemple agréger ou nettoyage des fichiers journaux.  Vous devrez entraînerait la création en plusieurs sites ou pour les périodes séparé auquel vous voulez combiner pour exécuter les tâches d’analyse dessus pour les fichiers journaux. Ou vous souhaiterez peut-être planifier une tâche pour exécuter hebdomadaire pour effacer les anciens fichiers journaux.

* Pénétration en Tables Azure. Vous pourriez avoir des fichiers stockés et des objets BLOB et souhaitez les analyser et stocker les données dans des tables. La fonction pénétration pourrait être écrire des lots de lignes (millions dans certains cas) et le WebJobs SDK permet d’implémenter facilement cette fonctionnalité. Le Kit de développement fournit également la surveillance en temps réel d’indicateurs de progression telles que le nombre de lignes écrites dans la table.

* Autres tâches longue que vous voulez exécuter dans un thread d’arrière-plan, telles que [l’envoi de messages électroniques](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Toutes les tâches que vous voulez exécuter sur une planification, par exemple une opération de sauvegarde chaque nuit.

Dans la plupart de ces scénarios vous pouvez souhaiter à l’échelle d’une application web pour s’exécuter sur plusieurs ordinateurs virtuels, qui s’exécute simultanément plusieurs WebJobs. Dans certains scénarios, cela peut entraîner dans les mêmes données traitées plusieurs fois, mais ce n’est pas un problème lorsque vous utilisez la file d’attente intégré, blob et des déclencheurs Bus des services de WebJobs SDK. Le Kit de développement garantit que les fonctions seront traitées qu’une seule fois pour chaque message ou blob.

Le WebJobs SDK facilite également gérer le traitement des scénarios erreurs courantes. Vous pouvez définir des alertes pour envoyer des notifications lorsqu’une fonction échoue, et vous pouvez définir la durée afin qu’une fonction est automatiquement annulée si elle ne se termine dans un délai spécifié.

## <a id="code"></a>Exemples de code

Le code pour la gestion des tâches courantes qui fonctionnent avec le stockage Azure est simple. Dans votre Application Console `Main` méthode vous créez un `JobHost` objet que les appels aux méthodes que vous rédigez des coordonnées. L’infrastructure WebJobs SDK sait quand il doit appeler vos méthodes et que les valeurs de paramètre à utiliser basent sur les attributs WebJobs SDK que vous utilisez qu’ils. Le Kit de développement fournit des *déclencheurs* qui spécifient les conditions causes de la fonction à appeler et des *classeurs* qui spécifient comment obtenir des informations dans et se déconnecter de paramètres de la méthode.

Par exemple, l’attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) entraîne une fonction pour être appelé lorsqu’un message est reçu sur une file d’attente, et si le format du message est JSON pour un tableau d’octets ou un type personnalisé, le message est automatiquement désérialisé. L’attribut [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) déclenche un processus chaque fois qu’un nouveau blob est créé dans un compte de stockage Azure.

Voici un programme simple qui consulte une file d’attente et crée un blob pour chaque message file d’attente reçu :

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

La `JobHost` objet est un conteneur pour un ensemble de fonctions de l’arrière-plan. La `JobHost` objet surveille les fonctions, des observations pour les événements qui déclenchent, et s’exécute les fonctions lorsque l’événement déclencheur se produit. Vous appelez un `JobHost` méthode pour indiquer si vous souhaitez que le processus de conteneur pour s’exécuter sur le thread actuel ou un thread d’arrière-plan. Dans l’exemple, le `RunAndBlock` méthode s’exécute le processus en continu sur le thread actuel.

Étant donné que la `ProcessQueueMessage` méthode dans cet exemple a un `QueueTrigger` attribut, le déclencheur pour cette fonction est la réception d’un nouveau message file d’attente. La `JobHost` objet surveille pour les nouveaux messages de file d’attente dans la file d’attente spécifiée (« webjobsqueue » dans cet exemple) et dès qu’il est détecté, il appelle `ProcessQueueMessage`. 

La `QueueTrigger` attribut lie le `inputText` paramètre à la valeur du message file d’attente. Et la `Blob` attribut lie une `TextWriter` objet à un blob appelé « blobname » dans un conteneur nommé « containername ».  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

La fonction utilise ces paramètres pour écrire la valeur du message file d’attente au blob :

        writer.WriteLine(inputText);

Les fonctionnalités déclencheur et liant du WebJobs SDK simplifient le code que vous avez à écrire. Le code de niveau inférieur requis pour traiter les files d’attente, des objets BLOB ou des fichiers ou pour lancer des tâches planifiées, est effectué pour vous par l’infrastructure WebJobs SDK. Par exemple, l’infrastructure crée des files d’attente qui n’existent pas encore, ouvre la file d’attente, les messages en file d’attente lectures, suppressions en file d’attente messages lorsque traitement terminée, crée des conteneurs blob qui n’existent pas encore, écrit dans des objets BLOB et ainsi de suite.

L’exemple suivant montre une variété de déclencheurs dans un seul WebJob : `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, et `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Planification

La `TimerTrigger` attribut vous donne la possibilité aux fonctions déclencheur à s’exécuter sur une planification. Vous pouvez planifier une WebJob entier par le biais des fonctions individuelles Azure ou planification d’une WebJob en utilisant WebJobs SDK `TimerTrigger`. Voici un exemple de code.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Pour des exemples de code, voir [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) dans le référentiel azure-webjobs-sdk-extensions sur GitHub.com.

## <a name="extensibility"></a>Extensibilité

Vous n’êtes pas obligé de fonctionnalités intégrées--le WebJobs SDK vous permet d’écrire les classeurs et des déclencheurs personnalisés.  Par exemple, vous pouvez écrire des déclencheurs pour les événements de cache et les planifications périodiques. Un [référentiel source d’ouvrir](https://github.com/Azure/azure-webjobs-sdk-extensions) contient un code [guide détaillé sur l’extensibilité WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) et exemples pour vous aider à commencer à écrire vos propres déclencheurs et des classeurs.

## <a id="workerrole"></a>À l’aide du Kit de développement WebJobs en dehors de WebJobs

Un programme qui utilise le le SDK WebJobs est une Application Console standard et anywhere peut être exécuté--ne doit pas nécessairement exécuter en tant qu’un WebJob. Vous pouvez tester le programme localement sur votre ordinateur de développement et en production vous pouvez l’exécuter dans un rôle de collaborateur Service Cloud ou un service Windows si vous préférez utiliser un de ces environnements. 

Toutefois, le tableau de bord n’est disponible en tant qu’extension d’une application web Azure Application Service. Si vous voulez exécuter en dehors d’une WebJob et continuer à utiliser le tableau de bord, vous pouvez configurer une application web pour utiliser le même compte de stockage que votre chaîne de connexion du tableau de bord SDK WebJobs fait référence à, et que WebJobs Dashboard l’application web provoque l’affichage données relatives à l’exécution de la fonction à partir de votre programme est en cours d’exécution dans un autre emplacement. Vous pouvez accéder au tableau de bord à l’aide de la.scm.azurewebsites.net/azurejobs/#/functions URL https://*{webappname}*. Pour plus d’informations, voir [l’obtention d’un tableau de bord pour le développement local avec le Kit de développement WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mais notez que le billet de blog affiche un ancien nom de chaîne de connexion. 

## <a id="nostorage"></a>Fonctionnalités de tableau de bord

Le SDK WebJobs présente plusieurs avantages par même si vous n’utilisez pas WebJobs SDK déclencheurs ou les classeurs :

* Vous pouvez appeler des fonctions du tableau de bord.
* Vous pouvez relire fonctions du tableau de bord.
* Vous pouvez afficher les journaux dans le tableau de bord, liés à la WebJob particulier (journaux d’applications, écrites à l’aide de Console.Out, Console.Error, Trace, etc.) ou lié à l’appel de fonction particulière ayant généré les (journaux écrits en utilisant un `TextWriter` objet qui le Kit de développement passe à la fonction en tant que paramètre). 

Pour plus d’informations, voir [comment appeler manuellement une fonction](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) et [comment écrire des journaux](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d’informations sur le WebJobs SDK, voir [Azure WebJobs recommandé de ressources](http://go.microsoft.com/fwlink/?linkid=390226).

Pour plus d’informations sur les dernières améliorations apportées au SDK WebJobs, consultez les [Notes de publication](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
