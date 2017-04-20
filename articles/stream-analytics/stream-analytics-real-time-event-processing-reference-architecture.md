<properties 
    pageTitle="Traitement avec traitement Analytique de flux de données de l’événement de l’événement en temps réel | Microsoft Azure" 
    description="Découvrez comment un ensemble de services Azure peut interagir d’activation analytique et traitement des événements en temps réel." 
    keywords="traitement en temps réel, traitement de l’événement, architecture de référence"
    services="stream-analytics,event-hubs,storage,sql-database" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="stream-analytics" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architecture de référence : traitement avec Microsoft Azure flux Analytique des événements en temps réel

Architecture de référence pour traitement avec Azure flux Analytique des événements en temps réel est destiné à fournir un plan générique pour le déploiement d’une plateforme en temps réel comme une solution de traitement de flux de service (PaaS) avec Microsoft Azure.

## <a name="summary"></a>Résumé

En règle générale, les solutions analytique ont été basées sur les fonctionnalités, telles que ETL (extraction, transformation, chargement) et data warehouse, où sont stockées les données avant l’analyse. Évolution des besoins, y compris les données plus rapidement entrantes, repoussent ce modèle existant à la limite. La possibilité d’analyser des données au sein de déplacement des flux de données avant d’être stockées est une solution, et s’il n’est pas une nouvelle fonctionnalité, l’approche n’a pas été largement adoptée dans tous les secteurs de secteur. 

Microsoft Azure fournit un catalogue d’étendue de technologies analytique qui peuvent prendre en charge d’un tableau de différents scénarios de solution et configuration requise. Sélectionner les services Azure à déployer pour une solution de bout en bout peut être difficile étant donné la diversité des offres. Cet article est conçu pour décrire les fonctionnalités et interopérabilité entre les différents services Azure qui prennent en charge d’une solution en continu l’événement. Il explique également des scénarios dans lequel les clients bénéficient de ce type d’approche.

## <a name="contents"></a>Contenu

- Synthèse
- Présentation Analytique en temps réel
- Proposition de valeur de données en temps réel dans Azure
- Scénarios courants d’Analytique en temps réel
- Architecture et composants
    - Sources de données
    - Couche d’intégration des données
    - Calque Analytique en temps réel
    - Couche de stockage de données
    - Présentation / consommation de calque
- Conclusion

**Auteur :** Centre d’analyse de données Charles Feddersen, architecte de la Solution, d’Excellence, Microsoft Corporation

**Publié :** Janvier 2015

**Révision :** 1.0

**Télécharger :** [Traitement des flux de données Microsoft Azure Analytique des événements en temps réel](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 
