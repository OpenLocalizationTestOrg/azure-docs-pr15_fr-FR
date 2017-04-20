<properties 
   pageTitle="Comment effectuer une migration à partir de groupes affinité à un réseau virtuel régionaux (VNet)"
   description="Découvrez comment migrer à partir de groupes affinité à vnets régionaux"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Comment effectuer une migration à partir de groupes affinité à un réseau virtuel régionaux (VNet)

Vous pouvez utiliser un groupe affinité pour vous assurer que les ressources créées au sein du même groupe affinité sont physiquement hébergés par des serveurs qui sont proches, l’activation de ces ressources communiquer plus rapidement. Par le passé, affinités ont été une obligation pour créer des réseaux virtuels (VNets). À ce stade, le service du Gestionnaire de réseau gérées VNets pourrait fonctionnent uniquement au sein d’un ensemble de serveurs physiques ou unité d’échelle. Architecturales améliorations ont augmenté l’étendue de gestion de réseau à une région.

À la suite de ces améliorations architecturales, affinités ne sont plus recommandées, ou requis pour les réseaux virtuels. L’utilisation des groupes affinité pour VNets est remplacée par régions. VNets qui sont associées à des régions sont appelées VNets régionaux.

En outre, nous vous recommandons de que vous n’utilisez pas affinités en général. Outre l’exigence VNet, affinités ont été également importantes de permettent de ressources, tels que le cluster et le stockage, passées proches. Toutefois, avec l’architecture réseau Azure actuelle, ces exigences de position ne sont plus nécessaires. Voir [affinités et machines virtuelles](#Affinity-groups-and-VMs) pour les quelques autres cas spécifiques dans lequel vous souhaitez utiliser un groupe affinité.

## <a name="creating-and-migrating-to-regional-vnets"></a>Création et la migration vers VNets régionaux

Désormais, lorsque vous créez de nouveaux VNets, utilisez *la région*. Vous verrez ceci en tant qu’option dans le portail de gestion. Notez que dans le fichier de configuration réseau, cela signifie en tant *qu’emplacement*.

>[AZURE.IMPORTANT] Bien qu’il soit toujours techniquement possible de créer un réseau virtuel est associé à un groupe affinité, il est inutile attrayante à le faire. Nouvelles fonctionnalités, telles que les groupes de sécurité réseau, sont disponibles uniquement lorsque vous utilisez un VNet régionaux et ne sont pas disponibles pour les réseaux virtuels qui sont associés à affinités.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>À propos de VNets actuellement associés aux groupes affinité

VNets qui sont actuellement associées affinités sont activées pour la migration vers VNets régionaux. Pour migrer vers un VNet régionaux, procédez comme suit :

1. Exporter le fichier de configuration réseau. Vous pouvez utiliser PowerShell ou le portail de gestion. Pour obtenir des instructions à l’aide du portail de gestion, voir [configurer votre VNet à l’aide d’un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Modifier votre fichier de configuration réseau, remplacent les anciennes valeurs avec les nouvelles valeurs. 

    > [AZURE.NOTE] L' **emplacement** est la région que vous avez spécifié pour le groupe affinité associé à votre VNet. Par exemple, si votre VNet est associé à un groupe affinité qui se trouve aux États-Unis Ouest, lors de la migration, votre emplacement doit pointer vers US ouest. 
    
    Modifiez les lignes suivantes dans votre fichier de configuration réseau, remplacer les valeurs par votre propre : 

    **Ancienne valeur :** \<VirtualNetworkSitename = « VNetUSWest » AffinityGroup = « VNetDemoAG »\> 

    **Nouvelle valeur :** \<VirtualNetworkSitename = « VNetUSWest » emplacement = « US ouest »\>

1. Enregistrer vos modifications et [Importer](virtual-networks-using-network-configuration-file.md) la configuration réseau dans Azure.

>[AZURE.NOTE] Cette migration n’entraîne pas d’interruption de service à vos services.

## <a name="affinity-groups-and-vms"></a>Affinités et machines virtuelles

Comme indiqué précédemment, affinités sont n’est plus généralement recommandées pour les machines virtuelles. Vous devez utiliser un groupe affinité uniquement lorsqu’un ensemble de machines virtuelles doit avoir la latence du réseau les plus faibles absolue entre les ordinateurs virtuels. En plaçant machines virtuelles dans un groupe d’affinité, les ordinateurs virtuels seront toutes placés dans la même cluster cluster ou unité d’échelle.

Il est important de noter qu’à l’aide d’un groupe affinité peut avoir deux, éventuellement négative, conséquences :

- Le jeu de tailles machine virtuelle est limités au jeu de tailles machine virtuelle offertes par l’unité d’échelle de calcul.

- Il existe une probabilité de ne pas pouvoir allouer un nouvel ordinateur virtuel. Cela se produit lorsque l’unité d’échelle spécifiques pour le groupe affinité n’appartient pas la capacité.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>Que faire si vous avez une machine virtuelle dans un groupe d’affinité

Machines virtuelles qui se trouvent dans un groupe affinité n’avez pas besoin de quitter le groupe affinité.

Une fois qu’une machine virtuelle est déployée, il est déployé sur une unité d’échelle unique. Affinités peuvent limiter le jeu de tailles de mémoire virtuelle disponibles pour un déploiement machine virtuelle, mais n’importe quel ordinateur virtuel existant qui est déployé est déjà limité à l’ensemble des tailles de mémoire virtuelle disponibles dans l’unité d’échelle dans lequel la machine virtuelle est déployée. Pour cette raison, la suppression d’un ordinateur virtuel à partir du groupe affinité n’a aucun effet.
 
