<properties
    pageTitle="Mettre à l’échelle votre tâche de flux Analytique avec les fonctions d’apprentissage automatique Azure | Microsoft Azure"
    description="Découvrez comment mettre à l’échelle correctement les tâches de flux de données Analytique (partition, quantité so, etc.) lors de l’utilisation des fonctions d’apprentissage automatique Azure."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Mettre à l’échelle votre tâche de flux Analytique avec les fonctions d’apprentissage automatique Azure

Il est souvent facile à configurer une tâche de flux de données Analytique et parcourez des exemples de données. Que dois-je faire quand nous avons besoin de l’exécution de cette tâche avec le volume de données plus élevé ? Il nous oblige à mieux comprendre comment configurer la tâche de flux de données Analytique afin qu’il mettra à l’échelle. Dans ce document, nous allons étudier les aspects particuliers de mise à l’échelle des travaux Analytique de flux avec les fonctions d’apprentissage automatique. Pour plus d’informations sur la façon de mettre à l’échelle de tâches de flux de données Analytique en général voir l’article [travaux de mise à l’échelle](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Qu’est une fonction d’apprentissage automatique Azure dans flux Analytique ?

Une fonction d’apprentissage automatique dans Analytique flux peut être utilisée comme un appel de fonction normal dans le langage de requête Analytique de flux de données. Toutefois, derrière la scène, les appels de fonction sont en réalité des demandes de Azure Machine apprentissage Web Service. Services web de formation ordinateur prend en charge « le traitement par lots » plusieurs lignes, qui est appelé par lots mini, dans l’appel même API de service web, à améliorer les performances globales. Voir les articles suivants pour plus d’informations ; [Fonctions d’apprentissage automatique Azure dans flux Analytique](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) et [Azure Machine Learning Web Services](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurer une tâche de flux de données Analytique avec les fonctions d’apprentissage automatique

Lorsque vous configurez une fonction d’apprentissage automatique pour une tâche de flux de données Analytique, il existe deux paramètres à prendre en considération, la taille du lot des appels de fonction d’apprentissage automatique et les unités de diffusion en continu (SUs) mis en service pour la tâche de flux de données Analytique. Pour déterminer les valeurs appropriées dans ce cas, tout d’abord une décision doit être effectuée entre latence et débit, autrement dit, latence de la tâche de flux de données Analytique, débit de chaque so. SUs peuvent toujours être ajoutés à un projet pour augmenter le débit d’une requête de flux Analytique bien partitionnée, bien que SUs supplémentaires augmente le coût de l’exécution de la tâche.

Par conséquent, il est important de déterminer la *tolérance* de latence dans l’exécution d’une tâche de flux de données Analytique. Latence supplémentaire d’exécuter des demandes de service d’apprentissage automatique Azure augmente naturellement avec une taille de lot, qui est composée de la latence de la tâche de flux de données Analytique. En revanche, augmentation de la taille lot permet de la tâche de flux Analytique traiter les *des événements supplémentaires avec la *même nombre * des demandes de service web apprentissage automatique. L’augmentation de la latence de service web apprentissage automatique est souvent secondaire linéaire à l’augmentation de la taille du lot aussi est-il important de tenir compte de la taille du lot plus économique d’un service web apprentissage automatique dans une situation donnée. La taille du lot par défaut pour les demandes de service web est de 1 000 et peut-être être modifiée en utilisant l' [API REST d’Analytique flux](https://msdn.microsoft.com/library/mt653706.aspx "API REST d’Analytique flux") ou le [client de PowerShell pour flux Analytique](stream-analytics-monitor-and-manage-jobs-use-powershell.md "client PowerShell pour flux Analytique").

Une fois qu’une taille de lot a été déterminée, la quantité d’unités de diffusion en continu (SUs) peut être déterminée, en fonction du nombre d’événements que la fonction doit traiter par seconde. Pour plus d’informations sur les unités de diffusion en continu, consultez l’article [tâches de flux de données Analytique échelle](stream-analytics-scale-jobs.md#configuring-streaming-units).

En règle générale, il existe 20 connexions simultanées au service web apprentissage automatique pour chaque 6 SUs, sauf que les travaux so 1 et 3 tâches so obtiendrez 20 connexions simultanées également.  Par exemple, si le taux d’entrée de données est de 200 000 événements par seconde et la taille du lot est de gauche à la valeur par défaut de 1000 la latence du service web qui en résulte avec lot mini 1000 événements est de 200 ms. Cela signifie que chaque connexion peut envoyer des 5 demandes au service web apprentissage automatique dans une seconde. Avec 20 connexions, la tâche de flux de données Analytique peut traiter 20 000 événements dans 200 ms et par conséquent 100 000 dans une seconde. Pour traiter les 200 000 événements par seconde, la tâche de flux de données Analytique doit donc 40 connexions simultanées, qui est fourni de 12 SUs. L’illustration ci-dessous montre les requêtes à partir de la tâche de flux de données Analytique au point de terminaison de service web apprentissage automatique – chaque 6 SUs avec 20 connexions simultanées au service web apprentissage automatique en max.

![Échelle de flux Analytique avec l’exemple de tâche Machine Learning fonctions 2] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Échelle de flux Analytique avec l’exemple de tâche Machine Learning fonctions 2")

En règle générale, ***B*** pour taille du lot, ***L*** pour la latence du service web en taille du lot B en millisecondes, le débit d’une tâche de flux de données Analytique avec SUs ***N*** est la suivante :

![Mettre à l’échelle Analytique de flux avec apprentissage formule de fonctions de l’ordinateur] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Mettre à l’échelle Analytique de flux avec apprentissage formule de fonctions de l’ordinateur")

Un compte supplémentaire peut être « appels simultanées max » sur le côté de service web apprentissage automatique, il est recommandé de lui pour attribuer la valeur maximale (200 actuellement).

Pour plus d’informations sur ce texte, paramètre consulter l' [article de mise à l’échelle pour les Services Web de formation Machine](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Exemple : analyse opinion

L’exemple suivant inclut une tâche de flux de données Analytique avec l’analyse opinion fonction d’apprentissage automatique, comme décrit dans le [didacticiel de l’intégration de flux Analytique Machine apprentissage](stream-analytics-machine-learning-integration-tutorial.md).

La requête est une requête simple entièrement partitionnée suivie de la fonction **opinion** , comme indiqué ci-dessous :

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Considérez le scénario suivant ; avec un débit de 10 000 tweets par seconde une tâche de flux de données Analytique doit être créée pour effectuer une analyse opinion des tweets (événements). À l’aide de 1 so, cette tâche de flux de données Analytique serait en mesure de traiter le trafic ? À l’aide de la taille du lot par défaut de 1000 le travail doit être en mesure de répondre à l’entrée. Davantage la fonction d’apprentissage automatique ajoutée doit générer pas plus de seconde de latence, ce qui est le général latence par défaut de l’analyse opinion service web apprentissage automatique (avec une taille de lot par défaut de 1000). Latence **générale** ou de bout en bout de la tâche de flux de données Analytique serait généralement quelques secondes. Examinons à présent plus détaillée dans cette tâche de flux Analytique, *notamment* l’apprentissage appels de la fonction de l’ordinateur. La taille du lot comme 1000, un débit de 10 000 événements prendra environ 10 demandes de service web. Même avec 1 so, il existe suffisamment connexions simultanées pour s’adapter à ce trafic d’entrée.

Mais que se passe-t-il si le taux d’événement d’entrée augmente de x 100 et maintenant le travail Analytique de flux de données a besoin pour traiter 1 000 000 tweets par seconde ? Il existe deux possibilités :

1.  Augmenter la taille du lot, ou
2.  Partition du flux d’entrée pour traiter les événements en parallèle

Avec la première option, la tâche **latence** augmente.

Avec la deuxième option, SUs plus devront être mis en service et par conséquent générer plus simultanées demandes de service web apprentissage automatique. Cela signifie que le projet **coût** augmente.


Supposons que la latence de l’analyse opinion service web apprentissage automatique est de 200 ms pour 1000 événement lots ou en dessous, 250ms des lots de 5 000 événements, de 300 MS pour des lots de 10 000 événement ou 500 ms pour lots d’événements de 25 000.

1. À l’aide de la première option, (**pas** plus SUs de mise en service), la taille du lot peut être augmentée à **25 000**. Cela permet à son tour le la tâche traiter les 1 000 000 événements avec 20 connexions simultanées au service web apprentissage automatique (avec une latence de 500 ms par appel). La latence supplémentaire de la tâche de flux Analytique en raison des demandes de fonction opinion par rapport à la demande de service web apprentissage automatique serait donc être augmentée de **200 ms** à **500 ms**. Toutefois, notez que lot taille **ne peut pas** être infini accrue comme les services web apprentissage automatique requiert la taille de la charge utile d’une demande de 4 Mo ou plus petit délai de demande de service web après 100 secondes d’opération.
2. À l’aide de la deuxième option, la taille du lot est laissée à 1 000, avec une latence de service web 200 ms, chaque 20 connexions simultanées au service web peuvent être en mesure de traiter 1000 événements *20* 5 = 100000 de seconde. Donc pour traiter les 1 000 000 événements par seconde, le travail devez 60 SUs. Par rapport à la première option, tâche de flux de données Analytique serait plus demandes de traitement par lots de service web, à son tour générer une augmentation des coûts.

Voici un tableau pour le débit de la tâche de flux de données Analytique pour différents SUs et tailles de lots (en nombre d’événements par seconde).

| taille du lot (latence ML)  | 500 (200 ms) | 1 000 (200 ms) | 5 000 (250ms) | 10 000 (de 300 MS) | 25 000 (500 ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SO** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **3 SUs** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **6 SUs** | 2 500 | 5 000 | 20 000 | 30 000 | 50 000 |
| **12 SUs** | 5 000 | 10 000 | 40 000 | 60 000 | 100 000 |
| **18 SUs** | 7 500 | 15 000 | 60 000 | 90 000 | 150 000 |
| **24 SUs** | 10 000 | 20 000 | 80 000 | 120 000 | 200 000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25 000 | 50 000 | 200 000 | 300 000 | 500 000 |

À présent, vous disposez déjà d’une bonne compréhension du fonctionnement des fonctions d’apprentissage automatique dans flux Analytique. Vous probablement également comprenez que flux Analytique travaux « extraire » des données de sources de données et chaque « extraire » renvoie un lot d’événements pour la tâche de flux Analytique à traiter. Comment cet impact de modèle extraire l’apprentissage Machine web des demandes de service ?

En règle générale, la taille du lot que nous définir pour les fonctions d’apprentissage automatique ne sont pas exactement être divisible par le nombre d’événements retourné par chaque tâche de flux de données Analytique « extraire ». Dans ce cas que le service web apprentissage automatique est appelé avec des lots de « partielles ». Cela pour ne pas subir une charge latence tâche supplémentaire dans les événements fusion d’extraction à extraire.

## <a name="new-function-related-monitoring-metrics"></a>Nouvelles métriques de surveillance liés à la fonction

Dans la zone moniteur d’une tâche de flux de données Analytique, trois indicateurs liés à la fonction supplémentaires ont été ajoutées. Ils sont demandes de fonctions, les événements de la fonction et les demandes de fonction a échoué, comme indiqué dans l’illustration ci-dessous.

![Mettre à l’échelle Analytique de flux avec apprentissage métriques de fonctions de l’ordinateur] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Mettre à l’échelle Analytique de flux avec apprentissage métriques de fonctions de l’ordinateur")

Sont définies comme suit :

**Fonction demandes**: le nombre de requêtes de fonction.

**Fonction événements**: le nombre d’événements contenus dans les requêtes de fonction.

**Échec de la fonction demandes**: le nombre de requêtes de fonction qui a échoué.

## <a name="key-takeaways"></a>Points clés  

Pour résumer les principaux points, afin de mettre à l’échelle une tâche de flux de données Analytique avec les fonctions d’apprentissage automatique, les éléments suivants doivent être considérées :

1.  Le taux d’événement d’entrée
2.  La latence autorisée pour la tâche de flux de données Analytique en cours d’exécution (et donc la taille du lot des demandes de service web apprentissage automatique)
3.  Le SUs Analytique flux généré et le nombre de demandes de service web apprentissage automatique (les liés à la fonction frais supplémentaires)

Une requête Analytique flux entièrement partitionnée a été utilisée comme exemple. Si vous avez besoin d’une requête plus complexe le [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) est très utile pour obtenir de l’aide supplémentaire de l’équipe du flux de données Analytique.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les flux Analytique, voir :

- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
