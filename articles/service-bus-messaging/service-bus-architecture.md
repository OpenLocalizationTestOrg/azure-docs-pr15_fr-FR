<properties 
    pageTitle="Architecture de service Bus | Microsoft Azure"
    description="Décrit le message et le relais architecture de Bus des services Azure de traitement."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Architecture de Bus de service

Cet article décrit le message et le relais architecture de Bus des services Azure de traitement.

## <a name="service-bus-scale-units"></a>Unités de l’échelle Bus des services

Service Bus est organisé par *unités de l’échelle*. Une unité d’échelle est une unité de déploiement et contient tous les composants requis exécuteront le service. Chaque région déploie une ou plusieurs unités échelle Bus des services.

Un espace de noms Bus des services est associé à une unité d’échelle. L’unité d’échelle gère tous les types d’entités Bus de Service : relais et traitées entités messageries (files d’attente, rubriques, abonnements). Une unité d’échelle Bus des services se compose des éléments suivants :

- **Un ensemble de nœuds de passerelle.** Les nœuds passerelle authentifier les demandes entrantes et gérer les demandes de relais. Chaque nœud de passerelle a une adresse IP publique.

- **Un ensemble de messagerie intermédiaire nœuds.** Messagerie nœuds intermédiaire traitent les demandes relatives aux entités de messagerie.

- **Banque d’une passerelle.** La banque de passerelle contient les données de chaque entité défini dans cette unité d’échelle. Le magasin de passerelle est activée en haut d’une base de données SQL Azure.

- **Plusieurs banques de messagerie.** Messagerie banques qui doivent contenir les messages des files d’attente, rubriques et abonnements qui sont définis dans cette unité d’échelle. Elle contient également toutes les données de l’abonnement. À moins d’avoir [partitionnée entités la messagerie](service-bus-partitioning.md) est activée, un file d’attente ou une rubrique est mappé à une seule banque de messagerie. Les abonnements sont stockés dans la même banque de messagerie que leur rubrique parent. À l’exception des Bus des services [De messagerie Premium](service-bus-premium-messaging.md), les banques de messagerie sont implémentées en haut des bases de données SQL Azure.

## <a name="containers"></a>Conteneurs

Chaque entité messagerie est affectée un conteneur spécifique. Un conteneur est une construction logique qui utilise un magasin à la messagerie toutes les données pertinentes pour ce conteneur. Chaque conteneur est affectée à un nœud intermédiaire messagerie. En règle générale, il existe plusieurs conteneurs que la messagerie intermédiaire nœuds. Par conséquent, chaque nœud intermédiaire messagerie charge plusieurs conteneurs. La distribution de conteneurs à un nœud intermédiaire messagerie est organisée de sorte que tous les nœuds intermédiaire messagerie sont également chargés. Si la charge de motif modifications (par exemple, une de le reçoit conteneurs très occupé), ou si un nœud intermédiaire messagerie devient temporairement indisponible, les conteneurs sont redistribués parmi les nœuds intermédiaire messagerie.

## <a name="processing-of-incoming-messaging-requests"></a>Traitement des demandes de messagerie entrants

Lorsqu’un client envoie une demande de Service bus, l’équilibrage de charge Azure route vers un des nœuds de la passerelle. Le nœud de passerelle autorise la demande. Si la demande concerne une entité messagerie (file d’attente, rubrique, abonnement), le nœud de passerelle recherche l’entité dans le magasin de passerelle et détermine dans quelle banque de messagerie se trouve l’entité. Il recherche alors le nœud intermédiaire messagerie prend en charge actuellement ce conteneur et envoie la demande à ce nœud intermédiaire messagerie. Le nœud intermédiaire messagerie traite la demande et l’état d’entité dans le magasin de conteneur mis à jour. Le nœud intermédiaire messagerie envoie ensuite la réponse vers le nœud de passerelle, qui permet d’envoyer une réponse appropriée au client qui a publié la requête d’origine.

![Traitement des demandes de messagerie entrants](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Traitement des demandes entrantes de relais

Lorsqu’un client envoie une demande de Service bus, l’équilibrage de charge Azure route vers un des nœuds de la passerelle. Si la requête est une requête à l’écoute, le nœud de passerelle crée un nouveau relais. Si la demande est une demande de connexion à un relais spécifique, le nœud de passerelle transfère la demande de connexion pour le nœud de passerelle qui possède le relais. Le nœud de passerelle qui possède le relais envoie une demande de rendez-vous au client écoute, demandant que l’auditeur pour créer un canal temporaire vers le nœud de passerelle qui a reçu la demande de connexion.

Lorsque la connexion relais est établie, les clients peuvent échanger des messages via le nœud de passerelle est utilisé pour les rendez-vous.

![Traitement des demandes entrantes de relais](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu une vue d’ensemble de l’architecture de Bus des services, mise en route consultez les liens suivants :

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
- [Une solution en file d’attente de messagerie à l’aide de files d’attente Bus des services](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
