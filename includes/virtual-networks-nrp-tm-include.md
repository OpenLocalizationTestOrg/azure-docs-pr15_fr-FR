## <a name="traffic-manager-profile"></a>Profil de trafic Manager

Le trafic responsable et sa ressource de point de terminaison enfant activer le routage DNS aux points de terminaison dans Azure ou en dehors d’Azure. Ce distribution le trafic est régie par les méthodes de routage de stratégie. Gestionnaire de trafic permet également d’intégrité de point de terminaison à surveiller et le trafic passez correctement basé sur l’état d’un point de terminaison. 

| Propriété | Description |
|---|---|
|**trafficRoutingMethod**| les valeurs possibles sont les *performances*, *Weighted*et *priorité* | 
| **dnsConfig** | Nom de domaine complet pour le profil | 
| **Protocole** | surveillance protocole, les valeurs possibles sont *HTTP* et *HTTPS*|
| **Port** | port de surveillance |  
| **Chemin d’accès** | chemin d’accès de surveillance |
| **Points de terminaison** |  conteneur pour les ressources de point de terminaison | 

### <a name="endpoint"></a>Point de terminaison 

Un point de terminaison est une ressource enfant d’un profil Manager le trafic. Il représente un service ou point de terminaison web à quel utilisateur le trafic est distribué basée sur la stratégie configurée dans la ressource le trafic Gestionnaire de profil. 

| Propriété | Description | 
|---|---| 
| **Type** |  le type du point de terminaison, les valeurs possibles sont *Azure fin pointez*, *Point de terminaison externe*et *Imbriquées le point de terminaison* | 
| **targetResourceId** |  adresse IP publique d’un point de terminaison de service ou site web. Cela peut être un point de terminaison Azure ou externe. | 
| **Poids** | poids de point de terminaison utilisée dans la gestion du trafic. | 
| **Priorité** | priorité du point de terminaison, utilisée pour définir une action basculement |

Exemple du Gestionnaire de trafic au format Json : 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, lisez [API REST documentation pour le Gestionnaire de trafic](https://msdn.microsoft.com/library/azure/mt163664.aspx) .
