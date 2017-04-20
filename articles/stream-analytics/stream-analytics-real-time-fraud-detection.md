<properties
    pageTitle="Flux Analytique : Détection de fraude en temps réel | Microsoft Azure"
    description="Apprenez à créer une solution de détection de fraude en temps réel avec Analytique de flux de données. Utiliser un concentrateur de l’événement pour le traitement des événements en temps réel."
    keywords="détection des anomalies, détection de fraude, détection des anomalies en temps réel"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Commencer à utiliser Azure flux Analytique : détection de fraude en temps réel

Apprenez à créer une solution de bout en bout pour la détection de fraude en temps réel avec Azure flux Analytique. Importer des événements dans Azure événement Hubs, écrire des requêtes de flux Analytique d’agrégation ou d’alerte et envoyer les résultats à un récepteur de sortie pour mieux analyse des données avec traitement en temps réel. Détection d’anomalie en temps réel pour télécommunications est expliquée, mais la technique exemple est aussi bien d’autres types de détection de fraude tels que des scénarios vol carte bancaire ou identité.

Flux de données Analytique est un service entièrement géré qui fournit un traitement de l’événement faible latence, hautement disponible, format SVG, complexes sur la diffusion en continu de données dans le cloud. Pour plus d’informations, voir [présentation Azure flux Analytique](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénario : Télécommunications et Assistant fraude la détection en temps réel

Une entreprise de télécommunications comporte un grand nombre de données pour les appels entrants. L’entreprise a besoin de ce qui suit à partir de ses données :

* Réduire les données à une taille gérable et obtenir des informations sur l’utilisation du client au fil du temps et au sein de régions géographiques.
* Détecter une fraude Assistant (plusieurs appels provenant de la même identité en même temps, mais à des endroits différents géographiquement) en temps réel afin que la société puisse facilement répondre en avertir clients ou d’arrêt de service.

Scénarios Internet des objets (IoT) canoniques ont une tonne de télémétrie ou des données à partir de capteurs. Les clients veulent agréger les données ou recevoir des alertes concernant anomalies en temps réel.

## <a name="prerequisites"></a>Conditions préalables

- Téléchargez [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) à partir du centre de téléchargement Microsoft
- Facultatif : Le code du Générateur d’événements à partir de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) Source

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Créer Azure événement Hubs d’entrée et consommateur groupe

L’exemple d’application génère des événements et les pousser à une instance de l’événement Hubs pour traitement en temps réel. Service Bus événement Hubs sont la méthode préférée de réception événement pour flux Analytique. Vous pouvez en savoir plus sur événement Hubs dans la [documentation de Bus des services Azure](/documentation/services/service-bus/).

Pour créer un concentrateur événement :

1.  Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **Nouveau** > **Application SERVICES** > **SERVICE BUS** > **Événement concentrateur** > **Création rapide**. Fournir un nom, une région et un espace de noms nouvelle ou existante pour créer un nouveau concentrateur de l’événement.  
2.  Comme il est conseillé de lire la chaque tâche de flux de données Analytique d’un groupe de consommateur seul événement concentrateur. Nous vous guidera à travers le processus de création d’un groupe consommateur ultérieurement. [En savoir plus sur les consommateurs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de fournisseurs, accédez au concentrateur événement nouvellement créé, cliquez sur l’onglet **Groupes de consommateurs** et cliquez sur **créer** en bas de la page, puis attribuez un nom à votre groupe grand public.
3.  Pour accorder l’accès au concentrateur événement, nous nous avons besoin de créer une stratégie d’accès partagé. Cliquez sur l’onglet **configuration** de votre plateforme d’événement.
4.  Sous **Stratégies d’accès partagés**, créez une nouvelle stratégie qui a **Gérer** les autorisations.

    ![Partagé dans lequel vous pouvez créer une stratégie avec des autorisations de gérer les stratégies d’accès.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** dans la partie inférieure de la page.
6.  Atteindre le **tableau de bord**, cliquez sur **Informations de connexion** dans la partie inférieure de la page, puis copiez et enregistrer les informations de connexion.

## <a name="configure-and-start-the-event-generator-application"></a>Configurer et démarrer l’application de générateur d’événement

Nous vous fournissons une application cliente qui génèrent des métadonnées appel entrant exemple et insérez-la aux événements Hubs. Utilisez les étapes suivantes pour configurer cette application.  

1.  Téléchargez le [fichier TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)et décompresser le fichier dans un répertoire.

    > [AZURE.NOTE] Windows peut bloquer le fichier zip téléchargé. Cliquez sur le fichier, puis sélectionnez **Propriétés**. Si vous voyez le message « ce fichier provenance d’un autre ordinateur et peut-être être bloqué pour mieux vous protéger cet ordinateur », activez la case **Débloquer** , puis cliquez sur Appliquer dans le fichier zip.

2.  Remplacer les valeurs Microsoft.ServiceBus.ConnectionString et EventHubName dans telcodatagen.exe.config avec votre chaîne de connexion événement concentrateur et le nom.

    La chaîne de connexion que vous avez copiée à partir du portail Azure place le nom de la connexion à la fin. N’oubliez pas de supprimer « ; EntityPath =<value>» à partir de la « Ajouter clé = « champ.

3.  Démarrez l’application. L’utilisation est la suivante :

    telcodatagen.exe [#NumCDRsPerHour] [Assistant Carte fraude probabilité] [#DurationHours]

L’exemple suivant génère 1 000 événements avec une probabilité de 20 pour cent de fraude au cours des deux heures.

    telcodatagen.exe 1000 .2 2

Vous verrez enregistrements envoyés à votre plateforme d’événement. Certains champs de clé que nous utiliserons dans cette application de détection de fraude en temps réel sont définies ici :

| Enregistrement | Définition |
| ------------- | ------------- |
| CallrecTime | Horodatage pour l’heure de début d’appel. |
| SwitchNum | Commutateur de téléphone utilisé pour vous connecter à l’appel. |
| CallingNum | Numéro de téléphone de l’appelant. |
| CallingIMSI | Identification de l’abonné Mobile international (IMSI).  Identificateur unique de l’appelant. |
| CalledNum | Numéro de téléphone du destinataire de l’appel. |
| CalledIMSI | Identification de l’abonné Mobile international (IMSI).  Identificateur unique du destinataire de l’appel. |


## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique
Maintenant que nous disposons d’un flux d’événements de télécommunications, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Mise en service d’une tâche de flux de données Analytique

1.  Dans le portail Azure, cliquez sur **Nouveau** > **DATA SERVICES** > **Flux de données d’analyse** > **Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de données d’analyse de travail**:

    * **Nom de la tâche**: entrez un nom de la tâche.

    * **Région**: sélectionnez la zone où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le hub de l’événement dans la même région pour garantir de meilleures performances et pour garantir que vous pas ce mode de paiement transférer des données entre les régions.

    * **Compte de stockage**: sélectionnez le compte de stockage Azure que vous voulez utiliser pour stocker les données d’analyse pour toutes les tâches de flux de données Analytique qui s’exécutent dans cette zone. Vous avez la possibilité de choisir un compte de stockage existant ou créez-en un.

3.  Cliquez sur **Flux de données d’analyse** dans le volet gauche pour répertorier les tâches de flux de données Analytique.

    ![Icône de flux de données Analytique service](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    La nouvelle tâche est affichée avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer l’entrée de travail, la sortie et la requête avant de démarrer la tâche.

### <a name="specify-job-input"></a>Spécifier une entrée de travail
1.  Dans votre tâche de flux de données Analytique, cliquez sur **entrées** dans la partie supérieure de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous guidera tout au long de plusieurs étapes pour configurer votre saisie.
2.  Cliquez sur **flux de données**, puis cliquez sur le bouton droit.
3.  Cliquez sur **Concentrateur de l’événement**, puis cliquez sur le bouton droit.
4.  Tapez ou sélectionnez les valeurs suivantes dans la troisième page :

    * **Saisie de données d’ALIAS**: entrez un nom convivial, tel que *CallStream*, pour cette tâche. Notez que vous utiliserez ce nom dans la requête ultérieurement.

    * **Concentrateur événement**: si le hub de l’événement que vous avez créé se trouve dans le même abonnement en tant que la tâche de flux de données Analytique, sélectionnez l’espace de noms figurant dans le hub de l’événement.

        Si votre plateforme d’événement se trouve dans un autre abonnement, sélectionnez **Utiliser événement Hub à partir d’un autre abonnement**et puis entrer manuellement les informations **d’Espace de noms de SERVICE BUS**, **Événement concentrateur nom**, **Nom de la stratégie événement concentrateur**, **Événement concentrateur stratégie clé**et **Le nombre d’événements concentrateur PARTITION**.

    * **Nom de l’événement concentrateur**: sélectionnez le nom du concentrateur événement.

    * **Nom de la stratégie concentrateur événement**: sélectionnez la stratégie de concentrateur événement que vous avez créée précédemment dans ce didacticiel.

    * **Groupe des consommateurs concentrateur événement**: tapez le nom du groupe grand public que vous avez créée précédemment dans ce didacticiel.

5.  Cliquez sur le bouton droit.
6.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8
7.  Cliquez sur le bouton **Rechercher** pour ajouter cette source et vérifier que flux Analytique peut se connecter au hub de l’événement.

### <a name="specify-job-query"></a>Spécifier la requête de travail

Flux Analytique prend en charge un modèle de requête simple, déclarative qui décrit les transformations pour traitement en temps réel. Pour en savoir plus sur la langue, voir la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/dn834998.aspx). Ce didacticiel vous aidera à créer et tester plusieurs requêtes via votre flux de données de l’appel en temps réel.

#### <a name="optional-sample-input-data"></a>Facultatifs : Exemples de données d’entrée
Pour valider votre requête sur des données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un. JSON fichier des événements de test.  Les étapes suivantes montrent comment effectuer cette opération. Nous vous fournissons également un exemple de fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) des fins de test.

1.  Sélectionnez votre événement concentrateur une entrée, puis cliquez sur **Exemples de données** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’ouvre, spécifiez une **heure de début** pour démarrer la collecte de données et une **durée** pour la quantité de données supplémentaire consommer.
3.  Cliquez sur le bouton **Rechercher** pour démarrer des données d’échantillonnage à partir de l’entrée.  Il peut prendre une minute ou deux pour le fichier de données devant être produites.  Lorsque le processus est terminé, cliquez sur **Détails**, téléchargez généré. JSON de fichier, puis enregistrez-le.

    ![Télécharger et enregistrer des données traitées dans un fichier JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Requête SQL directe

Si vous souhaitez archiver tous les événements, vous pouvez utiliser une requête SQL directe à lire tous les champs de la charge utile de l’événement ou le message. Pour démarrer, effectuez une requête SQL directe simple que tous les champs dans un événement les projets.

1.  Cliquez sur la **requête** à partir du haut de la page de tâche de flux Analytique.
2.  Ajoutez les éléments suivants à l’éditeur de code :

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Vérifiez que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifiée précédemment.

3.  Sous l’éditeur de requête, cliquez sur **tester** .
4.  Fournir un fichier de test. Utilisez une que vous avez créé à l’aide de la procédure précédente, ou [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Cliquez sur le bouton **Vérifier** et consultez les résultats affichés en dessous de la définition de la requête.

    ![Résultats de la définition de requête](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projection de colonne

Nous allons maintenant Réduisez les champs renvoyés un plus petit ensemble.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Sortie dans l’éditeur de requête.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d’entrant appelle par région : fenêtre qui s’écroulent avec une agrégation

Pour comparer le nombre d’appels entrants par région, nous allons utiliser un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre d’appels entrants regroupés par **SwitchNum** toutes les cinq secondes.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Cette requête utilise le mot-clé **Horodatage par** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure à laquelle chaque événement est arrivé dans le hub de l’événement. Voir [« Arrivée Vs Application heure » dans le flux de données Analytique de référence du langage de requête](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Notez que vous pouvez accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp** .

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Résultats de la requête pour Timestand par](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Détection de fraude Assistant avec une jointure réflexive

Pour identifier l’utilisation potentiellement frauduleuse, nous allons examiner pour les appels provenant de l’utilisateur même mais à différents emplacements inférieure à 5 secondes.  Nous [rejoindre](https://msdn.microsoft.com/library/azure/dn835026.aspx) le flux d’événements d’appel avec lui-même pour vérifier dans ce cas.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Résultats de la requête d’une jointure](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Créer récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur événement d’entrée pour ajouter des événements et une requête pour effectuer une transformation via le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche. Nous allons écrire des événements pour comportement frauduleux au stockage d’objets Blob Azure.

Procédez comme suit pour créer un conteneur pour le stockage Blob si vous n’en avez pas déjà.

1.  Utiliser un compte de stockage existant ou créer un nouveau compte de stockage en cliquant sur **Nouveau > DATA SERVICES > stockage > Création rapide**, puis suivez les instructions.
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

## <a name="start-job-for-real-time-processing"></a>Démarrer la tâche pour un traitement en temps réel

Car une entrée de travail, requête et de sortie ont été spécifiés, nous sommes prêts à démarrer la tâche de flux de données Analytique pour la détection de fraude en temps réel.

1.  Dans le **tableau de bord**de travail, cliquez sur **Démarrer** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’ouvre, cliquez sur **l’heure de début de la tâche**, puis cliquez sur le bouton **Rechercher** en bas de la boîte de dialogue. L’état du travail remplacera par **départ** et modifiera peu de temps en cours **d’exécution**.

## <a name="view-fraud-detection-output"></a>Sortie de détection de fraude affichage

Utilisez un outil comme [Explorateur de stockage Azure](http://storageexplorer.com/) ou [Explorer Azure](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher les événements frauduleux qu’ils sont écrits dans votre sortie en temps réel.  

![Détection de fraude : événements frauduleux affichés en temps réel](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
