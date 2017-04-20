<properties 
   pageTitle="Comment utiliser les adresses IP publiques dans un réseau virtuel"
   description="Découvrez comment configurer un réseau virtuel pour utiliser des adresses IP publiques"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Espace d’adressage IP public dans un réseau virtuel (VNet)

Réseaux virtuels (VNets) peuvent contenir publiques et privées espaces d’adresse IP de (blocs d’adresses RFC 1918). Lorsque vous ajoutez une plage d’adresses IP publique, il sera traité dans le cadre de l’espace d’adressage VNet IP privé qui est uniquement accessible au sein de la VNet, VNets interconnecté et à partir de votre emplacement local.

L’image ci-dessous montre un VNet qui comporte des espaces d’adresse IP publiques et privées.

![Adresse IP publique conceptuelle](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>Comment ajouter une plage d’adresses IP publique ?

Vous ajoutez une plage d’adresses IP publique la même façon que vous ajouterez une plage d’adresses IP privées ; en utilisant un fichier *netcfg* ou en ajoutant la configuration du [portail Azure](http://portal.azure.com). Vous pouvez ajouter une plage d’adresses IP publique lorsque vous créez votre VNet, ou vous pouvez revenir en arrière et ajouter par la suite. L’exemple ci-dessous montre publiques et privées espaces d’adresses IP configurés dans le même VNet.

![Adresse IP publique dans le portail](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>Existe-t-il des restrictions ?

Il existe plusieurs plages d’adresses IP qui ne sont pas autorisés :

- 224.0.0.0/4 (multidiffusion)

- 255.255.255.255/32 (diffusion)

- 127.0.0.0/8 (en boucle)

- 169.254.0.0/16 (lien-local)

- 168.63.129.16/32 (DNS interne)

## <a name="next-steps"></a>Étapes suivantes

[Comment gérer les serveurs DNS utilisés par un VNet](virtual-networks-manage-dns-in-vnet.md)