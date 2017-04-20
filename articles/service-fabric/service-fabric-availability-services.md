<properties
   pageTitle="Disponibilité des services de structure de Service | Microsoft Azure"
   description="Décrit la détection de défaillance, le basculement et récupération des services"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="availability-of-service-fabric-services"></a>Disponibilité des services de structure de Service
Services de Service tissu Azure peuvent être avec ou sans. Cet article donne un aperçu de comment Service TISSU assure la disponibilité d’un service en cas de panne.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilité des services sans état tissu de Service
Un service sans état est un service d’application qui n’a pas d' [état permanent local](service-fabric-concepts-state.md).

Création d’un service sans état nécessite la définition d’un nombre d’instances, c'est-à-dire le nombre d’instances du service sans état qui doivent être exécutés dans le cluster. Il s’agit du nombre de copies de la logique d’application qui sera instanciation dans le cluster. Augmenter le nombre d’instances est le meilleur moyen de mise à l’échelle d’un service sans état.

Lorsqu’une erreur est détectée sur toute instance d’un service sans état, une nouvelle instance est créée sur un autre nœud éligible dans le cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilité des services avec état tissu de Service
Un service dynamique a un état qui lui est associé. Dans Service tissu, un service dynamique équivaut à un ensemble de réplicas. Chaque réplica est une instance du code du service qui comporte une copie de l’état. Opérations de lecture et d’écriture sont effectuées dans un réplica (appelé primaire). Modifications apportées à l’état d’opérations d’écriture sont *répliquées* dans plusieurs autres réplicas (appelé secondaires actives). La combinaison de duplications secondaires principales et actives est le jeu de réplica du service.

Il est possible qu’un seul réplica principal maintenance lire et écrire des requêtes, mais il peut y avoir plusieurs réplicas secondaires actives. Le nombre de répliques secondaires actives est configurable, et un nombre plus élevé de réplicas tolèrent un plus grand nombre de logiciels et de matériel.

Dans l’événement d’une défaillance (lorsque réplica principal est indisponible), Service tissu effectue un réplica secondaire active le nouveau réplica principal. Ce réplica secondaire actif comporte déjà la version mise à jour de l’état (par le biais de *la réplication*), et il peut continuer à traiter plus en lecture et les opérations d’écriture.

Ce concept--d’un réplica en cours principal ou actif secondaire--est connu sous le rôle de réplica.

### <a name="replica-roles"></a>Rôles de copie
Le rôle d’un double est utilisé pour gérer le cycle de vie de l’état géré par ce réplica. Un réplica dont le rôle est principaux services les demandes de lecture. Il sert également les demandes d’écriture par mise à jour son état et la réplication des modifications vers les secondaires actives dans son ensemble réplica. Le rôle d’un secondaires actifs consiste à recevoir des modifications de l’état qui réplica principal a répliquée et mettre à jour de la vue de l’état.

>[AZURE.NOTE] Modèles de programmation plus haut niveau tels que le [framework intervenants fiable](service-fabric-reliable-actors-introduction.md) résument absent (e) le concept de rôle réplica auprès du développeur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de tissu de Service, voir les rubriques suivantes :

- [Extensibilité élevées de services de structure de Service](service-fabric-concepts-scalability.md)

- [Partition des services de structure de Service](service-fabric-concepts-partitioning.md)

- [Création et gestion de l’état](service-fabric-concepts-state.md)
