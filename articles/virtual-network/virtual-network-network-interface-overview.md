<properties 
   pageTitle="Interfaces réseau | Microsoft Azure"
   description="Obtenir des informations sur les interfaces réseau Azure dans le Gestionnaire de ressources Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfaces réseau

Une interface réseau (carte réseau) est l’interconnexion entre une Machine virtuelle (machine virtuelle) et le réseau logiciel sous-jacent. Cet article explique quels une interface réseau est et comment il est utilisé dans le modèle de déploiement d’Azure le Gestionnaire de ressources.

Microsoft recommande le déploiement de nouvelles ressources à l’aide du modèle de déploiement Gestionnaire de ressources, mais vous pouvez également déployer des machines virtuelles avec la connectivité réseau dans le modèle de déploiement [classique](virtual-network-ip-addresses-overview-classic.md) . Si vous êtes habitué au modèle classique, il existe des différences importantes sur les réseaux de machine virtuelle dans le modèle de déploiement du Gestionnaire de ressources. En savoir plus sur les différences en lisant l’article [réseau machine virtuelle - classique](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

Dans Azure, une interface réseau :

1. Est une ressource qui peut être créée, supprimé, et dispose de sa propre paramètres configurables.
2. Doit être connecté à un seul sous-réseau dans un réseau virtuel Azure (VNet) lors de sa création. Si vous ne connaissez pas VNets, apprenez-en davantage sur les en lisant l’article [vue d’ensemble du réseau virtuel](virtual-networks-overview.md) . La carte réseau doit être connectée à un VNet qui existe dans la même Azure [emplacement](https://azure.microsoft.com/regions) et l' [abonnement](../azure-glossary-cloud-terminology.md#subscription) en tant que la carte réseau. Après la création d’une carte réseau, vous pouvez modifier le sous-réseau à qu'il est connecté, mais vous ne pouvez pas modifier la VNet il est connecté à.
3. A un nom affecté à ce que ne peut pas être modifié après la création de la carte réseau. Le nom doit être unique au sein d’un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups)Azure, mais ne doit pas être unique au sein de l’abonnement, l’emplacement Azure, qu'il est créé ou la VNet il est connecté à. Plusieurs cartes réseau est généralement créées au sein d’un abonnement Azure. Il est recommandé que vous concevoir une convention d’appellation qui facilite la gestion de plusieurs cartes réseau facilitent l’utilisation des noms par défaut. Consultez l’article [conventions d’appellation pour les ressources Azure recommandées](../guidance/guidance-naming-conventions.md) pour obtenir des suggestions.
4. Peuvent être reliés à une machine virtuelle, mais ne peut être attaché à un seul ordinateur virtuel qui existe dans le même emplacement que la carte réseau.
5. Contient une adresse MAC, qui est conservée avec la carte réseau pour tant qu’il reste reliée à une machine virtuelle. L’adresse MAC est conservé si le redémarrage de la machine virtuelle (à partir du système d’exploitation) ou arrêté (supprimer l’allocation) et l’utilisation de l’Interface de ligne de commande Azure, PowerShell Azure ou le portail Azure. Si elle a détaché à partir d’une machine virtuelle et joint à un autre ordinateur virtuel, la carte réseau reçoit une adresse MAC différente. Si la carte réseau est supprimée, l’adresse MAC est affectée à une autre carte réseau.
6. Doit lui ont un principal **privé** *IPv4* statique ou dynamique adresse IP affectées.
8. Peut avoir une ressource d’adresse IP publique associé à.
9. Prend en charge accélérée d’un réseau avec virtualisation racine unique e/s (SR IOV) pour des tailles spécifiques pour machine virtuelle en cours d’exécution des versions spécifiques du système d’exploitation Microsoft Windows Server. Pour en savoir plus sur cette fonctionnalité Aperçu, consultez l’article [Accelerated mise en réseau pour une machine virtuelle](virtual-network-accelerated-networking-powershell.md) .
10. Peuvent recevoir du trafic non destiné à des adresses IP privées est affectées à si le transfert IP est activé pour la carte réseau. Si une machine virtuelle s’exécute logiciel pare-feu par exemple, il achemine les paquets non destinés à ses propres adresses IP. La machine virtuelle doit toujours exécuter des logiciels capable d’acheminer ou transférer le trafic, mais pour ce faire, le transfert IP doit être activé pour une carte réseau.
11. Est souvent créée dans le même groupe de ressources que la machine virtuelle celui-ci est attaché ou le même VNet auquel il est connecté, même si elle n’est pas nécessaire pour être.

## <a name="vms-with-multiple-network-interfaces"></a>Machines virtuelles avec plusieurs interfaces réseau

Plusieurs cartes réseau peut être associés à une machine virtuelle, mais lorsque cela, tenir compte des éléments suivants :  

- La taille de la mémoire virtuelle doit prendre en charge plusieurs cartes réseau. Pour en savoir plus sur les tailles de mémoire virtuelle prend en charge plusieurs cartes réseau, lisez les articles [machine virtuelle Windows Server tailles](../virtual-machines/virtual-machines-windows-sizes.md) ou [tailles de machine virtuelle Linux](../virtual-machines/virtual-machines-linux-sizes.md) .   
- La machine virtuelle doit être créée avec au moins deux cartes réseau. Si la machine virtuelle est créée avec une carte réseau, même si la taille de la mémoire virtuelle prend en charge plusieurs, vous ne pouvez pas joindre des cartes réseau de la machine virtuelle après sa création. Dans la mesure où la machine virtuelle a été créée avec au moins deux cartes réseau, vous pouvez joindre des cartes réseau de la machine virtuelle après sa création, dans la mesure où la taille de la mémoire virtuelle prend en charge plus de deux cartes réseau.  
- Vous pouvez détacher cartes réseau secondaire (la carte réseau principale ne peut pas être détachée) à partir d’une machine virtuelle si la machine virtuelle comporte au moins trois cartes réseau est jointe à. Vous ne pouvez pas détacher cartes réseau si la machine virtuelle comporte deux ou moins cartes réseau est jointe à.  
- L’ordre des cartes de réseau à l’intérieur de la machine virtuelle est aléatoire et peut être modifié également entre Azure infrastructure mises à jour. Toutefois, les adresses IP et l’ethernet correspondant MAC adresses, restera identique. Par exemple, supposons que le système d’exploitation identifie NIC1 Azure comme Eth1. Eth1 contient les adresses IP 10.1.0.100 et MAC 00-0D-3A-B0-39-0D. Après une infrastructure Azure mettre à jour et redémarrer, le système d’exploitation peut identifier maintenant NIC1 Azure comme Eth2, mais les adresses IP et MAC sera la même tels qu’ils étaient lorsque le système d’exploitation identifié NIC1 Azure comme Eth1. Lorsqu’un redémarrage est exécutée par le client, l’ordre de carte réseau restera identique au sein du système d’exploitation.  
- Si la machine virtuelle est un membre d’une [disponibilité définie](../azure-glossary-cloud-terminology.md#availability-set), tous les ordinateurs virtuels dans l’ensemble de disponibilité doivent avoir une seule carte réseau ou plusieurs cartes réseau. Si les ordinateurs virtuels ont plusieurs cartes réseau, le numéro ils ont chacun n’est pas nécessairement être identiques, dans la mesure où chaque machine virtuelle comporte au moins deux cartes réseau.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à créer une machine virtuelle avec une seule carte réseau en lisant l’article [créer une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Apprenez à créer une machine virtuelle avec plusieurs cartes réseau en lisant l’article [déployer une machine virtuelle avec plusieurs cartes réseau](virtual-network-deploy-multinic-arm-ps.md) .
- Découvrez comment créer une carte réseau avec plusieurs configurations IP en lisant l’article [adresses IP multiples pour machines virtuelles Azure](virtual-network-multiple-ip-addresses-powershell.md) .
