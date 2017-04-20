<properties
 pageTitle="Concentrateur IoT HA et DR | Microsoft Azure"
 description="Décrit les fonctionnalités qui vous aident à créer des solutions IoT hautement disponibles avec urgence capacités de récupération."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Concentrateur IoT haute disponibilité et récupération d’urgence

Comme un service Azure, concentrateur IoT fournit haute disponibilité (HA) à l’aide de licenciement au niveau de la région Azure, sans aucun travail supplémentaire requis par la solution. En outre, Azure offre un certain nombre de fonctionnalités qui vous aident à créer des solutions avec reprise (DR) ou la région de manière croisée disponibilité si nécessaire. Vous devez concevoir et préparer vos solutions pour tirer parti de ces fonctionnalités DR si vous souhaitez fournir globale, disponibilité entre région pour appareils ou des utilisateurs. L’article [Azure entreprise continuité des conseils techniques](../resiliency/resiliency-technical-guidance.md) décrit les fonctionnalités intégrées dans Azure pour continuité des activités et DR. Le papier [sinistre et disponibilité des applications Azure][] explique comment architecture stratégies d’applications Azure à atteindre HA et DR.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Outre HA interne région, concentrateur IoT met en œuvre mécanismes de basculement de sinistre nécessitant sans intervention de l’utilisateur. IoT Hub DR est initiée automatique et comporte une récupération heure objectifs de 2-26 heures, les objectifs de point de récupération (RPO) suivants.

| Fonctionnalités | RPO |
| ------------- | --- |
| Disponibilité des services pour les opérations de Registre et communication | Perte CName |
| Données identité appareil identité Registre | 0-5 minutes les pertes de données |
| Messages d’appareil-nuage | Tous les messages non lus sont perdus |
| Opérations de surveillance des messages | Tous les messages non lus sont perdus |
| Messages cloud vers le périphérique | 0-5 minutes les pertes de données |
| File d’attente cloud vers le périphérique commentaires | Tous les messages non lus sont perdus |

## <a name="regional-failover-with-iot-hub"></a>Basculement régionaux avec concentrateur IoT

Un traitement complet des topologies de déploiement dans les solutions IoT est en dehors de l’étendue de cet article, mais en vue de disponibilité et reprise, nous allons étudier le modèle de déploiement *basculement régionaux* .

Dans un modèle de basculement régionaux, le serveur principal de solution s’exécute principalement dans un emplacement du centre de données, mais un concentrateur IoT supplémentaire et serveur principal sont déployées dans un autre emplacement de centre de données à des fins de basculement, en cas d’interruption ou le hub IoT dans le centre de données principal la connectivité réseau de l’appareil au centre de données principal est interrompue d’une certaine manière. Périphériques utilisent un point de terminaison du service secondaire chaque fois que la passerelle principale ne peuvent pas être atteintes. Avec une fonctionnalité de basculement entre région, la disponibilité de la solution peut être améliorée au-delà de la disponibilité d’une région unique.

À un niveau élevé, pour mettre en œuvre d’un modèle de basculement régionaux avec IoT concentrateur, vous devez les éléments suivants.

* **Un concentrateur IoT et appareil routage logique secondaire**: dans le cas d’interruption de service dans votre région principale, appareils doivent commencent à se connecter à votre région secondaire. Étant donné la nature prenant en charge les États de la plupart des services impliqués, il est courant pour les administrateurs de solution déclencher le processus de basculement entre région. La meilleure façon de communiquer le nouveau point de terminaison aux périphériques, tout en conservant le contrôle du processus, est demandez-lui de vérifier régulièrement *un Hervé du point de terminaison active en cours* . Le service conseiller peut être une application web simple qui est répliquée et conservée accessible à l’aide des techniques de la redirection DNS (par exemple, à l’aide du [Gestionnaire de trafic Azure][]).
* **Réplication de Registre identité** - afin de pouvoir l’utiliser, le secondaire IoT concentrateur doit contenir toutes les identités appareil qui peuvent se connecter à la solution. La solution doit conserver des sauvegardes répliquées geo d’identités des appareils et les télécharger dans le hub IoT secondaire avant de changer le point de terminaison active pour les appareils. La fonctionnalité d’exportation identité périphérique du concentrateur IoT est très utile dans ce contexte. Pour plus d’informations, voir le [Guide du développeur IoT concentrateur - Registre identité][].
* **Fusion de logique** - lorsque la région primaire redevient disponible, tous les États et les données qui ont été créées dans le site secondaire doivent être migrées arrière vers la zone principale. Cela concerne principalement identités des appareils et meta données de l’application, qui doivent être fusionnées avec le hub IoT primaire et tous les autres magasins spécifiques à l’application dans la région principale. Pour simplifier cette étape, il est généralement recommandé que vous utilisez idempotent opérations. Cela permet de minimiser des effets secondaires à partir d’une diffusion homogène éventuelle des événements, mais également de doublons ou d’expiration de livraison des événements. En outre, la logique d’application doit être conçue pour tolérer potentiels incohérences ou « légèrement » se déconnecter de date d’état. Ceci est en raison de la durée supplémentaire que nécessaire pour le système de « correction » basé sur récupération point objectifs.

## <a name="next-steps"></a>Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT concentrateur :

- [Prise en main IoT Hubs (didacticiel)][lnk-get-started]
- [Qu’est Azure IoT concentrateur ?][]

[Reprise d’activité et disponibilité des applications Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Gestionnaire de trafic Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guide du développeur IoT concentrateur - Registre identité]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est Azure IoT concentrateur ?]: iot-hub-what-is-iot-hub.md
