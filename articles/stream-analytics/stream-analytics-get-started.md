<properties
    pageTitle="Prise en main flux Analytique : détection de fraude en temps réel | Microsoft Azure"
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

Apprenez à créer une solution de bout en bout pour la détection de fraude en temps réel avec Azure flux Analytique. Importer des événements dans un concentrateur événement Azure, écrire des requêtes de flux Analytique d’agrégation ou d’alerte et envoyer les résultats à un récepteur de sortie de comprendre des données avec traitement en temps réel. Détection d’anomalie en temps réel pour télécommunications est couvert, mais la technique exemple est aussi bien d’autres types de détection de fraude tels que des scénarios vol carte bancaire ou identité.

Flux Analytique est un service entièrement géré qui offre un traitement de l’événement complexes faible latence, hautement disponible, scalable sur diffusion en continu de données dans le cloud. Pour plus d’informations, voir [présentation Azure flux Analytique](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scénario : Télécommunications et Assistant fraude la détection en temps réel

Une entreprise de télécommunications comporte un grand nombre de données pour les appels entrants. L’entreprise a besoin de ce qui suit à partir de ses données :
* Comparer ces données vers le bas jusqu'à un montant gérable et obtenir des informations sur l’utilisation du client dans le temps et les régions géographiques.
* Détecter fraude Assistant (plusieurs appels provenant de la même identité en même temps, mais à des endroits différents géographiquement) en temps réel afin qu’ils peuvent répondre facilement en avertir clients ou arrêter le service.

Dans les scénarios Internet des objets (IoT) canoniques il est une tonne de données de télémétrie ou capteur générée – et clients souhaitent agréger les ou d’alerte sur anomalies en temps réel.

## <a name="prerequisites"></a>Conditions préalables

- Téléchargez [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) à partir du centre de téléchargement Microsoft 
- Facultatif : Le code du Générateur d’événements à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator) Source

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Créer une entrée Azure événement Hubs et un groupe de fournisseurs

L’exemple d’application génère des événements et les pousser à une instance de l’événement concentrateur pour traitement en temps réel. Service Bus événement Hubs sont la méthode préférée de réception événement pour flux Analytique et vous pouvez en savoir plus sur événement Hubs dans la [documentation de Bus des services Azure](/documentation/services/service-bus/).

Pour créer un concentrateur événement :

1.  Cliquez sur **Nouveau**dans le [portail Azure](https://manage.windowsazure.com/)  > **Application Services** > **Bus des services** > **Événement concentrateur** > **Création rapide**. Fournir un nom, une région et un espace de noms nouvelle ou existante pour créer un nouveau concentrateur de l’événement.  
2.  Comme il est conseillé de lire la chaque tâche de flux de données Analytique d’un groupe de consommateur concentrateur événement unique. Nous vous guidera le processus de création d’un groupe de consommateurs ci-dessous, et vous pouvez [en savoir plus sur les consommateurs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de fournisseurs, atteindre le Hub événement nouvellement créé et cliquez sur l’onglet **Consommateur groupes** , puis cliquez sur **créer** en bas de la page et indiquez un nom pour votre groupe grand public.
3.  Pour accorder l’accès à l’événement concentrateur, nous nous avons besoin de créer une stratégie d’accès partagé.  Cliquez sur l’onglet **configuration** de votre plateforme d’événement.
4.  Sous **Stratégies d’accès partagés**, créez une nouvelle stratégie avec **Gérer** les autorisations.

    ![Partagé dans lequel vous pouvez créer une stratégie avec des autorisations de gérer les stratégies d’accès.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Cliquez sur **Enregistrer** dans la partie inférieure de la page.
6.  Accédez au **tableau de bord** et cliquez sur **Informations de connexion** dans la partie inférieure de la page, puis copiez et enregistrer les informations de connexion.

## <a name="configure-and-start-event-generator-application"></a>Configurer et démarrer l’application Générateur d’événements

Nous vous fournissons une application cliente qui génèrent des métadonnées appel entrant exemple et transmission à concentrateur de l’événement. Suivez les étapes ci-dessous pour configurer cette application.  

1.  Téléchargez le [fichier TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Puis décompressez dans un répertoire.

    **Remarque**: Windows peut se bloquer le fichier zip téléchargé. Cliquez avec le bouton droit sur le fichier et sélectionnez Propriétés. Si le message « ce fichier provenance d’un autre ordinateur et peut-être être bloqué pour mieux vous protéger cet ordinateur. » puis cochez la case « Débloquer », puis cliquez sur Appliquer dans le fichier zip.

2.  Remplacer les valeurs Microsoft.ServiceBus.ConnectionString et EventHubName dans **telcodatagen.exe.config** avec votre chaîne de connexion événement concentrateur et le nom.

    **Remarque**: la chaîne de connexion copiée à partir des emplacements portails Azure le nom de la connexion à la fin. N’oubliez pas de supprimer la « ; EntityPath =<value>« à partir de la touche Ajouter = champ.

3.  Démarrez l’application. L’utilisation est la suivante :

   telcodatagen.exe [#NumCDRsPerHour] [Assistant Carte fraude probabilité] [#DurationHours]

L’exemple suivant génère des événements 1000 avec une probabilité de 20 pour cent de fraude au cours des deux heures.

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


## <a name="create-stream-analytics-job"></a>Créer la tâche de flux de données Analytique
Maintenant que nous disposons d’un flux d’événements de télécommunications, nous pouvons configurer une tâche de flux de données Analytique pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Mise en service d’une tâche de flux de données Analytique

1.  Dans le portail Azure, cliquez sur **Nouveau > Data Services > flux Analytique > Création rapide**.
2.  Spécifiez les valeurs suivantes, puis cliquez sur **Créer un flux de travail Analytique**:

    * **Nom de la tâche**: entrez un nom de la tâche.

    * **Région**: sélectionnez la zone où vous souhaitez exécuter la tâche. Envisagez de placer le travail et le hub de l’événement dans la même région pour garantir de meilleures performances et pour garantir que vous pas ce mode de paiement transférer des données entre les régions.

    * **Compte de stockage**: sélectionnez le compte de stockage Azure que vous voulez utiliser pour stocker les données d’analyse pour toutes les tâches de flux de données Analytique qui s’exécutent dans cette zone. Vous avez la possibilité pour choisir un compte de stockage existant ou créez-en un.

3.  Dans le volet gauche pour répertorier les tâches de flux de données Analytique, cliquez sur **Flux Analytique** .

    ![Icône de flux de données Analytique service](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  La nouvelle tâche est affichée avec le statut **créé**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Vous devez configurer l’entrée de travail, la sortie et la requête avant de démarrer la tâche.

### <a name="specify-job-input"></a>Spécifier une entrée de travail
1.  Dans votre tâche de flux de données Analytique cliquez sur **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous aidera à un certain nombre de procédure pour configurer votre saisie.
2.  Sélectionnez le **flux de données**, puis cliquez sur le bouton droit.
3.  Sélectionnez **Hub de l’événement**, puis cliquez sur le bouton droit.
4.  Tapez ou sélectionnez les valeurs suivantes dans la troisième page :

    * **Alias d’entrée**: entrez un nom convivial pour cette tâche d’entrée comme *CallStream*. Notez que vous utiliserez ce nom dans la requête ultérieurement.
    * **Concentrateur événement**: si le Hub de l’événement que vous avez créé se trouve dans le même abonnement en tant que la tâche de flux de données Analytique, sélectionnez l’espace de noms figurant dans le hub de l’événement.

    Si votre plateforme d’événement se trouve dans un autre abonnement, sélectionnez **Utiliser événement Hub à partir d’un autre abonnement** et entrer manuellement les informations de **Service Bus Namespace**, **Événement concentrateur nom**, **Nom de la stratégie événement concentrateur**, **Événement concentrateur stratégie clé**et **Le nombre d’événements concentrateur Partition**.

    * **Nom de l’événement concentrateur**: sélectionnez le nom du concentrateur événement.

    * **Nom de la stratégie concentrateur événement**: sélectionnez la stratégie d’événement concentrateur créée précédemment dans ce didacticiel.

    * **Groupe des consommateurs concentrateur événement**: entrez le nom du groupe consommateur créé précédemment dans ce didacticiel.
5.  Cliquez sur le bouton droit.
6.  Spécifiez les valeurs suivantes :

    * **Format d’événement sérialiseur**: JSON
    * **Codage**: UTF8
7.  Cliquez sur le bouton de vérification pour ajouter cette source et vérifier que flux Analytique peut se connecter au hub de l’événement.

### <a name="specify-job-query"></a>Spécifier la requête de travail

Flux Analytique prend en charge un modèle de requête simple et déclarative pour décrire les transformations pour traitement en temps réel. Pour en savoir plus sur la langue, voir la [Référence du langage de requête Azure flux Analytique](https://msdn.microsoft.com/library/dn834998.aspx). Ce didacticiel vous aidera à créer et tester plusieurs requêtes via votre flux de données de l’appel en temps réel.

#### <a name="optional-sample-input-data"></a>Facultatifs : Exemples de données d’entrée
Pour valider votre requête sur des données de travail réel, vous pouvez utiliser la fonctionnalité **d’Exemples de données** pour extraire les événements à partir de votre flux de données et créer un. JSON fichier des événements de test.  Les étapes suivantes montrent comment effectuer cette opération et nous avons également fourni un exemple de fichier [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) des fins de test.

1.  Sélectionnez votre événement concentrateur une entrée, puis cliquez sur **Exemples de données** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’affiche, spécifiez une **Heure de début** pour démarrer la collecte de données à partir d’et une **durée** pour la quantité de données supplémentaire consommer.
3.  Cliquez sur le bouton de vérification pour démarrer des données d’échantillonnage à partir de l’entrée.  Il peut prendre une minute ou deux pour le fichier de données devant être produites.  Lorsque le processus est terminé, cliquez sur **Détails** et téléchargez et enregistrez la. Fichier JSON est générée.

    ![Télécharger et enregistrer des données traitées dans un fichier JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Requête relais

Si vous souhaitez archiver tous les événements, vous pouvez utiliser une requête relais à lire tous les champs de la charge utile de l’événement ou le message. Commencez par faire une requête simple relais que tous les champs dans un événement les projets.

1.  Cliquez sur la **requête** à partir du haut de la page de tâche de flux Analytique.
2.  Ajoutez les éléments suivants à l’éditeur de code :

        SELECT * FROM CallStream

    > Vérifiez que le nom de la source d’entrée correspond au nom de l’entrée que vous avez spécifié précédemment.

3.  Sous l’éditeur de requête, cliquez sur **tester** .
4.  Fournissez un fichier de test, soit celui que vous avez créé à l’aide de la procédure précédente ou utilisez [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Cliquez sur le bouton vérifier et consultez les résultats affichés en dessous de la définition de la requête.

    ![Résultats de la définition de requête](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projection de colonne

Nous allons maintenant raccourcir les champs renvoyés un plus petit ensemble.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Sortie dans l’éditeur de requête.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Nombre d’entrant appelle par région : fenêtre qui s’écroulent avec une agrégation

Pour comparer le volume que les appels entrants par région nous allons exploiter un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre d’appels entrants regroupés par SwitchNum toutes les 5 secondes.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Cette requête utilise le mot-clé **Horodatage par** pour spécifier un champ d’horodatage dans la charge utile pour être utilisé dans le calcul temporel. Si ce champ n’a pas été spécifié, l’opération windowing serait réalisée à l’aide de l’heure de que chaque événement est arrivé dans le Hub de l’événement. Voir [« Arrivée Vs Application heure » dans le flux de données Analytique de référence du langage de requête](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Notez que vous pouvez accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp** .

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Résultats de la requête pour Timestand par](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Détection de fraude Assistant avec une jointure réflexive

Pour identifier une utilisation potentiellement frauduleuse, nous allons examiner pour les appels provenant de l’utilisateur même mais à différents emplacements en moins de 5 secondes.  Nous [rejoindre](https://msdn.microsoft.com/library/azure/dn835026.aspx) le flux d’événements d’appel avec lui-même pour vérifier dans ce cas.

1.  Modifier la requête dans l’éditeur de code pour :

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Cliquez sur **réexécuter** sous l’éditeur de requête pour afficher les résultats de la requête.

    ![Résultats de la requête d’une jointure](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Créer récepteur de sortie

Maintenant que nous avons défini un flux d’événements, un concentrateur événement d’entrée pour ajouter des événements et une requête pour effectuer une transformation via le flux, la dernière étape consiste à définir un récepteur de sortie pour la tâche.  Nous allons écrire des événements pour comportement frauduleux au stockage d’objets Blob.

Suivez les étapes ci-dessous pour créer un conteneur pour le stockage Blob si vous n’en avez pas déjà.

1.  Utiliser un compte de stockage existant ou créer un nouveau compte de stockage en cliquant sur **Nouveau > DATA SERVICES > stockage > Création rapide** et en suivant les instructions.
2.  Sélectionnez le compte de stockage et cliquez sur **conteneurs** en haut de la page, puis cliquez sur **Ajouter**.
3.  Attribuez un **nom** à votre conteneur et définir son **accès** au Public Blob.

## <a name="specify-job-output"></a>Spécifier la sortie des tâches

1.  Dans votre tâche de flux de données Analytique cliquez sur **sortie** à partir du haut de la page, puis cliquez sur **Ajouter une sortie**. La boîte de dialogue qui s’ouvre vous aidera à un certain nombre de procédure pour configurer votre sortie.
2.  Sélectionnez **Stockage BLOB**, puis cliquez sur le bouton droit.
3.  Tapez ou sélectionnez les valeurs suivantes dans la troisième page :

    * **ALIAS de sortie**: entrez un nom convivial pour cette sortie des tâches.
    * **Abonnement**: si le stockage Blob que vous avez créé se trouve dans le même abonnement en tant que la tâche de flux de données Analytique, sélectionnez **Utiliser le compte de stockage d’abonnement actif**. Si votre espace de stockage se trouve dans un autre abonnement, sélectionnez **Utiliser le compte de stockage à partir d’un autre abonnement** et entrer manuellement les informations de **Compte de stockage**, **Clé de compte de stockage**, **conteneur**.
    * **Compte de stockage**: sélectionnez le nom du compte de stockage.
    * **Conteneur**: sélectionnez le nom du conteneur.
    * **Nom de fichier préfixe**: Type dans un préfixe de fichier à utiliser lors de l’écriture de sortie blob.

4.  Cliquez sur le bouton droit.
5.  Spécifiez les valeurs suivantes :

    * **FORMAT d’événement sérialiseur**: JSON
    * **Codage**: UTF8

6.  Cliquez sur le bouton de vérification pour ajouter cette source et vérifiez que flux Analytique pouvez vous connecter pour le compte de stockage.

## <a name="start-job-for-real-time-processing"></a>Démarrer le travail pour traitement en temps réel

Dans la mesure où une entrée de travail, requête et de sortie ont été spécifiés, nous sommes prêts à démarrer la tâche de flux de données Analytique pour la détection de fraude en temps réel.

1.  Dans le **tableau de bord**de travail, cliquez sur **Démarrer** dans la partie inférieure de la page.
2.  Dans la boîte de dialogue qui s’affiche, sélectionnez **l’heure de début de la tâche** , puis sur le bouton de vérification en bas de la boîte de dialogue. L’état du travail met en **commençant** et se déplace peu de temps en cours **d’exécution**.

## <a name="view-fraud-detection-output"></a>Affichage fraude détection sortie

Utilisez un outil comme [Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) pour afficher les événements frauduleux qu’ils sont écrits dans votre sortie en temps réel.  

![Détection de fraude : événements frauduleux affichés en temps réel](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
