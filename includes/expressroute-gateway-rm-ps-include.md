Les étapes de cette tâche utilisent un VNet basée sur les valeurs ci-dessous. Les noms et les paramètres supplémentaires sont également présentées dans cette liste. Nous n’utilisons cette liste directement dans une des étapes, bien que nous ajoutent variables basées sur les valeurs de cette liste. Vous pouvez copier la liste à utiliser comme référence, remplacer les valeurs par la vôtre.

Liste de référence de configuration :
    
- Nom du réseau virtuel = « TestVNet »
- Espace d’adressage réseau virtuel = 192.168.0.0/16
- Groupe de ressources = « TestRG »
- Nom Subnet1 = « FrontEnd » 
- Espace d’adressage Subnet1 = « 192.168.0.0/16 »
- Nom de la passerelle sous-réseau : « GatewaySubnet », vous devez toujours nommer un sous-réseau passerelle *GatewaySubnet*.
- Espace d’adressage passerelle sous-réseau = « 192.168.200.0/26 »
- Région = « US Extrême-Orient »
- Nom de la passerelle = « GW »
- Nom de la passerelle IP = « GWIP »
- Configuration IP passerelle nom = « gwipconf »
-  Type = « ExpressRoute » ce type est requis pour une configuration ExpressRoute.
- Nom IP Public passerelle = « gwpip »


## <a name="add-a-gateway"></a>Ajouter une passerelle

1. Se connecter à votre abonnement Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Déclarez vos variables pour cet exercice. Cet exemple utilise l’utiliser les variables dans l’exemple ci-dessous. Veillez à modifier ce pour refléter les paramètres que vous souhaitez utiliser. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Stocker l’objet réseau virtuel comme étant une variable.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Ajouter un sous-réseau passerelle à votre réseau virtuel. Le sous-réseau passerelle doit être nommé « GatewaySubnet ». Vous souhaiterez créer une passerelle qui est /27 ou supérieure (/ 26, / 25, etc..).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Définir la configuration.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Stocker le sous-réseau passerelle comme étant une variable.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Demander une adresse IP publique. L’adresse IP est requise avant de créer la passerelle. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Il est affectée dynamiquement. Vous allez utiliser cette adresse IP dans la section suivante de la configuration. La AllocationMethod doit être dynamique.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Créer la configuration de la passerelle. La configuration de passerelle définit le sous-réseau et l’adresse IP à utiliser. Dans cette étape, vous spécifiez la configuration qui sera utilisée lorsque vous créez la passerelle. Cette étape ne crée pas l’objet de la passerelle. Utilisez l’exemple ci-dessous pour créer votre configuration de la passerelle. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Créer la passerelle. Dans cette étape, la **-GatewayType** est particulièrement important. Vous devez utiliser la valeur **ExpressRoute**. Notez qu’après l’exécution de ces applets de commande, la passerelle peut prendre 20 minutes ou plus pour créer.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Vérifier que la passerelle a été créée

Utilisez la commande suivante pour vérifier que la passerelle a été créée.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Redimensionner une passerelle

Il existe un certain nombre de [Passerelle références SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la passerelle de référence (SKU) à tout moment.

>[AZURE.IMPORTANT] Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle vers une passerelle UltraPerformance, supprimez d’abord la passerelle ExpressRoute existante, puis créez une nouvelle passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, supprimez d’abord la passerelle UltraPerformance, puis créez une nouvelle passerelle.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Supprimer une passerelle

Utilisez la commande suivante pour supprimer une passerelle

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
