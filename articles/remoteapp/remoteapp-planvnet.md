<properties
    pageTitle="Comment planifier votre réseau virtuel d’une collection de RemoteApp Azure | Microsoft Azure"
    description="Apprenez à planifier votre réseau virtuel d’une collection de RemoteApp Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Comment planifier votre réseau virtuel RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Ce document explique comment configurer votre réseau virtuel Azure (VNET) et le sous-réseau de RemoteApp Azure. Si vous connaissez mal les réseaux virtuels Azure, c’est une fonctionnalité qui vous permet de virtualisation de votre infrastructure réseau dans le cloud et à créer des solutions hybride avec Azure et vos ressources locales. Vous pouvez en savoir plus à ce sujet [ici](../virtual-network/virtual-networks-overview.md).

Si vous voulez définir des stratégies de sécurité pour le trafic (entrant et sortant) de votre réseau virtuel où vous déployez RemoteApp Azure, nous vous recommandons vivement de création d’un sous-réseau distinct pour Azure RemoteApp du reste de votre déploiement dans le réseau virtuel Azure. Pour plus d’informations sur la définition des stratégies de sécurité sur votre sous-réseau Azure réseau virtuel, veuillez lire [qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Types de collections de RemoteApp Azure Azure réseaux virtuels

Les graphiques suivants illustrent les deux options de collection de sites différentes lorsque vous voulez utiliser un réseau virtuel.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Collection de cloud RemoteApp Azure avec VNET

 ![Azure RemoteApp - collection Cloud avec un VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Cela représente une collection de RemoteApp Azure où la session RemoteApp hôtes doivent pouvoir accéder à toutes les ressources sont déployés dans Azure. Elles peuvent être dans la même VNET en tant que la RemoteApp VNET ou un autre VNET dans Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Collection de hybride RemoteApp Azure avec VNET

![Azure RemoteApp - collection hybride avec un VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Cela représente une collection de RemoteApp Azure où certaines des ressources qui les hôtes session RemoteApp doivent pouvoir accéder à sont déployés en local. La RemoteApp VNET est lié au réseau local à l’aide de technologies hybrides Azure comme site à VPN ou acheminer Express.


## <a name="how-the-system-works"></a>Comment fonctionne le système

Coulisses Azure RemoteApp déploie Azure machines virtuelles (avec votre image téléchargée) à la réseau virtuel dont vous avez choisi pendant la mise en service. Si vous avez opté pour une collection de sites hybride, nous tenter de résoudre le nom de domaine complet du contrôleur de domaine que vous avez entré dans la mise à disponibilité du flux de travail avec le serveur DNS fourni dans le réseau virtuel.  
Si vous vous connectez à un réseau virtuel, veillez à exposer les ports nécessaires dans vos groupes de sécurité réseau dans votre sous-réseau RemoteApp Azure. 

Nous vous recommandons de qu'utiliser un [sous-réseau assez grande pour Azure RemoteApp](remoteapp-vnetsizing.md). Le plus grand pris en charge par réseau virtuel Azure est /8 (à l’aide des définitions de sous-réseau CIDR). Votre sous-réseau doit être assez grande pour s’adapter à tous les Azure RemoteApp ordinateurs virtuels pendant l’échelle lorsque plusieurs utilisateurs ont accès les applications. 

Voici les choses que vous devez activer votre sous-réseau virtuel : 

2.  Le trafic sortant à partir du sous-réseau convient dans l’étendue de port 10101 10175 pour communiquer avec l’un des services Azure RemoteApp internes.
3.  Le trafic sortant doit être autorisé à partir de votre sous-réseau à se connecter au stockage Azure sur le port 443
4.  Si vous avez Active Directory hébergé dans Azure, vérifiez que tout machine virtuelle au sein du sous-réseau réseau virtuel pour Azure RemoteApp est en mesure de vous connecter à ce contrôleur de domaine. Le DNS dans le réseau virtuel doit être en mesure de résoudre le nom de domaine complet de ce contrôleur de domaine.


## <a name="virtual-network-with-forced-tunneling"></a>Réseau virtuel avec tunnel forcé

[Forcé tunnel](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) est désormais pris en charge pour toutes les nouvelles collections RemoteApp Azure. Nous ne permettent pas la migration d’une collection existante pour prendre en charge tunnel forcé.  Vous devrez supprimer toutes les collections existantes à l’aide de la VNET que vous souhaitez lier à Azure RemoteApp et créer un autre pour obtenir forcé tunnel activé sur vos collections. 
