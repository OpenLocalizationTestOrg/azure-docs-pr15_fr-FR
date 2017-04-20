<properties
    pageTitle="Quelles sont les relais Azure ? | Microsoft Azure"
    description="Vue d’ensemble de relais Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>Quelles sont les relais Azure ?

Service de relais Azure facilite vos applications hybrides en ce qui vous permet en toute sécurité exposent les services qui se trouvent dans un réseau d’entreprise dans le cloud public, sans avoir à ouvrir une connexion de pare-feu, ou qui nécessitent apporter de changements importants à une infrastructure de réseau d’entreprise. Relais prend en charge une variété de différents protocoles de transport et normes de services web.

Le service de relais prend en charge traditionnel demande/réponse à sens unique et le trafic égal à égal. Il prend également en charge la distribution événement à portée internet pour activer la publication/abonnement scénarios et communication bidirectionnelle socket pour l’efficacité de point à point accrue. 

Dans le modèle de transfert de données relayé, un service en local se connecte au service de relais via un port de sortie et crée un socket bidirectionnelle pour communication lié à l’adresse d’un rendez-vous particulier. Le client puis communiquer avec le service en local en envoyant le trafic vers le service de relais ciblage de l’adresse de rendez-vous. Le service de relais ensuite « le relais « données au service local via un socket bidirectionnelle dédié à chaque client. Le client n’a pas besoin d’une connexion directe au service locaux, il n’est pas nécessaire de savoir où réside le service, et le service local n’a pas besoin de ports entrants ouvert sur le pare-feu.

Les éléments clés fonctionnalité fournis par relais sont une communication bidirectionnelle, sans tampon au-delà des limites de réseau avec TCP à que la limitation, découverte de point de terminaison, état de la connectivité et a-t-il sécurité de point de terminaison. Fonctionnalités de relais diffèrent des technologies d’assistance intégration au niveau du réseau comme VPN dans pouvez portée à un point de terminaison d’application unique sur un seul ordinateur, tandis que la technologie VPN est beaucoup plus poussée car elle se base sur la modification de l’environnement réseau.

Relais Azure contient deux fonctionnalités :

1. [Connexions hybride](#hybrid-connections) - utilise les supports Web standard ouvert l’activation de scénarios multi-plateformes

2. [Relais WCF](#wcf-relays) - utilise Windows Communication Foundation à activer des appels de procédure distante

Connexions hybride et WCF relais permettent d’une connexion sécurisée à assests qui existent au sein d’un réseau d’entreprise. Utilisation d’un rapport à l’autre dépend de vos besoins détaillées ci-dessous :

|                                    | Relais WCF | Connexions hybride |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **Core .NET**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Protocole ouvert normalisé**  |           |         x          |
| **Plusieurs modèles de programmation RPC** |           |         x          |
*<sub>Par disponibilité générale</sub>

## <a name="hybrid-connections"></a>Connexions hybride

Connexions de hybride de relais Azure capacité est une évolution sécurisée, protocole ouvert des fonctionnalités relais existantes qui peut être activée dans n’importe quelle plate-forme et dans n’importe quelle langue qui dispose d’une simple fonctionnalité WebSocket, qui inclut explicitement l’API WebSocket en commun navigateurs web. Connexions hybride est basé sur HTTP et WebSocket.

## <a name="wcf-relays"></a>Relais WCF

Le relais WCF fonctionne pour les complète .NET Framework (NETFX) et pour WCF. Vous lancez la connexion entre votre service sur site et le service de relais à l’aide d’une suite de liaisons de relais « WCF ». Les coulisses, les liaisons de relais mappez à de nouveaux éléments de liaison de transport conçus pour créer des composants de canal WCF qui s’intègrent à Service dans le cloud.

## <a name="service-history"></a>Historique des services

Connexions hybride greffons l’ancienne, également appelée fonctionnalité « Services BizTalk » qui a été créée pour le relais Azure Service Bus WCF. La nouvelle fonctionnalité de connexions hybride complète le relais WCF existant et que ces fonctionnalités deux service apparaissent côte à côte dans le service de relais l’avenir ; elles partager une passerelle courantes, mais dans le cas contraire différentes mises en œuvre.

Relais WCF est le relais hérité offrant utilisent déjà que de nombreux clients avec leurs modèles de programmation WCF.

## <a name="next-steps"></a>Étapes suivantes :

- [Relais Forum aux questions](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Prise en main .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Prise en main nœud](relay-hybrid-connections-node-get-started.md)