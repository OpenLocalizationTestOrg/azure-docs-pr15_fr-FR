<properties
    pageTitle="Ajouter une entrée de données pour vos tâches de flux Analytique | Microsoft Azure"
    description="Découvrez comment se connecter à une source de données à votre tâche de flux Analytique en tant que la diffusion en continu de saisie de données à partir des données Hubs événement ou une référence à partir du stockage de Blog."
    keywords="données d’entrée, diffusion en continu de données"
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


# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Ajouter un flux d’entrée ou une référence de données à une tâche de flux de données Analytique

Découvrez comment se connecter à une source de données à votre travail flux Analytique en tant que la diffusion en continu de saisie de données à partir des données depuis le stockage Blob Hubs événement ou une référence.

Tâches de flux Analytique Azure pouvant être connectés à l’entrée de données ou plus, chacun d'entre eux définir une connexion à une source de données existante. Comme les données sont envoyées à la source de données, il est utilisé par la tâche de flux de données Analytique et traité en temps réel en tant que flux de données. Flux Analytique a première classe intégration à [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/) et [stockage d’objets Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) à l’intérieur et en dehors de l’abonnement de la tâche.

Cet article est une étape dans [flux de données Analytique rubriques d’apprentissage](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrée de données : flux de données et données de référence

Il existe deux types d’entrées dans les flux Analytique : flux de données et les données de référence.

- **Flux de données**: tâches de flux de données Analytique doivent inclure l’entrée de flux de données au moins une pour être consommées et transformées par la tâche. Stockage d’objets Blob Azure et de Azure événement Hubs sont prises en charge en tant que sources d’entrée de flux de données. Azure Hubs événement sont utilisés pour collecter les flux d’événements des appareils connectés, services et applications. Stockage d’objets Blob Azure peut servir comme source d’entrée pour traiter les données en bloc sous forme de flux.  
- **Données de référence**: flux Analytique prend en charge un deuxième type de données de référence appelée d’entrée auxiliaire.  Contrairement à des données en marche, ces données sont statique ou ralentissement modification.  Il est généralement utilisé pour effectuer des recherches et corrélations avec des flux de données pour créer un jeu de données plus riche.  Stockage d’objets Blob Azure est actuellement la seule source d’entrée pris en charge pour les données de référence.  

Pour ajouter une entrée à votre tâche de flux de données Analytique :

1. Dans le portail Azure **entrées** , puis cliquez sur **Ajouter une entrée** dans votre tâche de flux de données Analytique.

    ![Azure portal classique - ajouter une entrée.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  

    Dans le portail Azure cliquez sur la vignette **entrées** dans votre tâche de flux de données Analytique.  

    ![Portail Azure - ajouter des entrées de données.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  

2. Spécifier le type de l’entrée : **flux de données** ou **les données de référence**.

    ![Ajouter les données correctes d’entrée, diffusés en continu ou faire référence à](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  

    ![Ajouter les données correctes d’entrée, diffusés en continu ou faire référence à](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  

3. Si vous créez une entrée de flux de données, spécifiez le type de source pour l’entrée.  Cette étape peut être omise lors de la création de données de référence en tant que Blob uniquement stockage est pris en charge pour le moment.

    ![Ajouter des entrées de données de flux de données](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  

    ![Ajouter des entrées de données de flux de données](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  

4. Fournir un nom convivial pour cette entrée dans la zone Alias d’entrée.  Ce nom servira de requête de votre travail ultérieurement pour faire référence à l’entrée.

    Renseignez le reste des propriétés de connexion requise pour vous connecter à votre source de données. Ces champs varient selon le type d’entrée et source type et sont définies en détail [ici](stream-analytics-create-a-job.md).  

    ![Ajouter des entrées de données événement concentrateur](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  

5. Spécifier les paramètres de sérialisation pour les données d’entrée :
    - Pour vous assurer que vos requêtes fonctionnent comme vous le souhaitez, spécifiez le **Format de sérialisation événement** des données entrantes.  Formats de sérialisation pris en charge sont JSON, CSV et Avro.
    - Vérifiez que le **codage** pour les données.  UTF-8 est le format de codage pris en charge uniquement pour le moment.

    ![Paramètres de sérialisation de données pour les données d’entrée](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  

    ![Paramètres de sérialisation de données pour les données d’entrée](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  

6. À l’issue de la création d’entrée, flux Analytique vérifie qu’il peut se connecter à la source d’entrée.  Vous pouvez afficher l’état de l’opération de tester la connexion dans le hub de Notification.

    ![Tester la connexion les diffusion en continu d’entrée de données](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  

    ![Tester la connexion les diffusion en continu d’entrée de données](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obtenir de l’aide avec la diffusion en continu les entrées de données
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
