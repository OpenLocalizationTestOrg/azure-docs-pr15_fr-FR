<properties
   pageTitle="Déploiement d’authentification multifacteur dans disponibilité | Microsoft Azure"
   description="Comment déployer authentification multifacteur réseau dans disponibilité."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Déploiement d’authentification multifacteur dans disponibilité

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de pratiques pour le déploiement réseau authentification multifacteur (NVAs) d’une manière hautement disponible. Avant de poursuivre, assurez-vous que vous savez comment [itinéraires définies par l’utilisateur (UDR)] [ udr-overview] et [l’équilibrage de charge] [ lb-overview] Professionnel dans Azure.

Vous pouvez utiliser différents NVAs disponibles sur le marché Azure pour étendre les fonctionnalités d’Azure de la même façon que vous utilisez des appareils dans votre centre de données locale. La figure suivante montre un exemple de déploiement d’un [seul à couloirs] [ nva-scenario] une application de pare-feu. 

![[0]][0]

Bien que le déploiement précédent fonctionne, il présente un point de défaillance unique. Si l’application virtuelle échoue, aucun trafic ne continue. Pour résoudre ce problème, vous devez utiliser plusieurs NVAs. Toutefois, qui requiert également d’autres ressources et des paramètres à utiliser selon vos besoins.

Vous pouvez utiliser une des solutions suivantes pour déployer un environnement à couloirs hautement disponible.

|Solution|Avantages|Considérations relatives à la|
|---|---|---|
|Pénétration avec authentification multifacteur couche 7|Tous les nœuds sont actifs|Nécessite un à couloirs qui peuvent mettre fin aux connexions et utiliser SNAT<br/>Nécessite un ensemble de NVAs distinctes pour le trafic entrant à partir d’Internet et à partir d’Azure<br/>Peut uniquement être utilisé pour le trafic d’origine en dehors d’Azure|
|Pénétration-sortie avec authentification multifacteur couche 7|Tous les nœuds sont actifs<br/>En mesure de gérer le trafic a été créée dans Azure |Nécessite un à couloirs qui peuvent mettre fin aux connexions et utiliser SNAT<br/>Nécessite un ensemble de NVAs distinctes pour le trafic entrant à partir d’Internet et à partir d’Azure|
|Commutateur PIP UDR|Ensemble unique de NVAs pour tout le trafic<br/>Peut gérer l’ensemble du trafic (aucune limite sur les règles de port)|Actif-passif<br/>Nécessite un processus de basculement|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Pénétration avec authentification multifacteur couche 7
Vous pouvez utiliser un ensemble de NVAs derrière un équilibrage de charge Azure pour fournir une connectivité aux charges de travail Azure derrière un ensemble réduit de ports côté serveur (par exemple, HTTP et HTTPS). La figure suivante met en évidence comment fournir une grande disponibilité dans ce scénario au niveau à couloirs.

![[1]][1]

Dans ce scénario, le matériel virtuel réseau est utilisé doit mettre fin à toutes les connexions et les passer au sous-réseau la charge de travail. Machines virtuelles la charge de travail (machines virtuelles) répondre à l’à couloirs reçu une demande à partir d’et flux de trafic sans problème. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Pénétration-sortie avec authentification multifacteur couche 7
Vous pouvez étendre l’architecture précédente et ajouter un jeu supplémentaire de NVAs pour gérer le trafic provenant d’Azure devant être traité par NVAs, comme illustré dans l’illustration suivante :

![[2]][2]

Dans ce scénario, tout le trafic d’origine dans Azure est acheminé vers un équilibrage de charge interne qui répartit charge entre un jeu de NVAs différent. Ces NVAs diriger le trafic vers Internet en utilisant les adresses IP publiques individuels. 

## <a name="pip-udr-switch"></a>Commutateur PIP UDR
Vous pouvez éviter de créer plusieurs piles à couloirs à l’aide de deux NVAs en mode actif-passif. Dans ce scénario, vous pouvez basculer l’adresse IP publique (PIP) et les itinéraires définies par l’utilisateur (UDRs) lorsque le nœud actif s’arrête.  

![[3]][3]

Ce scénario est semblable au scénario à couloirs unique. La seule différence est que le PIP et UDRs doivent être modifiées pour le trafic entre les NVAs. Ces modifications peuvent être effectuées manuellement, ou vous pouvez également automatiser. Pour automatiser, vous pouvez déployer une application sur les deux NVAs qui vérifie l’état du nœud actif. Une fois le nœud actif vers le bas, votre application peut modifier la PIP et UDRs pour créer un lien vers le nœud passif.

Une implémentation possible de cette solution consiste à utiliser un [soigneur] [ zookeeper] processus sous les NVAs pour gérer des élections points de suite (décider quel nœud est le nœud actif). Une fois qu’un responsable est choisi, il appelle à l’API REST Azure pour supprimer le PIP à partir du nœud échec et joindre à la suite. Il doit également modifier UDRs pour qu’il pointe vers adresse IP privée de l’organisateur de la nouvelle.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment mettre en [œuvre une DMZ entre Azure et votre centre de données locale] [ dmz-on-prem] à l’aide de la couche 7 NVAs.
- Découvrez comment mettre en [œuvre une DMZ entre Azure et Internet] [ dmz-internet] à l’aide de la couche 7 NVAs.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Architecture à couloirs unique"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Pénétration couche 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "Calque 7 entrant et sortant"
[3]: ./media/guidance-nva-ha/active-passive.png "Cluster actif-passif"