<properties 
   pageTitle="Gérer les NSGs à l’aide de PowerShell dans le Gestionnaire de ressources | Microsoft Azure"
   description="Découvrez comment gérer existant NSGs à l’aide de PowerShell dans le Gestionnaire de ressources"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Gérer les NSGs à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Récupérer des informations

Vous pouvez afficher votre NSGs existants, récupérer des règles pour un NSG existante et déterminer quelles ressources un NSG est associé à.

### <a name="view-existing-nsgs"></a>Affichage existants NSGs
Pour afficher toutes les NSGs existants dans un abonnement, exécutez la `Get-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

    Get-AzureRmNetworkSecurityGroup

Résultat attendu :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Pour afficher la liste des NSGs dans un groupe de ressources spécifique, exécutez la `Get-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Résultat attendu :

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Liste de toutes les règles pour un NSG

Pour afficher les règles d’un NSG nommés **NSG FrontEnd**, exécutez la `Get-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Résultat attendu :
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] Vous pouvez également utiliser `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` pour répertorier les règles par défaut de **NSG FrontEnd** NSG.

### <a name="view-nsgs-associations"></a>Afficher les associations NSGs

Pour afficher ce que ressources est la NSG **NSG FrontEnd** associer à, exécutez la `Get-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Recherchez les propriétés **NetworkInterfaces** et des **sous réseaux** comme indiqué ci-dessous :

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

Dans l’exemple ci-dessus, le NSG n’est pas associé à des interfaces réseau (cartes réseau), et il est associé à un sous-réseau nommé **FrontEnd**.

## <a name="manage-rules"></a>Gérer les règles

Vous pouvez ajouter des règles à un NSG existant, modifier les règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** pour le port **443** à partir de n’importe quel ordinateur à **NSG FrontEnd** NSG, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Add-AzureRmNetworkSecurityRuleConfig` applet de commande, comme illustré ci-dessous.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Pour enregistrer les modifications apportées à la NSG, exécutez la `Set-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Résultat attendu affichant uniquement les règles de sécurité :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Modifier une règle

Pour modifier la règle créée ci-dessus pour autoriser le trafic entrant à partir d' **Internet** uniquement, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Set-AzureRmNetworkSecurityRuleConfig` applet de commande, comme illustré ci-dessous.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Pour enregistrer les modifications apportées à la NSG, exécutez la `Set-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Résultat attendu affichant uniquement les règles de sécurité :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Supprimer une règle

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Remove-AzureRmNetworkSecurityRuleConfig` applet de commande, comme illustré ci-dessous.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Pour enregistrer les modifications apportées à la NSG, exécutez la `Set-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Sortie affichant uniquement les règles de sécurité, notez la **règle de https** n’apparaît plus attendue :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gérer les associations

Vous pouvez associer un NSG à sous-réseaux et cartes réseau. Vous pouvez également dissocier un NSG à partir de n’importe quelle ressource qu'il est associée.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un NSG à une carte réseau

Pour associer **NSG FrontEnd** NSG à la **TestNICWeb1** carte réseau, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Get-AzureRmNetworkInterface` applet de commande pour récupérer la carte réseau existante et stockez-le dans une variable, comme illustré ci-dessous.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Définissez la propriété **NetworkSecurityGroup** de la variable de **carte réseau** à la valeur de la variable **NSG** , comme illustré ci-dessous.

        $nic.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez la `Set-AzureRmNetworkInterface` applet de commande comme illustré ci-dessous.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Résultat attendu ne montrant que la propriété **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier une NSG à partir d’une carte réseau

Pour dissocier **NSG FrontEnd** NSG à partir de la carte réseau **TestNICWeb1** , suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Exécuter le `Get-AzureRmNetworkInterface` applet de commande pour récupérer la carte réseau existante et stockez-le dans une variable, comme illustré ci-dessous.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Définissez la propriété **NetworkSecurityGroup** de la variable de **carte réseau** sur **$null**, comme illustré ci-dessous.

        $nic.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez la `Set-AzureRmNetworkInterface` applet de commande comme illustré ci-dessous.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Résultat attendu ne montrant que la propriété **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier une NSG à partir d’un sous-réseau

Pour dissocier **NSG FrontEnd** NSG à partir du sous-réseau **FrontEnd** , suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmVirtualNetwork` applet de commande pour récupérer le VNet existant et stockez-le dans une variable, comme illustré ci-dessous.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Exécuter le `Get-AzureRmVirtualNetworkSubnetConfig` applet de commande pour récupérer le sous-réseau **FrontEnd** et stockez-le dans une variable, comme illustré ci-dessous.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Définissez la propriété **NetworkSecurityGroup** de la variable **sous-réseau** sur **$null**, comme illustré ci-dessous.

        $subnet.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez la `Set-AzureRmVirtualNetwork` applet de commande comme illustré ci-dessous.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Résultat attendu affichant uniquement les propriétés du sous-réseau **FrontEnd** . Notez qu’il n’est pas une propriété pour **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Associer un NSG à un sous-réseau

Pour associer **NSG FrontEnd** NSG au sous-réseau **FronEnd** à nouveau, suivez les étapes ci-dessous.

1. Exécuter le `Get-AzureRmVirtualNetwork` applet de commande pour récupérer le VNet existant et stockez-le dans une variable, comme illustré ci-dessous.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Exécuter le `Get-AzureRmVirtualNetworkSubnetConfig` applet de commande pour récupérer le sous-réseau **FrontEnd** et stockez-le dans une variable, comme illustré ci-dessous.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Exécuter le `Get-AzureRmNetworkSecurityGroup` applet de commande pour récupérer la NSG existant et stockez-le dans une variable, comme illustré ci-dessous.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Définissez la propriété **NetworkSecurityGroup** de la variable **sous-réseau** sur **$null**, comme illustré ci-dessous.

        $subnet.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez la `Set-AzureRmVirtualNetwork` applet de commande comme illustré ci-dessous.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Sortie affichant uniquement la propriété **NetworkSecurityGroup** du sous-réseau **FrontEnd** attendue :

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Supprimer un NSG

Vous pouvez uniquement supprimer une NSG si elle n’a pas associé à une ressource. Pour supprimer un NSG, suivez les étapes ci-dessous.

1. Pour vérifier les ressources associées à un NSG, exécutez la `azure network nsg show` comme indiqué dans les [associations d’affichage NSGs](#View-NSGs-associations).
2. Si la NSG est associée aux cartes réseau, exécutez la `azure network nic set` comme indiqué dans [Dissociate un NSG à partir d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si la NSG est associée à n’importe quel sous-réseau, exécutez la `azure network vnet subnet set` comme indiqué dans [Dissociate un NSG à partir d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) de chaque sous réseau.
4. Pour supprimer la NSG, exécutez la `Remove-AzureRmNetworkSecurityGroup` applet de commande comme illustré ci-dessous.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] La **-Force** paramètre veille à ce que vous n’avez pas besoin de confirmer la suppression.

## <a name="next-steps"></a>Étapes suivantes

- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.