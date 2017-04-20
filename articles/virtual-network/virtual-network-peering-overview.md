
<properties
   pageTitle="Réseau virtuel Azure peering | Microsoft Azure"
   description="En savoir plus sur VNet peering dans Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>VNet peering

VNet peering est un mécanisme qui se connecte deux réseaux virtuels (VNets) dans la même région via le réseau principal Azure. Une fois que ressources, les deux réseaux virtuels apparaissent sous forme de l’autre pour tous les rôles de connectivité. Ils sont toujours gérées comme des ressources distinctes, mais machines virtuelles dans ces réseaux virtuels puissent communiquer avec eux directement à l’aide des adresses IP privées.

Le trafic entre des machines virtuelles dans les réseaux virtuels ressources est routé via l’infrastructure Azure de la même manière que le trafic est acheminé entre machines virtuelles dans le même réseau virtuel. Voici quelques-uns des avantages de l’utilisation VNet peering :

- Une connexion de faible latence, haut débit entre les ressources dans des réseaux virtuels différents.
- La possibilité d’utiliser des ressources comme des équipements de réseau et de passerelles VPN en tant que points voies dans un VNet ressources.
- La possibilité de se connecter à un réseau virtuel qui utilise le modèle Azure le Gestionnaire de ressources à un réseau virtuel qui utilise le modèle de déploiement classique et activer la connectivité complète entre les ressources dans ces réseaux virtuels.

Configuration requise et aspects clés de VNet peering :

- Les deux réseaux virtuels sont ressources qui doivent être dans la même région Azure.
- Réseaux virtuels sont ressources doivent avoir espaces d’adresses IP non superposés.
- VNet peering est comprise entre deux réseaux virtuels, et il n’existe aucune relation verbes dérivée. Par exemple, si réseau virtuel A est ressources avec le réseau virtuel B et si réseau virtuel B est ressources avec le réseau virtuel C, il ne se traduit pas virtuel au réseau une ressources en cours avec réseau virtuel C.
- Peering peut être établie entre les réseaux virtuels dans deux différents abonnements comme long un utilisateur privilégié des deux abonnements autorise le peering et les abonnements sont associés à la même client Active Directory. 
- Peering entre réseau virtuel dans le modèle de gestionnaire de ressources et le modèle de déploiement classique nécessite que les VNets doit se trouver dans le même abonnement.
- Un réseau virtuel qui utilise le modèle de déploiement du Gestionnaire de ressources peut être peered avec un autre réseau virtuel qui utilise ce modèle, ou avec un réseau virtuel qui utilise le modèle de déploiement classique. Toutefois, réseaux virtuels qui utilisent le modèle de déploiement classique ne peut pas être peered entre eux.
- Bien que la communication entre des machines virtuelles dans des réseaux virtuels ressources n’a aucune restriction de bande passante supplémentaire, lettrine de bande passante en fonction de la taille de mémoire virtuelle s’applique toujours.


![Base VNet peering](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connectivité
Une fois que deux réseaux virtuels sont ressources, une machine virtuelle (rôle web/travail) dans le réseau virtuel peuvent se connecter directement avec d’autres machines virtuelles dans le réseau virtuel ressources. Ces deux réseaux ont niveau IP complè connectivity.

La latence du réseau pour un aller-retour entre deux machines virtuelles dans des réseaux virtuels ressources est identique à celle d’aller-retour dans un réseau local virtuel. Le débit réseau est basé sur la bande passante qui est autorisée pour la machine virtuelle proportionnellement à sa taille. Il n’existe pas aucune restriction supplémentaire sur la bande passante.

Le trafic entre les machines virtuelles dans des réseaux virtuels ressources est routé directement par le biais de l’infrastructure principale Azure et non à l’aide d’une passerelle.

Machines virtuelles dans un réseau virtuel peuvent accéder les internes extrémités d’équilibrage de charge (ILB) dans le réseau virtuel ressources. Groupes de sécurité réseau (NSGs) peuvent être appliquées dans un réseau virtuel pour bloquer l’accès à d’autres réseaux virtuels ou sous-réseaux si vous le souhaitez.

Lorsque les utilisateurs configurent peering, ils peuvent les ouvrir ou fermer les règles NSG entre les réseaux virtuels. Si l’utilisateur choisit d’ouvrir une connectivité totale entre ressources réseaux virtuels (qui est l’option par défaut), ils peuvent ensuite utiliser NSGs sur sous-réseaux spécifiques ou machines virtuelles bloquer ou refuser l’accès spécifique.

Azure communiquée interne résolution de noms DNS pour les machines virtuelles ne fonctionne pas sur les ressources réseaux virtuels. Machines virtuelles ont des noms DNS internes qui peuvent être résolus uniquement au sein du réseau local virtuel. Toutefois, les utilisateurs peuvent configurer des machines virtuelles qui s’exécutent dans des réseaux virtuels ressources comme des serveurs DNS pour un réseau virtuel.

## <a name="service-chaining"></a>Chaîne de service
Les utilisateurs peuvent configurer des tables de routage définies par l’utilisateur qui pointent vers des machines virtuelles dans des réseaux virtuels ressources comme « saut suivant » adresse IP, comme indiqué dans le diagramme plus loin dans cet article. Cela permet aux utilisateurs obtenir la chaîne de service, via lequel ils peuvent diriger le trafic à partir d’un réseau virtuel à un matériel virtuel qui s’exécute dans un réseau virtuel ressources dans les tables itinéraire définies par l’utilisateur.

Les utilisateurs peuvent également efficacement créer environnements en roue type où le hub peut héberger des composants de l’infrastructure comme un dispositif virtuel réseau. Tous les réseaux virtuel étoile pouvant puis homologue avec elle, ainsi qu’un sous-ensemble de trafic pour les appareils qui s’exécutent dans le réseau virtuel concentrateur. En bref, VNet peering permet à l’adresse IP du plafond du suivante sur la « définies par l’utilisateur table itinéraire » à l’adresse IP d’un ordinateur virtuel dans le réseau virtuel ressources.

## <a name="gateways-and-on-premises-connectivity"></a>Connectivité passerelles et en local
Chaque réseau virtuel, indépendamment de si elle est ressources avec un autre réseau virtuel, peut toujours avoir son propre passerelle et l’utiliser pour vous connecter à local. Utilisateurs peuvent également configurer [les connexions VNet-VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) à l’aide de passerelles, même si les réseaux virtuels sont ressources.

Lorsque les deux options pour l’interconnectivité réseau virtuel sont configurées, le trafic entre les réseaux virtuels continue à travers la configuration homologation (autrement dit, par le réseau principal Azure).

Lorsque les réseaux virtuels sont ressources, les utilisateurs peuvent également configurer la passerelle dans le réseau virtuel ressources comme point de voies vers local. Dans ce cas, le réseau virtuel qui utilise une passerelle à distance ne peut pas contenir de sa propre passerelle. Un réseau virtuel peut avoir qu’une seule passerelle. Il peut être une passerelle locale ou une passerelle à distance (dans le réseau virtuel ressources), comme illustré dans l’image suivante.

Voies passerelle n’est pas pris en charge dans la relation entre les réseaux virtuels à l’aide du modèle de gestionnaire de ressources et ceux qui utilisent le modèle de déploiement classique homologation. Les deux réseaux virtuels dans la relation homologation devez utiliser le modèle de déploiement Gestionnaire de ressources pour un voies passerelle pour l’utiliser.

Lorsque les réseaux virtuels qui partagent une seule connexion Azure ExpressRoute sont ressources, le trafic entre elles parcourt la relation homologation (autrement dit, via le réseau principal Azure). Les utilisateurs peuvent toujours utiliser passerelles locales dans chaque réseau virtuel pour vous connecter au circuit local. Par ailleurs, ils peuvent utiliser une passerelle partagée et configurer voies pour la connectivité locale.

![Voies homologation VNet](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Mise en service
VNet peering est une opération dotés de privilèges. Il est une fonction séparée sous l’espace de noms VirtualNetworks. Un utilisateur peut être donné des droits spécifiques à autoriser peering. Un utilisateur qui a accès en lecture / écriture au réseau virtuel hérite ces droits automatiquement.

Un utilisateur qui est un administrateur ou un utilisateur privilégié de la capacité homologation pouvez débuter une homologation sur un autre VNet. S’il existe une demande de correspondance pour peering de l’autre côté et si d’autres conditions sont remplies, les peering sera établie.

Consultez les articles dans la section « Étapes suivantes » pour en savoir plus sur l’établissement de VNet peering entre deux réseaux virtuels.

## <a name="limits"></a>Limites
Il existe des limites sur le nombre de peerings autorisées pour un seul réseau virtuel. Pour plus d’informations, voir [limites de mise en réseau Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Tarifs
VNet peering sera gratuitement pendant la période de révision. Après leur publication, il sera frais nominal sur le trafic entrant et sortant qui utilise le peering. Pour plus d’informations, reportez-vous à la [page de tarification](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Étapes suivantes
- [Configurer la peering entre les réseaux virtuels](virtual-networks-create-vnetpeering-arm-portal.md).
- En savoir plus sur [NSGs](virtual-networks-nsg.md).
- Découvrez les [itinéraires définies par l’utilisateur et transfert IP](virtual-networks-udr-overview.md).
