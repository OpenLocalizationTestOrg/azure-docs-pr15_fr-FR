Pour modifier l’adresse IP de passerelle, utilisez la `New-AzureRmVirtualNetworkGatewayConnection` applet de commande. Dans la mesure où vous conservez le nom de la passerelle réseau local exactement comme le nom existant, les paramètres remplaceront. Pour l’instant, l’applet de commande « Définir » ne reconnaît pas la modification de l’adresse IP de passerelle.

### <a name="gwipnoconnection"></a>Comment modifier l’adresse IP de passerelle - aucune connexion passerelle

Pour mettre à jour l’adresse IP de passerelle pour votre passerelle réseau local qui n’est pas encore connecté, utilisez l’exemple ci-dessous. Vous pouvez également mettre à jour les préfixes d’adresse en même temps. Les paramètres que vous spécifiez remplace les paramètres existants. Veillez à utiliser le nom de la passerelle de votre réseau local existant. Si vous n’avez pas, vous allez créer une nouvelle passerelle réseau local, ne pas remplacer celui qui existe déjà.

Utilisez l’exemple suivant, remplacer les valeurs pour le vôtre.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Comment modifier l’adresse IP de passerelle - passerelle connexion existante

Si une passerelle connexion existe déjà, vous devez tout d’abord supprimer la connexion. Ensuite, vous pouvez modifier l’adresse IP de passerelle et recréer une nouvelle connexion. Ainsi, une interruption de service pour votre connexion VPN.


>[AZURE.IMPORTANT] Ne pas supprimer la passerelle VPN. Si vous procédez ainsi, vous devrez passer en revue les étapes pour recréer, ainsi que de reconfigurer votre routeur en local avec l’adresse IP qui doivent être affectée à la passerelle nouvellement créée.
 

1. Supprimer la connexion. Vous pouvez trouver le nom de votre connexion à l’aide de la `Get-AzureRmVirtualNetworkGatewayConnection` applet de commande.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modifiez la valeur GatewayIpAddress. Vous pouvez également modifier votre préfixes d’adresse pour l’instant, si nécessaire. Notez que cela remplacera les paramètres de la passerelle réseau local existant. Utilisez le nom de la passerelle de votre réseau local existant lors de la modification de sorte que les paramètres remplaceront. Si vous n’avez pas, vous allez créer une nouvelle passerelle réseau local, ne modifie ne pas un existant.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Créer la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion est spécifié pour votre configuration. Pour les types de connexion supplémentaires, reportez-vous à la page de [l’applet de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Pour obtenir le nom de VirtualNetworkGateway, vous pouvez exécuter le `Get-AzureRmVirtualNetworkGateway` applet de commande.

    Définissez les variables :

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Créer la connexion :
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

