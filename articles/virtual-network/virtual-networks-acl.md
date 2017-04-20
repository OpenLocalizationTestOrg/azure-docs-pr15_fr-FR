<properties
   pageTitle="Qu’est une liste de contrôle (accès) de réseau ?"
   description="En savoir plus sur les utilisateurs"
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

# <a name="what-is-an-endpoint-access-control-list-acls"></a>Qu’est un point de terminaison liste de contrôle d’accès (utilisateurs) ?

Un point de terminaison de contrôle de liste d’accès est une amélioration de la sécurité disponible pour votre déploiement d’Azure. Un et offre la possibilité de manière sélective autoriser ou refuser le trafic de point de terminaison de la machine virtuelle. Cette fonctionnalité de filtrage de paquets fournit un niveau de sécurité supplémentaire. Vous pouvez spécifier des utilisateurs de réseau pour les points de terminaison uniquement. Vous ne pouvez pas spécifier un et un réseau virtuel ou un sous-réseau spécifique contenues dans un réseau virtuel.

> [AZURE.IMPORTANT] Il est recommandé d’utiliser des groupes de sécurité réseau (NSGs) au lieu d’utilisateurs dès que possible. Pour en savoir plus sur NSGs, voir [qu’est un groupe de sécurité réseau ?](virtual-networks-nsg.md).

Utilisateurs peuvent être configurés à l’aide de PowerShell ou le portail de gestion. Pour configurer un réseau et à l’aide de PowerShell, voir [Gestion des accès aux listes de contrôle () pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md). Pour configurer un réseau et à l’aide du portail de gestion, Découvrez [comment définir des points de terminaison pour une Machine virtuelle](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

À l’aide des utilisateurs du réseau, vous pouvez procédez comme suit :

- Sélectivement autoriser ou interdire le trafic entrant basé sur sous-réseau distant plage d’adresses IPv4 à un point de terminaison d’entrée machine virtuelle.

- Liste de blocage des adresses IP

- Créer plusieurs règles par point de terminaison machine virtuelle

- Spécifier les règles et jusqu'à 50 par point de terminaison machine virtuelle

- Classement des règles d’utilisation pour garantir le jeu correct de règles sont appliquées à un point de terminaison donné machine virtuelle (plus basse à la plus élevée)

- Spécifiez un et pour un sous-réseau à distance spécifique adresse IPv4.

## <a name="how-acls-work"></a>Comment fonctionnent les utilisateurs

Un et est un objet qui contient la liste des règles. Lorsque vous créez un et et appliquez à un point de terminaison machine virtuelle, le filtrage de paquets a lieu sur le nœud hôte de votre machine virtuelle. Cela signifie que le trafic provenant d’adresses IP distantes est filtré par le nœud hôte pour les règles et de correspondance plutôt que sur votre ordinateur virtuel. Ainsi, votre machine virtuelle dépenses les cycles de processeur précieux sur le filtrage de paquets.

Lorsqu’une machine virtuelle est créée, une valeur par défaut et est mis en place pour bloquer tout le trafic entrant. Toutefois, si un point de terminaison est créé pour (Port3389), puis la valeur par défaut et a été modifiée pour autoriser tout le trafic entrant pour ce point de terminaison. Le trafic entrant à partir de n’importe quel sous-réseau distant est autorisé à ce point de terminaison et aucune mise en service de pare-feu n’est requise. Tous les autres ports sont bloqués pour le trafic entrant, sauf si les points de terminaison sont créés pour les ports. Le trafic sortant est autorisé par défaut.

**Exemple de tableau par défaut et**

| **Règle #** | **Sous-réseau distant** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Autoriser      |

## <a name="permit-and-deny"></a>Autorisation et de refus

Vous pouvez sélectivement autoriser ou interdire le trafic réseau pour un point de terminaison d’entrée machine virtuelle en créant des règles qui spécifient « autoriser » ou « non ». Il est important de noter que par défaut, lors de la création d’un point de terminaison, tout le trafic est autorisé au point de terminaison. Pour cette raison, il est important de comprendre comment créer des règles de refus de l’autorisation/et placez-les dans l’ordre de priorité approprié si vous souhaitez contrôler le trafic réseau que vous souhaitez autoriser pour atteindre le point de terminaison machine virtuelle granulaire.

Points à prendre en compte :

1. **Aucun et –** Par défaut lors de la création d’un point de terminaison, nous autoriser tout pour le point de terminaison.

1. **Autoriser-** Lorsque vous ajoutez un ou plusieurs « autorisent » plages, vous refusez toutes les autres plages par défaut. Seuls les paquets à partir de la plage d’adresses IP autorisée seront en mesure de communiquer avec le point de terminaison machine virtuelle.

1. **Refuser-** Lorsque vous ajoutez un ou plusieurs « refuser » plages, vous êtes autorisé à tous les autres plages du trafic par défaut.

1. **Combinaison d’autorisation et de refus-** Vous pouvez utiliser une combinaison de « autoriser » et « refuser » lorsque vous voulez imaginer une plage IP spécifique pour être autorisé ou non.

## <a name="rules-and-rule-precedence"></a>Règles et la priorité des règles

Utilisateurs du réseau peuvent être configurés sur les points de terminaison machine virtuelle spécifique. Par exemple, vous pouvez spécifier un réseau et un point de terminaison de RDP créée sur un ordinateur virtuel qui verrouille accès pour certaines adresses IP. Le tableau ci-dessous indique un moyen pour accorder l’accès au public virtuel adresses IP (VIP) d’une certaine plage à autoriser l’accès à RDP. Toutes les adresses IP distante sont refusées. Un ordre *a la plus basse priorité* des règles à suivre.

### <a name="multiple-rules"></a>Plusieurs règles

Dans l’exemple ci-dessous, si vous voulez autoriser l’accès au point de terminaison RDP uniquement à partir de deux publics plages d’adresses IPv4 (65.0.0.0/8 et 159.0.0.0/8), vous pouvez obtenir ceci en définissant des deux règles *Autoriser* . Dans ce cas, étant donné que RDP est créé par défaut pour une machine virtuelle, vous souhaiterez bloquer l’accès au port RDP basé sur un sous-réseau distant. L’exemple ci-dessous montre un moyen pour accorder l’accès au public virtuel adresses IP (VIP) d’une certaine plage à autoriser l’accès à RDP. Toutes les adresses IP distante sont refusées. Cela fonctionne car réseau utilisateurs peut être configuré pour un point de terminaison machine virtuelle spécifique et accès refusé par défaut.

**Exemple – plusieurs règles**

| **Règle #** | **Sous-réseau distant** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Autoriser      |
| 200    | 159.0.0.0/8   | 3389     | Autoriser      |

### <a name="rule-order"></a>Ordre de la règle

Étant donné que plusieurs règles peuvent être spécifiés pour un point de terminaison, il faut classer les règles afin de déterminer quelle règle est prioritaire. L’ordre des règles spécifie la priorité. Utilisateurs du réseau suivent un ordre de règle *a la plus basse priorité* . Dans l’exemple ci-dessous, le point de terminaison sur le port 80 sélectivement a accès uniquement certaines plages d’adresses IP. Pour ce faire, nous avons une règle de refus (règle \# 100) pour les adresses dans l’espace 175.1.0.1/24. Une deuxième règle est puis spécifiée avec la priorité des 200 qui autorise l’accès à toutes les autres adresses sous 175.0.0.0/8.

**Exemple : la priorité des règles**

| **Règle #** | **Sous-réseau distant** | **Point de terminaison** | **Autoriser/Refuser** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Refuser        |
| 200    | 175.0.0.0/8   | 80       | Autoriser      |

## <a name="network-acls-and-load-balanced-sets"></a>Utilisateurs du réseau et de chargement des jeux équilibrée

Utilisateurs du réseau peuvent être spécifiés sur un point de terminaison équilibrée de charge jeu (kg définie). Si une et n’est spécifié pour un ensemble de livres, le réseau et s’applique à tous les ordinateurs virtuels dans ce jeu de kg. Par exemple, si un ensemble de kg est créé avec « Port 80 » et que l’ensemble de kg contient 3 machines virtuelles, le réseau et créé sur le point de terminaison « Port 80 » d’une que machine virtuelle s’appliquent automatiquement aux autres ordinateurs virtuels.

![Utilisateurs du réseau et de chargement des jeux équilibrée](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Étapes suivantes

[Comment gérer les listes de contrôle d’accès (utilisateurs) pour les points de terminaison à l’aide de PowerShell](virtual-networks-acl-powershell.md)
