<properties
    pageTitle="Analyse d’opinion Twitter en temps réel avec Analytique flux | Microsoft Azure"
    description="Découvrez comment utiliser Analytique de flux de données pour une analyse opinion Twitter en temps réel. Obtenir des instructions détaillées de génération d’événements à des données dans un tableau de bord live."
    keywords="analyse des tendances twitter en temps réel, analyse opinion, analyse de médias sociaux, exemple d’analyse de tendance."
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse de médias sociaux : analyse d’opinion Twitter en temps réel dans Azure flux Analytique

Apprenez à créer une solution d’analyse opinion pour analytique médias sociaux en mettant des événements Twitter en temps réel dans Azure événement Hubs. Vous pouvez écrire une requête Azure flux Analytique pour analyser les données. Vous devez ensuite stocker les résultats et consulter plus tard ou utiliser un tableau de bord et [Power BI](https://powerbi.com/) pour fournir des informations en temps réel.

Outils d’analytique médias sociaux aident les organisations comprendre les sujets actuels, c'est-à-dire sujets et attitude ayant un volume élevé de billets dans les réseaux sociaux. Analyse opinion, également appelée *exploration d’avis*, utilise médias sociaux analytique outils pour déterminer l’attitude un produit, idea et ainsi de suite. Analyse des tendances Twitter en temps réel est un excellent exemple, car le modèle d’abonnement hashtag permet d’écouter des mots clés spécifiques et de développement de l’analyse opinion au flux.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Scénario : Analyse d’opinion en temps réel

Société qui a un site Web de médias est souhaite recevoir un avantage sur ses concurrents par présentant du contenu de site immédiatement adaptées aux lecteurs. La société utilise l’analyse de médias sociaux sur des rubriques qui s’appliquent aux lecteurs en effectuant l’analyse opinion en temps réel sur des données Twitter. Plus précisément, pour identifier les tendances sujets en temps réel sur Twitter, la société doit analytique en temps réel sur le volume de tweet et opinion des rubriques clés. Par conséquent, par nature, qu’il soit nécessaire est un moteur d’analytique analyse opinion basé sur ce flux de médias sociaux.

## <a name="prerequisites"></a>Conditions préalables
-   Twitter compte et [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) à partir du centre de téléchargement Microsoft
-   Facultatif : Code Source pour client twitter de [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Créer un concentrateur événement d’entrée et un groupe de fournisseurs

L’exemple d’application génère des événements et les pousser à une instance de l’événement Hubs (un concentrateur événement, abrégé). Hubs d’événement Bus des services sont la méthode préférée de réception événement pour flux Analytique. Voir la documentation événement Hubs dans [Bus des services](/documentation/services/service-bus/).

Procédez comme suit pour créer un concentrateur de l’événement.

1.  Dans le portail Azure, cliquez sur **Nouveau** > **Application SERVICES** > **BUS des services** > **Événement concentrateur** > **Création rapide**et fournir un nom, une région et un espace de noms nouveau ou existant.  
2.  Comme il est recommandé de chaque tâche de flux de données Analytique lire à partir d’un seul groupe consommateur Hubs événement. Nous vous guidera à travers le processus de création d’un groupe consommateur ultérieurement. Vous pouvez en savoir plus sur les consommateurs en [vue d’ensemble Azure événement Hubs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de fournisseurs, accédez au concentrateur événement nouvellement créé, cliquez sur l’onglet **Groupes de consommateurs** et cliquez sur **créer** en bas de la page, puis attribuez un nom à votre groupe grand public.
3.  Pour accorder l’accès au concentrateur événement, nous nous avons besoin de créer une stratégie d’accès partagé. Cliquez sur l’onglet **configuration** de votre plateforme d’événement.
4.  Sous **Stratégies d’accès partagés**, créez une nouvelle stratégie avec **Gérer** les autorisations.

    ![Partagé dans lequel vous pouvez créer une stratégie avec des autorisations de gérer les stratégies d’accès.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** dans la partie inférieure de la page.
6.  Atteindre le **tableau de bord**, cliquez sur **Informations de connexion** dans la partie inférieure de la page, puis copiez et enregistrer les informations de connexion. (Utilisez l’icône de copie qui s’affiche sous l’icône de recherche).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application cliente Twitter

Nous vous fournissons une application cliente qui se connecteront à Twitter données via des [API de diffusion en continu de Twitter](https://dev.twitter.com/streaming/overview) pour recueillir des événements Tweet relatifs à un ensemble de rubriques paramétré. L’outil open source [Sentiment140](http://help.sentiment140.com/) est utilisé pour assigner une valeur opinion à chaque tweet.

- 0 = négatif
- 2 = neutre
- 4 = positif

Ensuite, événements Tweet ont diffusées sur le hub de l’événement.  

Suivez ces étapes pour configurer l’application :

1.  [Télécharger la solution TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Ouvrez TwitterClient.exe.config et remplacer oauth_consumer_key, oauth_consumer_secret, oauth_token et oauth_token_secret avec jetons Twitter ayant les valeurs.  

    [Étapes à suivre pour générer un jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Notez que vous devez effectuer une application pour générer un jeton vide.  
3.  Remplacer les valeurs EventHubConnectionString et EventHubName dans TwitterClient.exe.config par la chaîne de connexion et le nom de votre plateforme d’événement. La chaîne de connexion que vous avez copiée précédemment vous donne la chaîne de connexion et le nom de votre plateforme d’événement, veillez donc à les séparer et placer chacune d’elles dans le champ approprié. Par exemple, considérez la chaîne de connexion suivante :

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    Le fichier TwitterClient.exe.config doit contenir les paramètres comme dans l’exemple suivant :

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    Il est important de noter que le texte « EntityPath = » signifie __pas__ s’affichent dans la valeur EventHubName.

4.  *Facultatif :* Ajuster les mots clés à rechercher.  Par défaut, cette application recherche « Azure, Skype, XBox, Microsoft, Seattle ».  Vous pouvez ajuster les valeurs de **twitter_keywords** dans TwitterClient.exe.config, si vous le souhaitez.
5.  Exécutez TwitterClient.exe pour démarrer votre application. Vous verrez les événements Tweet avec les valeurs **créédans**, le **sujet**et **SentimentScore** envoyés à votre plateforme d’événement.

    ![Analyse opinion : valeurs SentimentScore envoyées à un concentrateur de l’événement.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

À présent qu’événements Tweet sont en continu en temps réel à partir de Twitter, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Mise en service d’une tâche de flux de données Analytique

1.  Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Nouveau** > **DATA SERVICES** > **Flux de données d’analyse** > **Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de données d’analyse de travail**:

    * **Nom de la tâche**: entrez un nom de la tâche.
    * **Région**: sélectionnez la zone où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le hub de l’événement dans la même région pour garantir de meilleures performances et pour garantir que vous pas ce mode de paiement transférer des données entre les régions.
    * **Compte de stockage**: sélectionnez le compte de stockage Azure que vous voulez utiliser pour stocker les données d’analyse pour toutes les tâches de flux de données Analytique qui s’exécutent dans cette zone. Vous avez la possibilité pour choisir un compte de stockage existant ou créez-en un.

3.  Cliquez sur **Flux de données d’analyse** dans le volet gauche pour répertorier les tâches de flux de données Analytique.  
    ![Icône de flux de données Analytique service](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    La nouvelle tâche est affichée avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer l’entrée de travail, la sortie et la requête avant de démarrer la tâche.


### <a name="specify-job-input"></a>Spécifier une entrée de travail
1.  Dans votre tâche de flux de données Analytique, cliquez sur **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous aidera à un certain nombre de procédure pour configurer votre saisie.
2.  Cliquez sur **flux de données**, puis cliquez sur le bouton droit.
3.  Cliquez sur **Concentrateur de l’événement**, puis cliquez sur le bouton droit.
4.  Tapez ou sélectionnez les valeurs suivantes dans la troisième page :

    * **ALIAS d’entrée**: entrez un nom convivial pour cette tâche d’entrée, par exemple *TwitterStream*. Notez que vous utiliserez ce nom dans la requête ultérieurement.
    **Concentrateur événement**: si le hub de l’événement que vous avez créé se trouve dans le même abonnement en tant que la tâche de flux de données Analytique, sélectionnez l’espace de noms figurant dans le hub de l’événement.

        Si votre plateforme d’événement se trouve dans un autre abonnement, cliquez sur **Utiliser événement concentrateur à partir d’un autre abonnement**et puis entrer manuellement les informations **d’Espace de noms de SERVICE BUS**, **Événement concentrateur nom**, **Nom de la stratégie événement concentrateur**, **Événement concentrateur stratégie clé**et **Le nombre d’événements concentrateur PARTITION**.

    * **Nom de l’événement concentrateur**: sélectionnez le nom du concentrateur événement.

    * **Nom de la stratégie concentrateur événement**: sélectionnez la stratégie de concentrateur événement que vous avez créée précédemment dans ce didacticiel.

    * **Groupe des consommateurs concentrateur événement**: tapez le nom du groupe grand public que vous avez créée précédemment dans ce didacticiel.
5.  Cliquez sur le bouton droit.
6.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8

7.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et vérifier que flux Analytique peut se connecter au hub de l’événement.

### <a name="specify-job-query"></a>Spécifier la requête de travail

Flux Analytique prend en charge un modèle de requête simple, déclarative qui décrit les transformations. Pour en savoir plus sur la langue, voir la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel vous aidera à créer et tester plusieurs requêtes sur Twitter données.

#### <a name="sample-data-input"></a>Exemple d’entrée de données

Pour valider votre requête sur des données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un fichier .json des événements de test.

1.  Sélectionnez votre événement concentrateur une entrée, puis cliquez sur **Exemples de données** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’ouvre, spécifiez une **heure de début** pour démarrer la collecte de données et une **durée** pour la quantité de données supplémentaire consommer.
3.  Cliquez sur le bouton **plus d’informations** , puis cliquez sur le lien **Cliquez ici** pour télécharger et enregistrer le fichier .json généré.

#### <a name="pass-through-query"></a>Requête SQL directe
Pour commencer, nous fera une requête SQL directe simple que tous les champs dans un événement les projets.

1.  Cliquez sur la **requête** en haut de la page de tâche de flux Analytique.
2.  Dans l’éditeur de code, remplacez le modèle de requête initial avec les éléments suivants :

        SELECT * FROM TwitterStream

    Vérifiez que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifiée précédemment.

3.  Sous l’éditeur de requête, cliquez sur **tester** .
4.  Accédez à votre fichier .json exemple.
5.  Cliquez sur le bouton **Vérifier** et afficher les résultats en dessous de la définition de la requête.

    ![Résultats affichés en dessous de la définition de requête](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Nombre de tweets par sujet : fenêtre qui s’écroulent avec une agrégation

Pour comparer le nombre de mentions entre les rubriques, nous allons utiliser un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par sujet de toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Cette requête utilise le mot-clé **Horodatage par** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure à laquelle chaque événement est arrivé dans le hub de l’événement.  En savoir plus dans la section « Arrivée Vs Application heure » de [Flux Analytique requête référence](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Cette requête accède également un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp** .

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

#### <a name="identify-trending-topics-sliding-window"></a>Identifier les sujets actuels : fenêtre coulissante

Pour identifier les sujets actuels, nous allons examiner les rubriques qui traversent une valeur de seuil de mentions dans un laps de temps donné. Aux fins de ce didacticiel, nous allons vérifier les rubriques sont mentionnées plus de 20 fois au cours des cinq dernières secondes à l’aide d’un [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Modifier la requête dans l’éditeur de code à : sélectionnez System.Timestamp comme heure, rubrique Nb (*) en tant que « mentions » à partir de TwitterStream horodatage par créédans groupe par SLIDINGWINDOW(s, 5), rubrique ayant compter (*) > 20

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Résultat de la requête fenêtre coulissante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Nombre de mentions et opinion : fenêtre qui s’écroulent avec une agrégation
La requête finale que nous allez tester utilise **TumblingWindow** pour obtenir le nombre de mentions, moyenne, minimum, maximum et l’écart type de résultat opinion pour chaque sujet toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.
3.  Il s’agit de la requête que nous utiliserons pour notre tableau de bord.  Cliquez sur **Enregistrer** dans la partie inférieure de la page.


## <a name="create-output-sink"></a>Créer récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur événement d’entrée pour ajouter des événements et une requête pour effectuer une transformation via le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche.  Nous allons écrire les événements tweet agrégé à partir de notre requête tâche dans stockage d’objets Blob Azure.  Vous pourriez pousser également les résultats de la base de données SQL Azure, stockage de Table Azure, ou événement Hubs, en fonction de votre application a besoin.

Procédez comme suit pour créer un conteneur pour le stockage Blob, si vous n’en avez pas déjà :

1.  Utiliser un compte de stockage existant ou créer un nouveau compte de stockage en cliquant sur **Nouveau** > **DATA SERVICES** > **stockage** > **Création rapide**et puis en suivant les instructions à l’écran.
2.  Sélectionnez le compte de stockage et cliquez sur **conteneurs** en haut de la page, puis cliquez sur **Ajouter**.
3.  Attribuez un **nom** à votre conteneur et définir son **accès** au **Public Blob**.

## <a name="specify-job-output"></a>Spécifier la sortie des tâches

1.  Dans votre tâche de flux de données Analytique, cliquez sur la **sortie** en haut de la page, puis cliquez sur **Ajouter une sortie**. La boîte de dialogue qui s’ouvre vous guidera tout au long de plusieurs étapes pour configurer votre sortie.
2.  Cliquez sur le **Stockage BLOB**, puis cliquez sur le bouton droit.
3.  Tapez ou sélectionnez les valeurs suivantes dans la troisième page :

    * **ALIAS de sortie**: entrez un nom convivial pour cette sortie des tâches.

    * **Abonnement**: si le stockage Blob que vous avez créé dans le même abonnement en tant que la tâche de flux de données Analytique, cliquez sur **Utiliser le compte de stockage d’abonnement actif**. Si votre espace de stockage se trouve dans un autre abonnement, cliquez sur **Utiliser le compte de stockage à partir d’un autre abonnement**et entrer manuellement les informations de **Compte de stockage**, **Clé de compte de stockage**et un **conteneur**.

    * **Compte de stockage**: sélectionnez le nom du compte de stockage.

    * **Conteneur**: sélectionnez le nom du conteneur.

    * **Nom de fichier préfixe**: tapez un préfixe de fichier à utiliser lors de l’écriture de sortie blob.

4.  Cliquez sur le bouton droit.
5.  Spécifiez les valeurs suivantes :
    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8
6.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et vérifiez que flux Analytique pouvez vous connecter pour le compte de stockage.

## <a name="start-job"></a>Démarrer le travail

Car une entrée de travail, requête et de sortie ont été spécifiés, nous sommes prêts à démarrer la tâche de flux de données Analytique.

1.  Dans le **tableau de bord**de travail, cliquez sur **Démarrer** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’ouvre, cliquez sur **l’heure de début de la tâche**, puis cliquez sur le bouton **Rechercher** en bas de la boîte de dialogue. L’état du travail remplacera par **départ** et modifiera peu de temps en cours **d’exécution**.


## <a name="view-output-for-sentiment-analysis"></a>Sortie d’affichage pour une analyse opinion

Une fois que votre tâche est en cours d’exécution et traitement du flux de Twitter en temps réel, choisissez comment vous voulez afficher les résultats d’une analyse opinion. Utilisez un outil comme [Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher votre sortie des tâches en temps réel. De là, vous pouvez utiliser [Power BI](https://powerbi.com/) pour étendre votre application pour inclure un tableau de bord personnalisé celle présentée dans l’écran suivant.

![Analyse de médias sociaux : flux Analytique opinion (exploration avis) sortie dans un tableau de bord Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
