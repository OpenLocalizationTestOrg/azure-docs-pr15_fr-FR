<properties
   pageTitle="Modifier les préfixes d’adresses IP passerelle réseau local et IP de la passerelle | Microsoft Azure"
   description="Cet article vous guide dans modifier les préfixes d’adresse IP de la passerelle réseau local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modifier les paramètres de la passerelle réseau local à l’aide de PowerShell

Parfois, les paramètres de la passerelle réseau local AddressPrefix ou GatewayIPAddress changent. Les instructions ci-dessous vous aideront à modifier vos paramètres de la passerelle réseau local. Vous pouvez également modifier ces paramètres dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
    
Vous devez installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.

## <a name="to-modify-ip-address-prefixes"></a>Pour modifier les préfixes d’adresses IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Pour modifier l’adresse IP de passerelle

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion passerelle. Voir [vérifier une connexion de passerelle](vpn-gateway-verify-connection-resource-manager.md).

