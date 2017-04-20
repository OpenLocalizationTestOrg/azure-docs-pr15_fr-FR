<properties
   pageTitle="Créer VNet Peering à l’aide des applets de commande Powershell | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel à l’aide du portail Azure dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Créer VNet Peering à l’aide des applets de commande Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet peering à l’aide de PowerShell, suivez les étapes suivantes :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) et suivez les instructions tout à fait à la fin pour vous connecter à Azure et sélectionnez votre abonnement.

> [AZURE.NOTE] Applet de commande PowerShell pour la gestion des VNet peering est fourni avec [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Lire les objets de réseau virtuel :

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Pour établir VNet peering, vous devez créer deux liens, une pour chaque direction. L’étape suivante allez créer un lien d’homologation VNet pour VNet1 à VNet2 tout d’abord :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Sortie indique :

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Cette étape crée un lien d’homologation VNet pour VNet2 à VNet1 :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Sortie indique :

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Une fois que le lien d’homologation VNet est créé, vous pouvez consulter l’état de lien ci-dessous :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Sortie indique :

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Il existe quelques propriétés configurables pour VNet peering :

  	|Option|Description|Par défaut|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Si l’espace de VNet égal à inclure dans le cadre de la balise Virtual_network d’adressage|Oui|
  	|AllowForwardedTraffic|Si le trafic ne proviennent ne pas une VNet ressources est accepté ou rejeté|N°|
  	|AllowGatewayTransit|Permet à l’hôte VNet à utiliser votre passerelle VNet|N°|
  	|UseRemoteGateways|Utiliser la passerelle de VNet de votre collègue. L’homologue VNet doit comporter une passerelle est configurée et AllowGatewayTransit sélectionné. Vous ne pouvez pas utiliser cette option si vous avez une passerelle est configurée|N°|

    Chaque lien dans VNet peering comporte l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess à vrai pour le lien d’homologation VNet VNet1 VNet2 et définissez sur False pour le lien d’homologation VNet dans le sens inverse.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Vous pouvez exécuter Get-AzureRmVirtualNetworkPeering à vérifier à la valeur de propriété après la modification. À partir du résultat, vous pouvez voir Qu'allowforwardedtraffic modifie jeu True après avoir exécuté les applets de commande ci-dessus.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Une fois la peering établie dans ce scénario, vous devriez pouvoir initier les connexions à partir de n’importe quelle machine virtuelle à toute machine virtuelle de deux VNets. Par défaut, AllowVirtualNetworkAccess a la valeur vrai et VNet peering configurera les utilisateurs appropriés pour autoriser la communication entre VNets. Vous pouvez toujours appliquer des règles de groupe (NSG) de sécurité de réseau pour bloquer la connectivité entre sous-réseaux spécifiques ou machines virtuelles à prendre le contrôle de grains fins d’accès entre deux réseaux virtuels.  Pour plus d’informations sur la création de règles NSG, reportez-vous à cet [article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer VNet peering abonnements à l’aide de PowerShell, suivez les étapes suivantes :

1. Se connecter à Azure avec privilèges utilisateur A du compte pour l’abonnement A et exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Ce n’est pas obligatoire, peering peut être établie même si les utilisateurs déclenchent individuellement demandes homologation pour leur VNets correspondantes dans la mesure où les demandes correspond à. Ajout d’un utilisateur privilégié de l’autre VNet en tant qu’utilisateur dans le VNet local rend plus facile d’effectuer la configuration.

2. Se connecter Azure avec compte dotés de privilèges à l’utilisateur B pour abonnement-b et exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. Dans utilisateur A de session de connexion, exécutée l’applet de commande ci-dessous :

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Session de connexion de l’utilisateur-B, exécutez l’applet de commande ci-dessous :

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Une fois la peering établie, toutes les machines virtuelles dans VNet3 doit être en mesure de communiquer avec n’importe quel ordinateur virtuel dans VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez exécuter les applets de commande PowerShell suivante pour établir la VNet peering.  Vous devez définir la propriété AllowForwardedTraffic sur True et créer un lien VNET1 HubVNet, qui permet du trafic entrant à partir d’en dehors de l’espace d’adressage VNet homologation.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Une fois peering établie, vous pouvez faire référence à cet [article](virtual-network-create-udr-arm-ps.md) et définir un itinéraire (UDR) pour rediriger le trafic VNet1 via une application virtuelle à utiliser ses fonctions définies par l’utilisateur. Lorsque vous spécifiez l’adresse du saut suivant dans l’itinéraire, vous pouvez le configurer pour l’adresse IP de l’application dans l’homologue VNet HubVNet virtuelle. Voici un exemple :

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Pour créer un VNet peering entre un réseau virtuel classique et un réseau virtuel Azure le Gestionnaire de ressources dans PowerShell, procédez comme suit :

1. Objet réseau virtuel pour **VNET1**, le réseau virtuel Azure le Gestionnaire de ressources comme suit :

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Pour établir VNet peering dans ce scénario, seul lien est nécessaire, en particulier un lien entre **VNET1** et **VNET2**. Cette étape repose sur la connaissance des ID de ressource. de votre VNet classique Le format d’ID de groupe ressource ressemble à :

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Veillez à remplacer SubscriptionID, ResourceGroupName et VirtualNetworkName avec les noms appropriés.

    Il est possible par le texte suivant :

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Une fois VNet lien d’homologation est créé, vous pouvez afficher l’état des liaisons comme indiqué dans les résultats suivants :

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Supprimer VNet Peering

1.  Pour supprimer la VNet peering, vous devez exécuter l’applet de commande suivante :

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Une fois que vous supprimez un lien dans un VNET peering, l’état des liaisons homologue accède à déconnecté. Dans cet état, vous ne pouvez pas recréer le lien jusqu'à ce que l’état de lien homologue devient initié. Nous vous recommandons de que vous supprimez les deux liens avant de recréer le VNet peering.
