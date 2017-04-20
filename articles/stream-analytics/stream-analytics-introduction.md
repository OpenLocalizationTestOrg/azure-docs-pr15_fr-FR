<properties 
    pageTitle="Présentation des flux de données Analytique | Microsoft Azure" 
    description="Découvrez les flux Analytique, un service géré qui vous permet d’analyser des données en continu à partir de l’Internet des objets (IoT) en temps réel." 
    keywords="analytique comme un service, code managé services, le traitement de flux, streaming analytique, qu’est analytique de flux de données"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>Quelles sont les flux Analytique ?

Azure Analytique de flux est un moteur de traitement des événements en temps réel entièrement managé et rentable qui permet de déverrouiller une analyse approfondie à partir des données. Flux Analytique facilite la configurer de calculs analytiques en temps réel sur des données en continu à partir d’appareils, capteurs, sites web, médias sociaux, des applications, systèmes d’infrastructure et plus.

En quelques clics dans le portail Azure, vous pouvez créer une tâche de flux de données Analytique spécifiant la source d’entrée des données en continu, le récepteur de sortie pour les résultats de votre travail, et une transformation de données exprimés en un langage similaire à SQL. Vous pouvez surveiller et ajuster l’échelle/vitesse de votre travail dans le portail Azure à l’échelle de quelques kilo-octets au moins un gigaoctet des événements traités par seconde.

Flux Analytique exploite années de travail Microsoft Research de développement hautement réglé diffusion en continu de moteurs pour traitement de durée de vie critique, ainsi que les intégrations d’entreprise langue pour les spécifications intuitives de ces.

## <a name="what-can-i-use-stream-analytics-for"></a>Que puis-je utiliser Analytique flux pour ?
Grandes quantités de données sont transmis à haute vitesse via le câble aujourd'hui. Les organisations qui peuvent traiter et agir sur ces données en temps réel en continu peuvent considérablement améliorer l’efficacité et se différencier sur le marché. Scénarios d’analytique en continu en temps réel, vous pouvez trouver dans tous les secteurs : analyse cotation stock personnalisée et en temps réel et les alertes offertes par sociétés financières services ; détection de fraude en temps réel. services de protection des données et des identités ; réception fiable et analyse des données générées par capteurs et cylindres incorporés dans des objets physiques (Internet des objets ou IoT) ; analytique de parcours Web ; et applications de gestion des relations client émettre des alertes lors de l’expérience client au sein d’un intervalle de temps est détérioré. Entreprises recherchent la plus flexible, fiable et économique permettent d’effectuer ce analyse des données en temps réel de flux d’événements pour réussir dans le monde moderne concurrence.

## <a name="key-capabilities-and-benefits"></a>Principales fonctionnalités et avantages
-   **Facilité d’utilisation :** Flux Analytique prend en charge un modèle de requête simple et déclarative pour décrire les transformations. Afin d’optimiser pour faciliter leur utilisation, flux Analytique utilise une variante T-SQL et supprime la nécessité de clients de traiter les opérations techniques complexes de systèmes de traitement de flux. À l’aide du [langage de requête Analytique de flux de données](https://msdn.microsoft.com/library/azure/dn834998.aspx) dans l’éditeur de requête dans le navigateur, vous obtenez bon sens intelligente semi-automatique afin de pouvoir rapidement et facilement implémenter temps série requêtes, notamment temporel basée sur les jointures, agrégats dans une fenêtre, filtres temporelles et autres opérations courantes telles que les jointures, agrégats, projections et filtres. En outre, test par rapport à un fichier de données d’exemple de requête dans le navigateur permet un développement itératif rapide.  

-   **Extensibilité élevées :** Flux de données Analytique est capable de gérer débit événements élevées de jusqu'à 1 Go/seconde. Intégration à [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/) et [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) laisser la solution millions d’événements par seconde provenant des appareils connectés, visites, d’acquisition et fichiers journaux, pour ne citer. Pour ce faire, flux Analytique s’appuie sur la fonctionnalité partition d’événement Hubs, qui génèrent 1 Mo/s par partition. Les utilisateurs peuvent partition le calcul en plusieurs étapes logiques au sein de la définition de requête, chacune avec la fonctionnalité permettant d’être plus partition pour augmenter extensibilité élevées.  

-   **Répétabilité, la fiabilité et la restauration rapide :** Un service géré dans le cloud, flux Analytique permet d’éviter les pertes de données et fournit continuité des activités en cas de panne grâce aux fonctions de récupération intégrées. La possibilité pour maintenir en interne l’état, le service fournit les résultats répétitifs garantissant qu'il est possible d’archiver les événements et réappliquer le traitement à l’avenir, toujours obtenir le même résultat. Cela permet aux clients de revenir en arrière et examinez les calculs lors d’une analyse en profondeur, analyse de scénarios, etc..  

-   **Économique :** Comme un service cloud, flux Analytique est optimisé pour fournir aux utilisateurs un coût très faible pour commencer et gérer des solutions analytique en temps réel. Le service est conçu pour vous payer fur en fonction de l’utilisation de la diffusion unité et la quantité de données traitées par le système. L’utilisation est dérivée en fonction du volume d’événements traités et la quantité de cluster power sa mise en service au sein du cluster pour gérer les tâches de flux Analytique correspondantes.  

-   **Référence à des données :** Flux Analytique fournit aux utilisateurs la possibilité de spécifier et d’utiliser des données de référence. Cela peut être données historiques ou données simplement non continue qui changent moins fréquemment au fil du temps. Le système simplifie l’utilisation des données de référence pour être traité comme n’importe quel autre flux événement entrant pour participer avec d’autres flux d’événements ingérées en temps réel pour effectuer des transformations.  

-   **Fonctions définies par l’utilisateur :** Flux Analytique est intégré à Azure Machine apprentissage pour définir des appels de fonction dans le service d’apprentissage automatique dans le cadre d’une requête Analytique de flux de données. Cela développe les capacités de flux Analytique pour tirer parti des solutions apprentissage automatique Azure existantes. Pour plus d’informations sur ce problème, veuillez consulter le [didacticiel de l’intégration d’apprentissage automatique](stream-analytics-machine-learning-integration-tutorial.md).

-   **Connectivité :** Flux Analytique se connecte directement à Azure événement Hubs et Azure IoT Hubs pour la réception de flux de données et le service de Blob Azure pour ajouter des données historiques. Résultats peuvent être écrites à partir de flux Analytique Azure stockage BLOB ou Tables, base de données SQL Azure, Azure données Lake Stores, DocumentDB, Hubs événement, Azure Service Bus rubriques ou files d’attente et Power BI, où il puis relativement, plus traitée par des flux de travail, utilisables dans analytique lot via [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) ou nouveau traité comme une série d’événements. Lorsque vous utilisez Hubs d’événement, il est possible composer plusieurs flux Analytique, ainsi que d’autres sources de données et les moteurs de traitement sans perdre la diffusion en continu nature des calculs.  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez été abordé flux Analytique, un service de diffusion en continu analytique sur des données à partir de l’Internet des objets géré. Pour en savoir plus sur ce service, voir :

- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

