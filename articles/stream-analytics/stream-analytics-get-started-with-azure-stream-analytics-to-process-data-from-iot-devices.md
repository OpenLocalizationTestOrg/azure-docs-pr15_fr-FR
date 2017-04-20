<properties
    pageTitle="Prise en main Azure flux Analytique pour traiter les données à partir d’appareils IoT. | Microsoft Azure"
    description="Flux de balises et les données de capteur avec analytique de flux de données et le traitement des données en temps réel IoT"
    keywords="solution IOT, prise en main iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Prise en main Azure flux Analytique pour traiter les données à partir d’appareils IoT

Dans ce didacticiel, vous allez apprendre à créer une logique de traitement de flux de données pour collecter des données à partir d’appareils Internet des objets (IoT). Nous allons utiliser un cas d’utilisation réel, Internet des objets (IoT) pour vous montrer comment créer votre solution rapidement et économique.

## <a name="prerequisites"></a>Conditions préalables

-   [Abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Exemple requête fichiers de données et être téléchargés à partir de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scénario

Contoso, qui est une société dans l’espace d’automatisation industriels, a automatisé complètement son processus de fabrication. La machine dans cette usine a capteurs qui sont en mesure d’émission de flux de données en temps réel. Dans ce scénario, un responsable de plancher fabrication souhaite perspectives en temps réel à partir des données capteur pour rechercher des motifs et effectuer des actions sur les. Nous allons utiliser le langage de requête Analytique flux (SAQL) sur les données de capteur pour rechercher des modèles intéressantes à partir du flux de données entrant.

Voici les données sont générées à partir d’un appareil Texas Instruments capteur balise.

![Indicateur de capteur Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

La charge utile des données est au format JSON et ressemble à ceci :


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Dans un scénario réel, vous pourriez avoir des centaines de ces capteurs générer des événements en tant que flux. Dans l’idéal, une passerelle serait exécuter du code pour étendre ces événements vers [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/) ou [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Votre tâche de flux de données Analytique serait ces événements à partir de l’événement Hubs d’acquisition et exécuter des requêtes analytique en temps réel contre les flux de données. Ensuite, vous pouvez envoyer les résultats à un de la [prise en charge des sorties](stream-analytics-define-outputs.md).

Pour faciliter leur utilisation, ce guide de mise en route fournit un exemple de fichier de données ont été capturée les dispositifs de balise mesure réel. Vous pouvez exécuter des requêtes dans les exemples de données et afficher les résultats. Dans les didacticiels suivants, vous allez apprendre à vous connecter votre travail aux entrées et sorties et les déployez sur le service Azure.

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

1. Dans le [portail Azure](http://portal.azure.com), cliquez sur le signe plus et tapez **Les flux de données d’analyse** dans la fenêtre de texte à droite. Sélectionnez ensuite **Analytique de flux de travail** dans la liste des résultats.

    ![Créer une nouvelle tâche de flux de données Analytique](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Entrez un nom unique de la tâche et vérifiez que l’abonnement est correcte pour votre projet. Puis créer un nouveau groupe de ressources ou sélectionnez-en un dans votre abonnement.

3. Sélectionnez un emplacement pour votre projet. Transfert de sélection au même emplacement que le groupe de ressources et le compte de stockage initial est recommandé pour la vitesse de traitement et réduire les coûts dans les données.

    ![Créer une nouvelle tâche de flux de données Analytique détails](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Vous devez créer ce compte de stockage qu’une seule fois par région. Ce stockage est partagé sur toutes les tâches de flux Analytique qui sont créés dans cette zone.

4. Cochez la case pour placer votre travail dans votre tableau de bord, puis cliquez sur **créer**.

    ![Création d’une tâche en cours](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Vous devez voir un « déploiement démarré... » affiché dans l’angle supérieur droit de la fenêtre du navigateur. Dès qu’il remplacera par une fenêtre complète comme illustré ci-dessous.

    ![Création d’une tâche en cours](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Créer une requête Analytique de flux de données Azure

Une fois votre travail est créé, il est temps pour l’ouvrir et créer une requête. Vous pouvez facilement accéder à votre travail en cliquant sur la vignette pour qu’elle.

![Vignette de travail](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Dans le volet de **La topologie de travail** , cliquez sur la zone de **requête** pour accéder à l’éditeur de requête. L’éditeur de **requête** vous permet d’entrer une requête T-SQL qui exécute la transformation sur les données d’événement entrants.

![Zone de requête](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Requête : Archiver vos données brutes

La forme la plus simple de requête est une requête SQL directe qui archive toutes les données entrées à sa sortie désigné. Téléchargez l’exemple de fichier de données à partir de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) vers un emplacement sur votre ordinateur. 

1. Collez la requête à partir du fichier PassThrough.txt. 

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Cliquez sur les points de suspension en regard de votre entrée et activez **télécharger des exemples de données à partir du fichier** .

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Un volet s’ouvre sur la droite en conséquence, en il sélectionnez le fichier de données HelloWorldASA InputStream.json à partir de votre emplacement téléchargé, cliquez sur **OK** au bas du volet.

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Cliquez sur l’engrenage **tester** dans le coin supérieur gauche de la fenêtre, puis traiter votre requête de test dans l’exemple de dataset. Une fenêtre de résultats s’ouvre en dessous de votre requête comme le traitement est terminé.

    ![Résultats des tests](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Requête : Filtrer les données selon une condition

Essayer filtrer les résultats en fonction d’une condition. Nous souhaitons afficher les résultats pour que les événements qui proviennent de « sensorA. » La requête se trouve dans le fichier Filtering.txt.

![Filtrage d’un flux de données](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Notez que la requête qui respecte la casse compare une valeur de chaîne. Cliquez sur l’engrenage **Test** à nouveau pour exécuter la requête. La requête doit renvoyer 389 lignes en dehors d’événements 1860.

![Résultat de la deuxième les résultats de test de requête](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Requête : Alerte pour déclencher la lecture d’un flux de travail d’entreprise

Nous allons créer notre requête plus détaillée. Pour chaque type de cellule, nous voulons surveillance température moyenne par de 30 secondes et afficher des résultats uniquement si la température moyenne est supérieure à 100 degrés. Nous écrire la requête suivante et puis cliquez sur **Test** pour afficher les résultats. La requête se trouve dans le fichier ThresholdAlerting.txt.

![requête de filtre de 30 secondes](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Vous devez maintenant voir les résultats qui contiennent des lignes et des noms de capteurs uniquement 245 dont la moyenne tempérée est supérieure à 100. Cette requête regroupe le flux d’événements par **dspl**, qui est le nom du capteur, sur une **Fenêtre de la distribution automatique** de 30 secondes. Requêtes temporelles doivent indiquer comment nous voulons au progrès. À l’aide de la clause **Horodatage BY** , nous avons spécifié la colonne **OUTPUTTIME** pour associer des heures à tous les calculs temporelles. Pour plus d’informations, lisez les articles MSDN sur la [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et de [fonctions multifenêtre](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Requête : Détecter absence d’événements

Comment nous pouvons pour écrire une requête pour rechercher un manque d’événements d’entrée ? Nous allons rechercher la dernière fois qu’un capteur envoyé des données et puis n’a pas envoyé événements pour la minute qui suit. La requête se trouve dans le fichier AbsenseOfEvent.txt.

![Détecter absence d’événements](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Ici, nous utilisons une jointure **Externe gauche** pour le même flux de données (jointure réflexive). Pour une jointure **interne** , un résultat est retourné uniquement lorsqu’une correspondance est trouvée.  Pour une jointure **Externe gauche** , si un événement du côté gauche de la jointure est sans correspondance, une ligne qui comporte la valeur NULL pour toutes les colonnes du côté droit est renvoyée. Cette technique est très utile pour vérifier l’absence d’événements. Consultez notre documentation MSDN pour plus d’informations sur la [jointure](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusion

L’objectif de ce didacticiel consiste à vous montrer comment écrire des requêtes de langage de requête Analytique flux différents et afficher les résultats dans le navigateur. Toutefois, il est uniquement prise en main. Vous pouvez effectuer bien plus encore avec Analytique de flux de données. Flux Analytique prend en charge les diverses entrées et sorties et peut même utiliser les fonctions dans l’apprentissage automatique Azure pour le convertir en un outil puissant d’analyse de flux de données. Vous pouvez commencer à Explorer plus sur les flux Analytique à l’aide de notre [plan d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Pour plus d’informations sur l’écriture de requêtes, consultez l’article à propos [des modèles de requête communs](./stream-analytics-stream-analytics-query-patterns.md).
