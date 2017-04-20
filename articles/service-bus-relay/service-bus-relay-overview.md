<properties
    pageTitle="Vue d’ensemble de relais Bus des services | Microsoft Azure"
    description="Vue d’ensemble de relais Bus des services."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Vue d’ensemble de relais Bus des services

Un composant important de Bus des services est un service centralisé (mais hautement équilibrage de charge) *relais* qui vous permet de créer des applications hybrides qui s’exécutent dans un centre de données Azure et votre propre environnement d’entreprise en local.  Le relais Bus des services prend en charge une variété de différents protocoles de transport et normes de services web. Cela inclut SOAP Web-, * et même reste. Le service de relais facilite vos applications hybrides en activant en toute sécurité exposer les services Windows Communication Foundation (WCF) qui se trouvent dans un réseau d’entreprise dans le cloud public, sans avoir à ouvrir une connexion de pare-feu, ou qui nécessitent apporter de changements importants à une infrastructure de réseau d’entreprise. 

![Concepts de relais](./media/service-bus-relay-overview/sb-relay-01.png)

Le service de relais prend en charge traditionnel à sens unique de messagerie, la messagerie demande/réponse et égal à la messagerie. Il prend également en charge la distribution événement à portée internet pour activer la publication/abonnement scénarios et communication bidirectionnelle socket pour l’efficacité de point à point accrue. 

Dans le modèle de messagerie relayé, un service en local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication lié à l’adresse d’un rendez-vous particulier. Le client puis communiquer avec le service en local en envoyant des messages au service de relais ciblage de l’adresse de rendez-vous. Le service de relais ensuite « le relais « messages au service local via le socket bidirectionnelle déjà en place. Le client n’a pas besoin d’une connexion directe au service locaux, il n’est pas nécessaire de savoir où réside le service, et le service local n’a pas besoin de ports entrants ouvert sur le pare-feu.

Vous lancez la connexion entre votre service sur site et le service de relais à l’aide d’une suite de liaisons de relais « WCF ». Les coulisses, les liaisons de relais mappez à de nouveaux éléments de liaison de transport conçus pour créer des composants de canal WCF qui s’intègrent à Service dans le cloud. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le relais Bus des services, consultez les rubriques suivantes.

- [Présentation de l’architecture Bus Service Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Comment utiliser le service de relais Bus de Service](service-bus-dotnet-how-to-use-relay.md)

 