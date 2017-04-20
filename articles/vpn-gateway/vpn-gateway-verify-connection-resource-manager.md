<properties
   pageTitle="Vérifier une connexion de passerelle | Microsoft Azure"
   description="Cet article vous explique comment faire pour vérifier une connexion de la passerelle dans le modèle de déploiement du Gestionnaire de ressources"
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
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Vérifier une connexion de passerelle

Vous pouvez vérifier votre connexion de passerelle de différentes façons. Cet article vous explique comment vérifier l’état d’une connexion de passerelle Gestionnaire de ressources à l’aide du portail Azure et à l’aide de PowerShell.


## <a name="verify-using-powershell"></a>Vérifier l’utilisation de PowerShell

Vous devez installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation des applets de commande Gestionnaire de ressources, voir [Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Étape 1 : Se connecter à votre compte Azure

1. Ouvrez votre console PowerShell avec des privilèges élevés et connectez-vous à votre compte.

        Login-AzureRmAccount

2. Vérifier les abonnements pour le compte.

        Get-AzureRmSubscription 

3. Spécifier l’abonnement que vous voulez utiliser.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Étape 2 : Vérifiez votre connexion


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Vérifiez à l’aide du portail Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

