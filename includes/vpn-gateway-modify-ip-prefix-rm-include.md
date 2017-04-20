### <a name="noconnection"></a>Comment ajouter ou supprimer des préfixes - aucune connexion passerelle

- **Pour ajouter** les préfixes d’adresse supplémentaires à une variable locale réseau passerelle que vous avez créé, mais qui ne pas encore disposer d’une connexion de la passerelle, utilisez l’exemple ci-dessous. N’oubliez pas de modifier les valeurs à votre propre.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Pour supprimer** un préfixe d’adresse d’une passerelle réseau local qui n’est pas connecté VPN, utilisez l’exemple ci-dessous. Exclut les préfixes qui vous n’avez plus besoin. Dans cet exemple, nous devons n’est plus préfixe 20.0.0.0/24 (à partir de l’exemple précédent), afin que nous met à jour l’ordinateur local passerelle du réseau et exclure ce préfixe.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Comment ajouter ou supprimer des préfixes - passerelle connexion existante

Si vous avez créé votre connexion passerelle et que vous voulez ajouter ou supprimer les préfixes d’adresses IP contenues dans la passerelle de votre réseau local, vous devez effectuer les opérations suivantes dans l’ordre. Ainsi, une interruption de service pour votre connexion VPN. Lors de la mise à jour votre préfixes, vous devez tout d’abord supprimer la connexion, modifier les préfixes et puis créer une nouvelle connexion. Dans les exemples ci-dessous, veillez à modifier les valeurs à votre propre.

>[AZURE.IMPORTANT] Ne pas supprimer la passerelle VPN. Si vous procédez ainsi, vous devrez passer en revue les étapes pour recréer, ainsi que de reconfigurer votre routeur en local avec les nouveaux paramètres.
 
1. Supprimer la connexion.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modifier les préfixes d’adresse de la passerelle réseau local.

    Définir la variable pour le LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modifier les préfixes.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Créer la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion est spécifié pour votre configuration. Pour les types de connexion supplémentaires, reportez-vous à la page de [l’applet de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Définir la variable pour le VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Créer la connexion. Notez que cet exemple utilise la variable $local que vous avez défini à l’étape précédente.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
