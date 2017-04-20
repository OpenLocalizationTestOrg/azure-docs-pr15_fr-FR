<properties
   pageTitle="Créer un Internet facing équilibrage de charge en mode classique à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer un Internet facing équilibrage de charge en mode classique à l’aide de PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Créer un Internet facing équilibrage de charge (classique) dans PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [découvrir comment créer un Internet facing équilibrage de charge à l’aide du Gestionnaire de ressources Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configurer l’équilibrage de charge à l’aide de PowerShell

Pour configurer un programme d’équilibrage de charge à l’aide de powershell, procédez comme suit :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../../articles/powershell-install-configure.md) et suivez les instructions tout à fait à la fin pour vous connecter à Azure et sélectionnez votre abonnement.


2. Après avoir créé une machine virtuelle, vous pouvez utiliser les applets de commande PowerShell pour ajouter un équilibrage de charge à une machine virtuelle au sein du même service cloud.

Dans l’exemple suivant, vous allez ajouter qu'un équilibrage de charge définie appelée « webfarm » vers le cloud service « mytestcloud » (ou myctestcloud.cloudapp.net), ajouter les points de terminaison pour l’équilibrage de charge des machines virtuelles nommé « web1 » et « web2 ». L’équilibrage de charge reçoit le trafic réseau sur le port 80 et à l’équilibrage entre les machines virtuelles définies par le point de terminaison local (dans ce cas le port 80) à l’aide de TCP.


### <a name="step-1"></a>Étape 1
Créer un point de terminaison équilibrée de charge pour la première machine virtuelle « web1 »

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Étape 2

Créer un autre point de terminaison de la deuxième virtuelle « web2 » utilisant le même nom de jeu équilibrage de charge

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Supprimer une machine virtuelle à partir d’un programme d’équilibrage de charge

Vous pouvez utiliser AzureEndpoint de supprimer pour supprimer un point de terminaison machine virtuelle à partir de l’équilibrage de charge

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [commencer à créer un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-classic-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement le trafic réseau d’équilibrage de charge especific.

Si votre application doit afin de maintenir des connexions pour les serveurs derrière un programme d’équilibrage de charge, vous pouvez savoir plus sur [les paramètres de délai TCP inactives pour un équilibrage de charge](load-balancer-tcp-idle-timeout.md). Il aidera pour en savoir plus sur le comportement des connexions inactives lorsque vous utilisez l’équilibrage de charge Azure.

