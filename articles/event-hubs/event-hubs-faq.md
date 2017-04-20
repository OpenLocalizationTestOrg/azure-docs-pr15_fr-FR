<properties 
    pageTitle="Événement Hubs Forum aux questions (FAQ) | Microsoft Azure"
    description="Événement Hubs Forum aux questions."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Événement Hubs Forum aux questions

Événement Hubs fournit admission à grande échelle, persistance et traitement des événements de données à partir de sources de données haut débit et/ou millions d’appareils. Associées à des rubriques et files d’attente de Bus des services, Hubs événement permet des déploiements de commande et de contrôle permanentes pour les scénarios [Internet des objets (IoT)](https://azure.microsoft.com/services/iot-hub/) .

Cet article décrit les informations de tarification et fournit des réponses à certaines questions fréquemment posées sur Hubs événement :

## <a name="pricing-information"></a>Informations sur les prix

Pour des informations complètes sur les événements Hubs de tarification, voir les [événements Hubs pour plus d’informations](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Mode de calcul des événements de pénétration Hubs événement ?

Compte de chaque événement envoyé à un concentrateur de l’événement en tant que message facturable. Un *événement de pénétration* est définie comme une unité de données qui est inférieure ou égale à 64 Ko. Tout événement qui est inférieure ou égale à 64 Ko la taille est considéré comme un seul événement facturable. Si l’événement est supérieur à 64 Ko, le nombre d’événements facturables est calculé selon la taille de l’événement, en multiples de 64 Ko. Par exemple, un événement de 8 Ko envoyé au concentrateur événement est facturé en tant qu’un événement, mais un message 96 Ko envoyé au concentrateur événement est facturé comme des deux événements.

Événements consommées depuis un concentrateur événement, en tant qu’ainsi que des opérations de gestion et contrôlent les appels tels que des points de contrôle, ne sont pas comptés comme des événements de pénétration facturables, mais accumuler jusqu'à l’allocation d’unité débit.

## <a name="what-are-event-hubs-throughput-units"></a>Que sont les unités de débit Hubs événement ?

Vous sélectionnez explicitement événement Hubs débit unités, via le portail Azure ou événement Hubs ressource Gestionnaire de modèles. Unités de débit s’appliquent à tous les événements Hubs dans un espace de noms événement Hubs et chaque unité de débit autorise l’espace de noms pour les fonctionnalités suivantes :

- Vers le haut et 1 Mo par seconde de pénétration événements (envoyé à un concentrateur événement), mais pas plus de 1000 événements pénétration, opérations de gestion de contrôle ou API appels par seconde.

- Jusqu'à 2 Mo par seconde des événements de sortie (événements consommées depuis un concentrateur événement).

- Jusqu'à 84 Go de stockage des événements (suffisant pour la période de rétention de 24 heures par défaut).

Unités de débit Hubs événement sont facturées toutes les heures, en fonction du nombre maximal d’unités sélectionné lors de l’heure donnée.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Comment les événements Hubs débit unité limites sont appliquées ?

Si le débit total pénétration ou le taux d’événement pénétration total sur tous les événements Hubs dans un espace de noms dépasse l’allocation d’unité débit total, expéditeurs seront limitées et recevez des erreurs indiquant que le quota de pénétration a été dépassé.

Si le débit de sortie total ou le taux de sortie événement total sur tous les événements Hubs dans un espace de noms dépasse l’allocation d’unité débit total, récepteurs sont limitées et recevez des erreurs indiquant que le quota de sortie a été dépassé. Les quotas entrant et sortant sont appliqués séparément, afin qu’aucun expéditeur ne peut entraîner la consommation événement ralentir, ni un récepteur peut empêcher d’événements d’être envoyé à un concentrateur de l’événement.

Notez que la sélection de l’unité débit est indépendante du nombre de partitions Hubs événement. Tandis que chaque partition offre débit maximum est de 1 Mo par seconde pénétration (avec un maximum de 1000 événements par seconde) et 2 Mo par seconde sortie, il est gratuit fixe pour les partitions eux-mêmes. Les frais concerne les unités de débit agrégé sur tous les événements Hubs dans un espace de noms Hubs événement. Avec ce modèle, vous pouvez créer suffisamment partitions pour prendre en charge la charge maximale prévue pour leur système, sans subir les frais unité débit jusqu'à ce que la charge de l’événement sur le système réellement nécessite des nombres plus grands de débit, sans avoir à modifier la structure et l’architecture de vos systèmes en tant que la charge sur le système augmente.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Existe-t-il une limite sur le nombre d’unités de débit pouvant être sélectionnés ?

Il existe un quota par défaut de 20 unités de débit par espace de noms. Vous pouvez demander un quota plus important d’unités débit en déposant une demande d’assistance. Au-delà de la limite d’unité 20 débit, offres sont disponibles dans les 20 à 100 unités de débit. Notez que l’utilisation de plus de 20 unités de débit supprime la possibilité de modifier le nombre d’unités de débit sans classer un tickets de support.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe-t-il des frais de conservation des événements Hubs événement pour plus de 24 heures ?

La couche événement Hubs Standard autorise-t-elle rétention message périodes plus de 24 heures, pour un maximum de 30 jours. Si la taille du nombre total d’événements stockées dépasse l’allocation de stockage pour le nombre d’unités de débit sélectionné (84 Go par unité débit), la taille est supérieure à l’allocation est facturée au taux stockage Blob Azure publié. L’allocation de stockage dans chaque unité de débit couvre tous les coûts de stockage pour les périodes de rétention de 24 heures (par défaut) même si l’unité de débit est à l’allocation de pénétration maximale.

## <a name="what-is-the-maximum-retention-period"></a>Quelle est la période de rétention maximale ?

Niveau Hubs Standard événement prend actuellement en charge une période de rétention maximale de 7 jours. Notez que Hubs événement ne sont pas destinées comme base de données permanentes. Période de rétention supérieure à 24 heures est conçue pour les scénarios dans lesquels il est judicieux de relire un flux d’événements dans les mêmes systèmes ; par exemple, pour former ou vérifier un nouveau modèle d’apprentissage machine sur les données existantes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Comment la taille de stockage événement Hubs est calculée et facturée ?

La taille totale de tous les événements stockées, y compris toute surcharge interne pour les en-têtes de l’événement ou sur les structures de stockage de disque dans tous les événements Hubs, est mesurée toute la journée. À la fin de la journée, la taille de stockage pointe est calculée. L’allocation de stockage quotidien est calculée en fonction du nombre minimal d’unités de débit qui ont été sélectionnés pendant la journée (chaque unité débit fournit une allocation de 84 Go). Si la taille totale dépasse l’allocation de stockage quotidien calculé, le stockage excédentaire est facturé à l’aide des taux de stockage Blob Azure (au taux de **Stockage redondants localement** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>Puis-je utiliser une connexion AMQP unique pour envoyer et recevoir d’événement Hubs et Bus des services files d’attente/rubriques ?

Oui, tant que tous les événements Hubs, files d’attente et rubriques sont dans le même espace de noms. Par conséquent, vous pouvez implémenter bidirectionnelle, traitée connectivité à de nombreux périphériques, avec latence moins, de façon rentable et hautement scalable.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Des frais de connexion traité s’appliquent aux événements Hubs ?

Pour les expéditeurs, frais de connexion s’appliquent uniquement lorsque le protocole AMQP est utilisé. Il n’existe aucune des frais de connexion d’envoi d’événements à l’aide de HTTP, quel que soit le nombre d’envoi de systèmes ou périphériques. Si vous prévoyez d’utiliser AMQP (par exemple, pour obtenir plus efficace événement diffusion en continu ou pour activer la communication bidirectionnelle dans les scénarios de commande et de contrôle IoT), reportez-vous à la page [informations sur les tarifs Bus des services](https://azure.microsoft.com/pricing/details/service-bus/) pour plus d’informations sur ce qui constitue une connexion traitée, et comment ils sont limitées.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Quelle est la différence entre les événements Hubs base et niveaux Standard ?

Niveau Hubs Standard événement des fonctionnalités au-delà de ce qui est disponible dans événement Hubs simple et dans certains systèmes sur la concurrence. Ces fonctionnalités incluent des périodes de conservation de plus de 24 heures et la possibilité d’utiliser une connexion AMQP unique pour envoyer des commandes à un grand nombre d’appareils avec la latence moins, ainsi que d’envoyer télémétrie à partir de ces appareils dans Hubs événement. Pour la liste des fonctionnalités, consultez les [événements Hubs pour plus d’informations](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilité géographique

Événement Hubs est disponible dans les régions suivantes :

|Geo|Régions|
|---|---|
|États-Unis|États-Unis centre, US Extrême-Orient, US Extrême-Orient 2, États-Unis centre sud, US ouest|
|Europe|Europe du Nord, Europe occidentale|
|Asie-Pacifique|Extrême-Orient, Asie du Sud-est|
|Japon|Japon orientale, Japon ouest|
|Brésil|Brésil sud|
|Australie|Australie Moyen-Orient, Australie sud-est.|

## <a name="support-and-sla"></a>Prise en charge et SLA

Support technique pour les événements Hubs est disponible via les [forums de la Communauté](https://social.msdn.microsoft.com/forums/azure/home). Support de gestion de facturation et l’abonnement est proposé gratuitement.

Pour en savoir plus sur notre délai d’intervention, consultez la page [Contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’événement Hubs, consultez les articles suivants :

- [Vue d’ensemble de l’événement Hubs][].
- Un [exemple d’application qui utilise l’événement Hubs][]complète.

[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
