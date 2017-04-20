<properties 
    pageTitle="Vue d’ensemble du service Bus AMQP | Microsoft Azure" 
    description="Découvrez comment utiliser les Advanced Message Queuing Protocol (AMQP) 1.0 dans Azure." 
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
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>



# <a name="amqp-10-support-in-service-bus"></a>Prise en charge de AMQP 1.0 dans marché de Service

Le service de nuage Bus des services Azure et en local [Service Bus pour Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) prend en charge l’avancées Message file d’attente Protocol (AMQP) 1.0. AMQP vous permet de créer multi-plateforme, applications hybrides à l’aide d’un protocole standard ouvert. Vous pouvez créer des applications à l’aide de composants qui sont générés avec des structures et des langues différentes, et qui s’exécutent sur différents systèmes d’exploitation. Tous ces composants peuvent se connecter au Service Bus et en toute transparence échangent des messages métiers structurés d’efficacité et de fidélité complète.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduction : Qu’est AMQP 1.0 et pourquoi est-il important ?

Généralement, les produits logiciels intermédiaires orientés message utilisent les protocoles propriétaires pour les communications entre les applications clientes et intermédiaires financiers. Cela signifie qu’une fois que vous avez sélectionné intermédiaire messagerie d’un fournisseur particulier, vous devez utiliser les bibliothèques de ce fournisseur pour vous connecter vos applications clientes à cette intermédiaire. Cela entraîne un degré de dépendance sur ce dernier, dans la mesure où porter une application à un autre produit nécessite des modifications de code dans toutes les applications connectées. 

Par ailleurs, il est difficile de connexion agents messageries provenant de différents fournisseurs. Cela requiert généralement au niveau de l’application de transition pour déplacer les messages d’un système à l’autre et traduire entre les formats de message propriétaires. Il s’agit d’une exigence courante ; par exemple, lorsque vous devez fournir une nouvelle interface unifiée aux anciens différents systèmes ou intégrer des systèmes informatiques suite de la fusion.

Le secteur des logiciels d’une entreprise se déplacent rapidement ; nouveaux langages de programmation et structures d’application sont présentés à un rythme parfois déconcertante. De même, la configuration requise des systèmes informatiques évoluer au fil du temps et les développeurs souhaitent tirer parti des fonctionnalités de plateforme plus récentes. Toutefois, parfois le fournisseur de messagerie sélectionné ne prend pas en charge ces plateformes. Étant donné que les protocoles de messagerie sont propriétaires, il n’est pas possible pour d’autres personnes fournir des bibliothèques pour ces nouvelles plates-formes. Par conséquent, vous devez utiliser approches tels que la création des passerelles ou des ponts qui vous permettent de continuer à utiliser le produit de messagerie.

Le développement de l’avancées Message Queuing Protocol (AMQP) 1.0 a été origine par ces problèmes. Elle a été créée en JP Morgan Chase, qui, comme des sociétés financières plus de services, sont gros utilisateurs de logiciels intermédiaires orientés message. L’objectif était simple : pour créer un protocole de messagerie standard ouvert rendus possibles créer des applications basées sur le message à l’aide de composants créés à l’aide de différentes langues, les cadres et les systèmes d’exploitation, tous les composants de pointe à partir d’un large éventail de fournisseurs à l’aide.

## <a name="amqp-10-technical-features"></a>Fonctionnalités techniques AMQP 1.0

AMQP 1.0 est un protocole de messagerie efficace, fiable au niveau du câble que vous pouvez utiliser pour générer robuste, multi-plateforme, applications de messagerie. Le protocole a un objectif simple : définir la mécanique du transfert sécurisé, fiable et efficace de messages entre deux parties. Les messages proprement dits sont codés à l’aide d’une représentation de données portable qui permet de hétérogènes expéditeurs et destinataires échanger des messages professionnels structurées en fidélité complète. Voici un résumé des fonctionnalités plus importantes :

*    **Efficaces**: AMQP 1.0 est orienté sur une connexion de protocole qui utilise un codage binaire pour les instructions de protocole et les messages d’entreprise transféré. Il incorpore jeux complexes de contrôle de flux d’optimiser l’utilisation du réseau et les composants connectés. Ceci étant dit, le protocole a été conçu pour un équilibre entre l’efficacité, flexibilité et l’interopérabilité.
*    **Fiable**: 1.0 AMQP le protocole permet aux messages d’être échangés avec une plage de garanties de fiabilité, de fire-et-oubliez pas fiable, exactement-accusé de réception une fois la remise.
*    **Flexible**: AMQP 1.0 est un protocole souple qui peut être utilisé pour prendre en charge des topologies différentes. Le protocole même peut être utilisé pour les communications client à client-intermédiaire et intermédiaire pour intermédiaire.
*    **Modèle d’intermédiaire indépendant**: spécification The AMQP 1.0 n’effectue aucune des exigences sur le modèle de messagerie utilisé par un intermédiaire. Cela signifie qu’il est possible d’ajouter facilement prise en charge AMQP 1.0 à agents de messagerie existants.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 est une norme (avec une majuscule »)

AMQP 1.0 est une norme internationale, approuvé par ISO et IEC comme ISO/IEC 19464:2014.

AMQP 1.0 a été développé depuis 2008 par un groupe de plus de 20 entreprises, fournisseurs technologiques et entreprises d’utilisateur final. Pendant ce temps, considèrent utilisateur ont collaboré à leurs besoins réels et les fournisseurs de technologie ont évolué le protocole pour répondre à ces exigences. Tout au long du processus, fournisseurs ayant participé à ateliers dans lequel ils ont collaboré pour valider l’interopérabilité entre leurs mises en œuvre.

En octobre 2011, le travail de développement basculé vers un comité technique au sein de l’organisation de l’avancement de structurées informations normes OASIS () et la norme OASIS AMQP 1.0 a été publié en octobre 2012. Les sociétés suivantes participé le comité technique pendant le développement de la norme :

*    **Fournisseurs de technologie**: Axway Software, Huawei Technologies, IIT logiciel, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, l’avancement des logiciels, rouge chapeau, SITA, logiciel AG, systèmes Solace, VMware, WSO2, Zenika.
*    **Entreprises d’utilisateur**: banque de Nord, crédit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Voici quelques-uns des avantages cités fréquemment des normes ouvertes :

*    Réduire les risques de verrouillage de fournisseur
*    Interopérabilité
*    Une large gamme d’outils et les bibliothèques
*    Protection contre l’obsolescence
*    Disponibilité de personnel expérimenté
*    Risque inférieur et facile à gérer

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 et le Service Bus

Prise en charge AMQP 1.0 dans Azure Service marché signifie que vous pouvez désormais exploiter Bus des services file attente comprenant et publication/abonnement traitées fonctionnalités de messagerie à partir d’une plage de plateformes à l’aide d’un protocole binaire efficace. Par ailleurs, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les cadres et les systèmes d’exploitation.

La figure suivante illustre un exemple de déploiement dans lequel les clients Java s’exécutant sur Linux, écrite à l’aide du standard Java Message Service (JMS) API et des clients .NET fonctionnant sous Windows, échangent des messages via Bus des services à l’aide de AMQP 1.0.

![][0]

**Figure 1 : Exemple de scénario déploiement affichant la disponibilité sur plusieurs plateformes messagerie à l’aide du Service Bus et AMQP 1.0**

Pour le moment les bibliothèques client suivants sont connus pour fonctionner avec Bus des services :

| Langue | Bibliothèque                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Client Apache Qpid Java Message Service (JMS)<br/>Client IIT logiciel SwiftMQ Java |
| C        | Apache Qpid PROTONS-C                                                          |
| PHP      | Apache Qpid PROTONS-PHP                                                        |
| Python   | Apache Qpid PROTONS-Python                                                     |
| C#       | .Net AMQP légère                                                                |

**Figure 2 : Tableau des bibliothèques de client AMQP 1.0**

## <a name="summary"></a>Résumé

*    AMQP 1.0 est un protocole de messagerie ouvert, fiable que vous pouvez utiliser pour générer multi-plateforme, applications hybrides. AMQP 1.0 est une norme OASIS.
*    Prise en charge AMQP 1.0 est désormais disponible en Bus des services Azure ainsi que Service Bus pour Windows Server (Service Bus 1.1). Tarifs est la même que pour les protocoles existants.

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Consultez les liens suivants :

- [À l’aide de Bus des services à partir de .NET avec AMQP]
- [À l’aide de Bus des services à partir de Java avec AMQP]
- [À l’aide de Bus des services à partir de Python avec AMQP]
- [À l’aide de Bus des services à partir de PHP avec AMQP]
- [L’installation d’Apache Qpid PROTONS-C dans une machine virtuelle Linux Azure]
- [AMQP dans Service marché pour Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[À l’aide de Bus des services à partir de .NET avec AMQP]: service-bus-amqp-dotnet.md
[À l’aide de Bus des services à partir de Java avec AMQP]: service-bus-amqp-java.md
[À l’aide de Bus des services à partir de Python avec AMQP]: service-bus-amqp-python.md
[À l’aide de Bus des services à partir de PHP avec AMQP]: service-bus-amqp-php.md
[L’installation d’Apache Qpid PROTONS-C dans une machine virtuelle Linux Azure]: service-bus-amqp-apache.md
[AMQP dans Service marché pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx