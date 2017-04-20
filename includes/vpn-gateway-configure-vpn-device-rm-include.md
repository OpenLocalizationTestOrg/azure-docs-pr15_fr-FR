
Pour configurer votre appareil VPN, vous devez l’adresse IP de la passerelle réseau virtuel pour configurer votre appareil VPN en local. Utiliser des informations de configuration spécifiques le fabricant de votre appareil et configurer votre appareil. Consultez les [Périphériques VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) pour plus d’informations sur les appareils VPN fonctionnent correctement avec Azure.

Pour rechercher l’adresse IP de votre passerelle réseau virtuel à l’aide de PowerShell, utilisez l’exemple suivant :

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Vous pouvez également afficher l’adresse IP de la passerelle réseau virtuel à l’aide du portail Azure. Accédez à **passerelles réseau virtuelle**, puis cliquez sur le nom de votre passerelle.