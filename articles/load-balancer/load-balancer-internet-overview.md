
<properties
   pageTitle="Internet en vue d’ensemble du programme d’équilibrage de charge | Microsoft Azure "
   description="Vue d’ensemble Internet susceptibles d’affecter équilibrage de charge et ses fonctionnalités. Fonctionne d’un équilibrage de charge pour Azure à l’aide de machines virtuelles et services cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internet-facing-load-balancer-overview"></a>Vue d’ensemble du équilibrage de charge qui fait face à Internet

Équilibrage de charge Azure mappe le public IP adresse et numéro de port du trafic entrant le IP adresse et le port numéro privé de la machine virtuelle et vice versa pour le trafic de réponse à partir de la machine virtuelle. Règles équilibrage de charge permettent de distribuer des types spécifiques du trafic entre plusieurs machines virtuelles ou services. Par exemple, vous pouvez répartir la charge du trafic demande web sur plusieurs serveurs web ou rôles web.

Pour un service cloud qui contient des instances des rôles web ou des rôles de travail, vous pouvez définir un point de terminaison public dans le fichier de définition (.csdef) service.

Le fichier _servicedefinition.csdef_ contient la configuration de point de terminaison et lorsque vous avez plusieurs instances de rôles d’un déploiement de rôle web ou collaborateur, l’équilibrage de charge sera le programme d’installation de celui-ci. La façon d’ajouter des instances à votre déploiement de cloud consiste à modifier le nombre d’instances de sur le fichier de configuration de service (.csfg).

La figure suivante illustre un point de terminaison équilibrage de charge pour le trafic web chiffré qui est partagé entre les trois machines virtuelles pour le port TCP privée et 443. Ces trois machines virtuelles sont dans un jeu d’équilibrage de charge.

![exemple d’équilibrage de charge public](./media/load-balancer-internet-overview/IC727496.png))

Figure 1 : équilibrage de point de terminaison pour le trafic web chiffré

Lorsque les clients Internet envoient des demandes de page web vers l’adresse IP du service cloud sur le port TCP 443, l’équilibrage de charge Azure distribue les demandes entre les trois machines virtuelles dans l’ensemble d’équilibrage de charge. Pour plus d’informations sur les algorithmes d’équilibrage de charge, voir la [page Vue d’ensemble d’équilibrage de charger](load-balancer-overview.md#load-balancer-features).

Par défaut, équilibrage de charge Azure répartit le trafic réseau entre plusieurs instances de machine virtuelle. Vous pouvez également configurer affinité session, pour plus d’informations, voir [mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’équilibrage de charge interne](load-balancer-internal-overview.md) afin de mieux comprendre l’équilibrage de charge est plus adaptée à votre déploiement de cloud.

Vous pouvez également [commencer à créer un Internet facing équilibrage de charge](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement de trafic réseau charge spécifique équilibrage.

Si votre application doit afin de maintenir des connexions pour les serveurs derrière un programme d’équilibrage de charge, vous pouvez savoir plus sur [les paramètres de délai TCP inactives pour un équilibrage de charge](load-balancer-tcp-idle-timeout.md). Il aidera pour en savoir plus sur le comportement des connexions inactives lorsque vous utilisez l’équilibrage de charge Azure.
