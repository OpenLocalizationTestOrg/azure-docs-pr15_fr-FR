<properties
    pageTitle="Vue d’ensemble du protocole IPv6 pour équilibrage de charge Azure | Microsoft Azure"
    description="Présentation de prise en charge du protocole IPv6 équilibrage de charge Azure et machines virtuelles équilibrage de charge."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="le protocole IPv6, équilibrage de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Vue d’ensemble du protocole IPv6 pour équilibrage de charge Azure

Programmes d’équilibrage de charge via Internet peuvent être déployés avec une adresse IPv6. En plus de connectivité IPv4, ainsi, les fonctionnalités suivantes :

* Bout en bout connectivité IPv6 native entre les clients Internet publics et Machines virtuelles Azure (machines virtuelles) via l’équilibrage de charge.
* Bout en bout connectivité IPv6 native sortante entre machines virtuelles et des clients Internet IPv6 publics.

L’image suivante illustre les fonctionnalités du protocole IPv6 pour équilibrage de charge Azure.

![Équilibrage de charge Azure IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Une fois déployé, un client IPv4 ou Internet compatibles IPv6 peut communiquer avec les adresses IPv4 ou IPv6 public (ou les noms d’hôtes) de l’équilibrage de charge via Internet Azure. L’équilibrage de charge achemine les paquets du protocole IPv6 pour les adresses IPv6 privées des ordinateurs virtuels à l’aide de la traduction d’adresses réseau (NAT). Le client Internet IPv6 ne peuvent pas communiquer directement avec l’adresse IPv6 des ordinateurs virtuels.

## <a name="features"></a>Fonctionnalités

Prise en charge du protocole IPv6 native pour les machines virtuelles déployé via le Gestionnaire de ressources Azure fournit :

1. Équilibrage de charge du protocole IPv6 services pour les clients IPv6 sur Internet
2. Points de terminaison IPv4 et IPv6 natif sur machines virtuelles (« double empilée »)
3. Entrantes et sortant lancé connexions IPv6 natives
4. Pris en charge les protocoles tels que TCP et UDP HTTP (S) permettent une gamme complète d’architectures de services

## <a name="benefits"></a>Avantages

Cette fonctionnalité permet des avantages suivants :

* Respecter les réglementations gouvernementales exigent que les nouvelles applications soient accessibles aux clients IPv6 uniquement
* Activer mobile et Internet des développeurs d’éléments (IOT) utiliser empilées double d’ordinateurs virtuels Azure (IPv4 + IPv6) pour la croissance mobile & IOT marchés d’adresses

## <a name="details-and-limitations"></a>Détails et limitations

Plus d’informations

* Le service DNS Azure contient les enregistrements de nom A IPv4 et IPv6 AAAA et répond avec les deux enregistrements pour l’équilibrage de charge. Le client choisit quelle adresse (IPv4 ou IPv6) pour communiquer avec.
* Lorsqu’une machine virtuelle établit une connexion à un appareil connecté à Internet IPv6 public, adresse IPv6 de la source de la machine virtuelle est adresse réseau traduite (NAT) vers l’adresse de l’équilibrage de charge du protocole IPv6.
* Machines virtuelles exécutant le système d’exploitation Linux doivent être configurés pour recevoir une adresse IP du protocole IPv6 via DHCP. La plupart des images dans la galerie Azure Linux sont déjà configurées pour prendre en charge du protocole IPv6 sans aucune modification. Pour plus d’informations, voir [Configuration DHCPv6 pour les machines virtuelles Linux](load-balancer-ipv6-for-linux.md)
* Si vous choisissez d’utiliser une sonde santé avec votre équilibrage de charge, créez une sonde IPv4 et utilisez-la avec points de terminaison IPv4 et IPv6. Si le service sur votre ordinateur virtuel s’arrête, points de terminaison IPv4 et IPv6 soient extraits de rotation.

Limitations

* Vous ne pouvez pas ajouter des règles d’équilibrage de charge charge du protocole IPv6 dans le portail Azure. Les règles peuvent être créés uniquement au moyen du modèle, infrastructure du langage commun, PowerShell.
* Vous ne pouvez pas mettre à niveau machines virtuelles existantes pour utiliser les adresses IPv6. Vous devez déployer des nouvelles machines virtuelles.
* Une seule adresse IPv6 peut être affectée à une seule interface réseau dans chaque machine virtuelle.
* Les adresses IPv6 publics ne peut pas être affectés à une machine virtuelle. Ils peuvent uniquement être affectées à un équilibrage de charge.
* Vous ne pouvez pas configurer la recherche DNS inversée pour les adresses IPv6 votre publics.
* Les ordinateurs virtuels avec les adresses IPv6 ne peuvent pas être membres d’un Service Cloud Azure. Ils peuvent être connectés à un réseau virtuel Azure (VNet) et communiquent avec eux via leurs adresses IPv4.
* Adresses IPv6 privé peuvent être déployés sur machines virtuelles individuelles dans un groupe de ressources, mais ne peut pas être déployés dans un groupe de ressources via jeux d’échelle.
* Machines virtuelles Azure ne peut pas se connecter via IPv6 vers d’autres machines virtuelles, autres services Azure ou appareils locaux. Ils peuvent uniquement communiquer avec l’équilibrage de charge Azure sur IPv6. Toutefois, ils peuvent communiquer avec les autres ressources à l’aide de IPv4.
* Protection de groupe de sécurité réseau (NSG) pour IPv4 est prise en charge dans les déploiements double pile (IPv4 + IPv6). NSGs ne s’appliquent pas aux points de terminaison du protocole IPv6.
* Le point de terminaison du protocole IPv6 sur l’ordinateur virtuel n’est pas exposé directement à internet. Il est protégé par un équilibrage de charge. Seuls les ports spécifiés dans les règles d’équilibrage de charge sont accessibles via IPv6.
* Modifier le paramètre IdleTimeout pour le protocole IPv6 est **actuellement pas pris en charge**. La valeur par défaut est de quatre minutes.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment déployer un équilibrage de charge du protocole IPv6.

* [Disponibilité du protocole IPv6 par région](https://go.microsoft.com/fwlink/?linkid=828357)
* [Déployer un équilibrage de charge du protocole IPv6 à l’aide d’un modèle](load-balancer-ipv6-internet-template.md)
* [Déployer un équilibrage de charge du protocole IPv6 à l’aide de PowerShell Azure](load-balancer-ipv6-internet-ps.md)
* [Déployer un équilibrage de charge du protocole IPv6 à l’aide d’infrastructure du langage commun Azure](load-balancer-ipv6-internet-cli.md)
