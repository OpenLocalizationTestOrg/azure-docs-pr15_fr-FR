<properties
    pageTitle="Vue d’ensemble de la messagerie Bus des services | Microsoft Azure"
    description="Service Bus messagerie : remise de données flexibles dans le cloud"
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
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Bus des services de messagerie : remise flexible des données dans le cloud

Bus des services Microsoft Azure est un service de remise des informations fiables. L’objectif de ce service consiste à faciliter la communication. Lorsque deux ou plusieurs parties voulez échanger des informations, dont elles ont besoin d’un mécanisme de communication. Service Bus est un mécanisme de communication traité ou tiers. Cela revient à un service postal dans le monde physique. Services postaux rendent très facile d’envoyer des différents types de lettres et de packages avec une variété de remise garanties, n’importe où dans le monde.

Comme pour les services postaux exécution lettres, Bus des services est remise informations flexibles à partir de l’expéditeur et le destinataire. Le service de messagerie garantit que les informations sont livrées même si les deux parties ne sont jamais à la fois en ligne en même temps, ou si elles ne sont pas disponibles en même temps exacte. De cette façon, la messagerie est similaire à l’envoi d’une lettre, tandis que les communications non demandée revient à placer un appel téléphonique (ou comment un appel téléphonique utilisé pour être - avant d’appel en attente et appelant ID qui sont beaucoup plus comme traité de messagerie).

L’expéditeur du message peut nécessiter également diverses caractéristiques de remise, y compris des transactions, détection des doublons, expiration temporelles et le traitement par lots. Ces modèles ont analogies postal ainsi : Répétez remise, signature requise, modification de l’adresse ou rappel.

Bus des services prend en charge deux modèles de messagerie distinctes : *relais* et *traité de messagerie*.

## <a name="service-bus-relay"></a>Service Bus relais

Le composant [relais](../service-bus-relay/service-bus-relay-overview.md) du Service Bus est un service centralisé (mais hautement équilibrage de charge) qui prend en charge une variété de différents protocoles de transport et normes de services Web. Cela inclut SOAP Web-, * et même reste. Le [service de relais](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) fournit de nombreuses options de connectivité de relais différent et peut vous aider à négociation des connexions directes égal à égal lorsque cela est possible. Bus des services est optimisé pour les développeurs .NET qui utilisent la Windows Communication Foundation (WCF), les deux en ce qui concerne les performances et la convivialité et offre un accès complet à son service de relais via les interfaces SOAP et reste. Cela permet les SOAP ou reste environnement de programmation intégrer à Service.

Le service de relais prend en charge traditionnel à sens unique de messagerie, la messagerie demande/réponse et égal à la messagerie. Il prend également en charge la distribution événement à portée Internet pour activer la publication d’abonnement de scénarios et communication bidirectionnelle socket pour l’efficacité de point à point accrue. Dans le modèle de messagerie relayé, un service en local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication lié à l’adresse d’un rendez-vous particulier. Le client puis communiquer avec le service en local en envoyant des messages au service de relais ciblage de l’adresse de rendez-vous. Le service de relais ensuite « le relais « messages au service local via le socket bidirectionnelle déjà en place. Le client n’a pas besoin d’une connexion directe au service en local, ni est-il nécessaire de savoir où réside le service, et le service local n’a pas besoin de ports entrants ouvert sur le pare-feu.

Vous lancez la connexion entre votre service en local et le service de relais, à l’aide d’une suite de liaisons de relais « WCF ». Les coulisses, les liaisons de relais mappent pour transport d’éléments de liaison conçus pour créer des composants de canal WCF qui s’intègrent à Service dans le cloud.

Service Bus relais offre de nombreux avantages, mais nécessite que le serveur et le client à la fois à être en ligne en même temps afin d’envoyer et recevoir des messages. Ce n’est pas optimal pour les communications HTTP style, dans lequel les requêtes peuvent ne pas être généralement à long terme, ni pour les clients qui se connectent occasionnellement, tels que des navigateurs, les applications mobiles et ainsi de suite. Demandé de communication découplé prend en charge la messagerie et possède ses propres avantages ; clients et les serveurs peuvent se connecter si nécessaire, effectuer les opérations de façon asynchrone.

## <a name="brokered-messaging"></a>Demandé de messagerie

Contrairement à la méthode de relais, [traité de messagerie](service-bus-queues-topics-subscriptions.md) peuvent être considérés comme asynchrone, ou « temporairement découplé. » Producteur (expéditeurs) et les consommateurs (récepteurs) est inutile d’être en ligne en même temps. L’infrastructure de messagerie fiable stocke les messages dans un « intermédiaire » (par exemple, une file d’attente) jusqu'à ce que la partie (consommateur) est prête à recevoir les. Cela permet des composants de l’application distribuée à être déconnecté, soit volontaire ; par exemple, pour la maintenance, ou en raison d’un blocage de composant, sans affecter la totalité du système. En outre, l’application de réception peut-être uniquement être en ligne durant certaines heures de la journée, par exemple un système de gestion de stock uniquement est requis pour exécuter à la fin de la journée.

Les composants principaux de l’infrastructure de messagerie traité Bus des services sont files d’attente, rubriques et abonnements.  La différence principale est que rubriques prennent en charge les fonctionnalités de publication et d’abonnement qui peuvent être utilisées pour élaborée en fonction du contenu routage et la remise logique, notamment en envoyant à plusieurs destinataires. Ces composants activent nouveaux scénarios de messagerie asynchrones, par exemple découplage temporel, publier et d’abonnement et l’équilibrage de charge. Pour plus d’informations sur ces entités messageries, voir [abonnements, rubriques et les files d’attente Bus des services](service-bus-queues-topics-subscriptions.md).

À l’instar de l’infrastructure relais, la fonctionnalité de messagerie traitée est fournie aux développeurs WCF et .NET Framework, mais également via le reste.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Bus des services, consultez les rubriques suivantes.

- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
- [Abonnements, rubriques et files d’attente Bus des services](service-bus-queues-topics-subscriptions.md)
- [Comment utiliser les files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)
- [Comment utiliser des abonnements et les rubriques de Bus des services](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
