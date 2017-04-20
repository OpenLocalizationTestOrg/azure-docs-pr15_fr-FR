<properties
    pageTitle="En quoi consiste le trafic Manager | Microsoft Azure"
    description="Cet article vous aidera à comprendre ce qui est responsable de trafic et s’il s’agit le choix de routage le trafic de votre application"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Vue d’ensemble du trafic Gestionnaire

Microsoft Azure le trafic Manager vous permet de contrôler la distribution du trafic utilisateur pour les points de terminaison de service dans différents centres de données. Points de terminaison de service pris en charge par le Gestionnaire de trafic incluent machines virtuelles Azure, applications Web et services cloud. Vous pouvez également utiliser le trafic gestionnaire avec points de terminaison externes, non Azure.

Le trafic Manager utilise le système DNS (Domain Name) pour envoyer les demandes de client au point de terminaison qui conviennent basé sur une [méthode le routage du trafic](traffic-manager-routing-methods.md) et la santé des points de terminaison. Le trafic Manager fournit une plage de routage du trafic méthodes pour s’adapter aux besoins de différentes applications, point de terminaison de l’état [de surveillance](traffic-manager-monitoring.md)et basculement automatique. Gestionnaire de trafic est résistant défaillance, y compris l’échec d’une région Azure entière.

## <a name="traffic-manager-benefits"></a>Avantages du trafic Manager

Le trafic Manager peut vous aider :

- **Améliorer la disponibilité des applications critiques**

    Gestionnaire de trafic augmentent la disponibilité de vos applications en vos points de terminaison de surveillance et en fournissant basculement automatique lorsqu’un point de terminaison est indisponible.

- **Améliorer la réactivité pour applications haute performance**

    Azure vous permet d’exécuter les services en nuage ou sites Web dans centres de données dans le monde entier. Gestionnaire de trafic améliore la réactivité des applications en redirigeant le trafic vers le point de terminaison avec la latence du réseau les plus faibles pour le client.

- **Effectuer la maintenance du service sans interruption de service**

    Vous pouvez effectuer les opérations de maintenance planifiée sur vos applications sans interruption de service. Gestionnaire de trafic achemine le trafic aux autres points de terminaison alors que la maintenance est en cours.

- **Combiner en local et applications basées sur le nuage**

    Prend en charge le trafic gestionnaire externe, points de terminaison non Azure l’activer être utilisé avec hybride cloud et déploiements, y compris la « rupture-nuage, » « migrer-nuage, » et « basculement-nuage » scénarios locaux.

- **Distribuer le trafic de grands déploiements complexes**

    Les profils [le trafic Gestionnaire imbriquée](traffic-manager-nested-profiles.md)des méthodes de routage de trafic peuvent être combinés pour créer des règles sophistiquées et flexibles pour répondre aux besoins des déploiements plus grandes et plus complexes.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [fonctionnement du Gestionnaire de trafic](traffic-manager-how-traffic-manager-works.md).

- Découvrez comment développer des applications à haute disponibilité à l’aide du [Gestionnaire de trafic surveillance de point de terminaison](traffic-manager-monitoring.md).

- En savoir plus sur les [méthodes de routage le trafic](traffic-manager-routing-methods.md) pris en charge par le Gestionnaire de trafic.

- [Créer un profil Manager le trafic](traffic-manager-manage-profiles.md).

