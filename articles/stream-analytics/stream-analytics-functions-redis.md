<properties
    pageTitle="La sortie vers un Cache Azure Redis, à l’aide de fonctions Azure, à partir d’Azure flux Analytique | Microsoft Azure"
    description="Découvrez comment utiliser une fonction d’Azure connecté une file d’attente Bus de Service, pour remplir un Cache Azure Redis à partir du résultat d’une tâche de flux de données Analytique."
    keywords="flux de données, redis cache, file d’attente de bus de service"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Comment faire pour stocker les données à partir d’Azure flux Analytique dans un Cache Azure Redis à l’aide de fonctions Azure

Azure Analytique de flux de données vous permet de développer et déployer des solutions de faible coût pour Familiarisez-vous en temps réel à partir d’appareils, capteurs, infrastructure et d’applications ou n’importe quel flux de données rapidement. Il permet à différentes utilisations telles que la gestion en temps réel et commande et de contrôle, détection de fraude, voitures connectées et bien d’autres encore. Dans de nombreux ces scénarios, vous souhaiterez stocker les données envoyées par Azure flux Analytique dans un magasin de données distribuées comme un cache Azure Redis.

Supposons que vous faites partie d’une entreprise de télécommunications. Vous essayez de détecter une fraude Assistant où plusieurs appels provenant de la même identité, en même temps, mais dans différents géographiquement emplacements. Vous êtes chargé de stockage de tous les frauduleux appels téléphoniques potentiels dans un cache Azure Redis. Dans ce billet de blog, nous fournissons des instructions sur la façon dont vous pouvez facilement exécuter votre tâche. 

## <a name="prerequisites"></a>Conditions préalables
Terminer la [Détection de fraude en temps réel] [ fraud-detection] étape par étape pour ASA

## <a name="architecture-overview"></a>Présentation de l’architecture
![Capture d’écran de l’architecture](./media/stream-analytics-functions-redis/architecture-overview.png)

Comme le montre l’illustration précédente, flux Analytique permet de diffusion en continu les données d’entrée pour interroger et envoyé vers une sortie. En fonction de la sortie, fonctions Azure peuvent puis déclencher un type d’événement. 

Dans ce billet de blog, nous concentrer sur la partie Azure fonctions de ce pipeline ou plus précisément le déclenchement d’un événement qui stocke les données frauduleuses dans le cache.
La fin de la [Détection de fraude en temps réel] [ fraud-detection] didacticiel, vous avez une entrée (un concentrateur événement), une requête et une sortie (stockage d’objets blob) déjà configuré et en cours d’exécution. Dans ce billet de blog, nous modifier la sortie pour utiliser une file d’attente Service Bus à la place. Après cela, nous connecter une fonction d’Azure à cette file d’attente. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Créer et connecter une sortie file d’attente de Bus de Service
Pour créer une file d’attente de Bus de Service, suivez les étapes 1 et 2 de la section .NET dans [Prise en main files d’attente de Service Bus][servicebus-getstarted].
Maintenant nous allons vous connecter la file d’attente à la tâche de flux Analytique qui a été créée dans la procédure de détection de fraude antérieure.



1. Dans le portail Azure, accédez à la carte **des sorties** de votre travail et sélectionnez **Ajouter** dans la partie supérieure de la page.

    ![Ajout de sorties](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Choisissez **File d’attente de Service Bus** comme le **récepteur** et suivez les instructions à l’écran. Assurez-vous de choisir l’espace de noms de la file d’attente de Bus de Service vous avez créé dans [Prise en main files d’attente de Service Bus][servicebus-getstarted]. Lorsque vous avez terminé, cliquez sur le bouton « right ».
3. Spécifiez les valeurs suivantes :
    - **Format d’événement sérialiseur**: JSON
    - **Codage**: UTF8
    - **FORMAT**: ligne en les séparant

4. Cliquez sur le bouton **créer** pour ajouter cette source et vérifiez que flux Analytique pouvez vous connecter pour le compte de stockage.

5. Dans l’onglet **requête** , remplacez la requête actuelle avec les éléments suivants. Remplacez *[Votre SERVICE BUS nom]* par le nom de sortie que vous avez créé à l’étape 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Créer un Cache Redis Azure
Créer un cache Azure Redis en suivant la section .NET dans [la section Utilisation Azure Redis Cache] [ use-rediscache] jusqu'à ce que la section appelée ***configurer les clients de cache***.
Une fois terminé, vous avez un Cache Redis. Sous **tous les paramètres**, sélectionnez **les touches d’accès** et note vers le bas de la ***chaîne de connexion principal***.

![Capture d’écran de l’architecture](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Créer une fonction d’Azure
Suivez [créer votre première fonction Azure] [ functions-getstarted] didacticiel pour commencer à utiliser les fonctions Azure. Si vous avez déjà une fonction Azure que vous voulez utiliser, puis passez directement à [style pour mettre en Cache Redis](#Writing-to-Redis-Cache)

1. Dans le portail, sélectionnez Services d’application dans le volet de navigation gauche, puis cliquez sur votre nom de l’application Azure fonction pour accéder au site Web de l’application de la fonction.
    ![Capture d’écran de la liste des applications services fonction](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Cliquez sur **nouvelle fonction > ServiceBusQueueTrigger – c#**. Pour les champs suivants, suivez ces instructions :
    - **Nom de la file d’attente**: le même nom que celui que vous avez entré lorsque vous avez créé la file d’attente dans [Prise en main files d’attente de Service Bus] [ servicebus-getstarted] (pas le nom du bus service). Vérifiez que vous utilisez la file d’attente qui est connecté à la sortie de flux Analytique.
    - **Connexion de service Bus**: sélectionnez **Ajouter une chaîne de connexion**. Pour rechercher la chaîne de connexion, accédez au portail classique, sélectionnez **Bus des services**, le bus des services que vous avez créé et les **Informations de connexion** dans la partie inférieure de l’écran. Vérifiez que vous êtes dans l’écran principal de cette page. Copiez et collez la chaîne de connexion. N’hésitez pas à entrer un nom de connexion.
    
        ![Capture d’écran de connexion au service bus](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: choisissez **Gérer**


3. Cliquez sur **créer**

## <a name="writing-to-redis-cache"></a>Écrire sur Redis Cache
Nous avons créé maintenant une fonction d’Azure qui lit à partir d’une file d’attente de Bus de Service. Tout ce qu’il reste à faire est notre fonction pour écrire ces données dans le Cache Redis. 

1. Sélectionnez votre **ServiceBusQueueTrigger**nouvellement créé, puis cliquez sur **paramètres de l’application fonction** dans le coin supérieur droit. Sélectionnez **accédez à paramètres du Service application > Paramètres > Paramètres de l’Application**

2. Dans la section de chaînes de connexion, créez un nom dans la section **nom** . Collez la chaîne de connexion principale trouvé à l’étape de **créer un Cache Redis** dans la section **valeur** . Cliquez sur **Personnaliser** l’emplacement indiqué **Base de données SQL**.

3. Dans la partie supérieure, cliquez sur **Enregistrer** .

    ![Capture d’écran de connexion au service bus](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Revenez aux paramètres du Service de l’application et sélectionnez **Outils > Éditeur de l’application de Service (Preview) > sur > accédez**.

    ![Capture d’écran de connexion au service bus](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Dans un éditeur de votre choix, créez un fichier JSON nommé **project.json** avec les éléments suivants et enregistrez-le sur votre disque local.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Téléchargez ce fichier dans le répertoire racine de votre fonction (pas WWWROOT). Vous devez voir un fichier nommé **project.lock.json** automatiquement s’affiche, confirmant que le Nuget packages « StackExchange.Redis » et « Newtonsoft.Json » ont été importés.

7. Dans le fichier **run.csx** , remplacez le code déjà généré par le code suivant. Dans la fonction lazyConnection, remplacez « Nom de connexion » par le nom que vous avez créé à l’étape 2 de **stockez les données dans le cache Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Démarrer la tâche de flux de données Analytique

1. Démarrez l’application telcodatagen.exe. L’utilisation est la suivante :````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. À partir de la carte de flux de données Analytique tâche dans le portail, cliquez sur **Démarrer** dans la partie supérieure de la page.

    ![Capture d’écran de tâche de début](./media/stream-analytics-functions-redis/starting-job.png)

3. Dans la carte de **Démarrer le travail** qui s’affiche, sélectionnez **maintenant** , puis sur le bouton **Démarrer** dans la partie inférieure de l’écran. Changements d’état de tâche au démarrage et après certaines modifications de l’heure en cours d’exécution.
 
    ![Capture d’écran de sélection de temps de travail début](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Exécuter les solutions et vérifier des résultats
Pour revenir à votre page **ServiceBusQueueTrigger** , vous devez maintenant voir instructions log. Ces fichiers journaux indiquent que vous vous avez quelque chose à partir de la file d’attente Bus Service, mettez-la dans la base de données et extraites à l’aide de l’heure comme clé !

Pour vérifier que vos données se trouve dans le cache de résolution Redis, accédez à votre page de cache Redis dans le nouveau portail (comme illustré dans l’étape précédente de [créer un Cache Redis Azure](#Create-an-Azure-Redis-Cache) ) et sélectionnez Console.

Vous pouvez à présent écrire commandes Redis pour confirmer que les données se trouve en fait dans le cache.

![Capture d’écran de la Console Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Étapes suivantes
Nous sommes enthousiasme Azure fonctions nouveautés et analytique flux possibles ensemble, et nous espérons que ceci permet de déverrouiller nouvelles possibilités pour vous. Si vous avez des commentaires sur ce que vous souhaitez ensuite, n’hésitez pas à utiliser le [site UserVoice Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Si vous êtes nouveau Microsoft Azure, nous vous invitons à essayer en qui s’inscrit à un [compte d’évaluation Azure gratuit](https://azure.microsoft.com/pricing/free-trial/). Si vous débutez dans Analytique de flux, puis nous vous invitons à [créer votre première tâche de flux de données Analytique](stream-analytics-create-a-job.md).

Si vous avez besoin d’une aide ou avez des questions, publier sur les forums [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) ou [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

Vous pouvez également voir les ressources suivantes :

- [Référence du développeur fonctions Azure](../azure-functions/functions-reference.md)
- [Référence du développeur Azure fonctions c#](../azure-functions/functions-reference-csharp.md)
- [Référence du développeur Azure fonctions F #](../azure-functions/functions-reference-fsharp.md)
- [Référence du développeur NodeJS fonctions Azure](../azure-functions/functions-reference.md)
- [Liaisons et azure déclencheurs de fonctions](../azure-functions/functions-triggers-bindings.md)
- [Comment faire pour surveiller Azure Redis Cache](../redis-cache/cache-how-to-monitor.md)

Pour rester informé des toutes les dernières actualités et fonctionnalités, suivez [@AzureStreaming](https://twitter.com/AzureStreaming) sur Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
