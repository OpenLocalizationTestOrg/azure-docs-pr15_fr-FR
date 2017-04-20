<properties
    pageTitle="Service Bus Premium et messagerie Standard vue d’ensemble des niveaux de tarification | Microsoft Azure"
    description="Service Bus Premium et la messagerie Standard"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Service Bus Premium et les niveaux de messagerie Standard 

Bus des services de messagerie, qui incluent des entités telles que des files d’attente de la messagerie et rubriques, fonctions avec enrichi de messagerie d’entreprise combine publication abonnement sémantique à l’échelle de cloud. Bus des services de messagerie est utilisée comme le réseau principal de communication pour de nombreuses solutions de cloud sophistiquées.

La couche *Premium* de Bus des services de messagerie adresses courantes demandes des clients autour d’échelle, les performances et disponibilité des applications critiques. Bien que les groupes de fonctionnalités sont presque identiques, ces deux niveaux de Bus des services de messagerie est conçus pour servir de cas d’utilisation différents.

Certaines différences de haut niveau sont mis en surbrillance dans le tableau ci-dessous.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Haut débit                       | Débit variable            |
| Performances prévisibles               | Latence variable               |
| Tarifs prévisibles                   | Tarifs variable de paiement |
| Possibilité de mettre à l’échelle haut et bas de la charge de travail | N/A                            |
| Message taille > 256 Ko                  | Taille du message est 256 Ko          |

**Service Bus Premium messagerie** fournit des isolement ressource au niveau du processeur et la mémoire afin que chaque charge de travail client s’exécute séparément. Ce conteneur de ressources est appelé une *unité de messagerie*. Chaque espace de noms premium alloué au moins une unité de messagerie. Vous pouvez acheter 1, 2 ou 4 unités de messagerie pour chaque espace de noms de Service Bus Premium. Une seule charge de travail ou une entité peut couvrir divisions de votre messageries et le nombre d’unités de messagerie peut être modifié en entraîne, bien que facturation de frais de taux quotidiennement ou 24 heures. Le résultat est prévisibles et répétitifs les performances de votre solution Bus des services.

Non seulement cette efficacité n’est plus prévisibles et disponible, mais il est également plus rapide. Premium Bus de service de messagerie s’appuie sur le moteur de stockage introduit dans [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/). Avec la messagerie Premium, des performances optimales sont beaucoup plus rapide avec la couche Standard.

## <a name="premium-messaging-technical-differences"></a>Différences techniques de messagerie Premium

Voici quelques différences entre Premium et les niveaux de messagerie Standard.

### <a name="partitioned-queues-and-topics"></a>Rubriques et partitionnées files d’attente

Rubriques et files d’attente partitionnées sont prises en charge dans Premium de messagerie, mais ils ne fonctionnent pas de la même façon que dans les couches Standard et Basic de Bus des services de messagerie. Messagerie Premium n’utilise pas SQL comme base de données et n’est plus la concurrence ressources possibles associée à une plateforme partagée. Par conséquent, il n’est pas nécessaire de partition. En outre, le nombre de partitions ont été modifié à partir de 16 partitions dans la messagerie Standard à 2 partitions dans Premium. Vous rencontrez des deux partitions garantissant la disponibilité et sont un nombre plus approprié pour l’environnement d’exécution Premium. Pour plus d’informations sur partition, voir [partitionnées files d’attente et rubriques](service-bus-partitioning.md).

### <a name="express-entities"></a>Entités Express

Messagerie Premium est exécuté dans un environnement d’exécution complètement isolé, entités express ne sont pas pris en charge dans les espaces de noms Premium. Pour plus d’informations sur la fonctionnalité expresse, consultez la propriété [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Bus des services, consultez les rubriques suivantes.

- [Présentation d’Azure Service Bus Premium messagerie (billet de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Présentation d’Azure Service Bus Premium messagerie (Channel 9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Comment utiliser les files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)
