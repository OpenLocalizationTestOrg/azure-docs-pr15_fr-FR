<properties
    pageTitle="Quelles sont les Hubs d’événement Azure ? | Microsoft Azure"
    description="Vue d’ensemble et une description de Azure événement Hubs"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>Quelles sont les Hubs d’événement Azure ?

Azure événement Hubs est un service de pénétration de données extensibles hautement qui peut traiter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données obtenues par vos périphériques connectés et les applications. Événement Hubs sert de la valeur « porte » pour un pipeline des événements, et une fois que les données sont collectées à un concentrateur de l’événement, il peut être transformé et stockées à l’aide de n’importe quel fournisseur analytique en temps réel ou les cartes de stockage/le traitement par lots. Événement Hubs sépare la production d’un flux d’événements à partir de la consommation de ces événements, afin que les consommateurs d’événements peuvent accéder aux événements selon leur propre calendrier. Pour plus d’informations techniques et détaillées, consultez la [vue d’ensemble de l’événement Hubs](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Fonctions de Hubs d’événements

Événement Hubs est un service qui fournit des événements et traitement de télémétrie à grande échelle avec faible latence et haute fiabilité de traitement des événements. Ce service est particulièrement utile pour :

- Instrumentation de l’application
- Expérience utilisateur ou traitement de flux de travail
- Scénarios Internet des objets (IoT)

Certaines autres fonctionnalités événement Hubs clés inclut le comportement de suivi dans les applications mobiles, informations sur des batteries de serveurs web, capture d’événement dans le jeu dans les jeux de console ou télémétrie collectées à partir de machines industrielles ou véhicules connectés le trafic.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les événements Hubs, consultez les rubriques suivantes.

- [Vue d’ensemble des Hubs événement](event-hubs-overview.md)
- [Événement Hubs guide de programmation](event-hubs-programming-guide.md)
- [Événement Hubs disponibilité et le support Forum aux questions](event-hubs-availability-and-support-faq.md)
- Prise en main avec un [événement Hubs didacticiel][]
- Un [exemple d’application qui utilise l’événement Hubs][] complète

[Didacticiel de Hubs d’événement]: event-hubs-csharp-ephcs-getstarted.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
