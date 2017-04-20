## <a name="virtual-network"></a>Réseau virtuel
Ressources réseaux (VNET) et des sous réseaux virtuelles permettent de définir une limite de sécurité pour les charges de travail en cours d’exécution dans Azure. Un VNet est caractérisée par un ensemble d’espaces d’adresse, définis sous forme de blocs CIDR. 

>[AZURE.NOTE] Les administrateurs réseau vous connaissez la notation CIDR. Si vous n’êtes pas familiarisé avec CIDR, [en savoir plus sur](http://whatismyipaddress.com/cidr).

![VNet avec plusieurs sous réseaux](./media/resource-groups-networking/Figure4.png)

VNets contenir les propriétés suivantes.

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**à EMC**|Collection de préfixes d’adresses qui constituent le VNet en notation CIDR|192.168.0.0/16|
|**sous-réseaux**|Collection des sous-réseaux qui composent le VNet|consultez les [sous-réseaux](#Subnets) ci-dessous.|
|**adresse IP**|Adresse IP affectée à un objet. Il s’agit d’une propriété en lecture seule.|104.42.233.77|

### <a name="subnets"></a>Sous-réseaux
Un constitue une ressource enfant d’un VNet et permettant de définir les segments d’espaces adresse au sein d’un bloc CIDR, à l’aide de préfixes d’adresses IP. Cartes réseau peut être ajoutées à sous-réseaux et connectés aux machines virtuelles, spéciale qui permet la connectivité des tâches variées.

Sous-réseaux contient les propriétés suivantes. 

|Propriété|Description|Exemples de valeurs|
|---|---|---|
|**addressPrefix**|Préfixe d’adresse unique qui constituent le sous-réseau en notation CIDR|192.168.1.0/24|
|**networkSecurityGroup**|NSG appliqué au sous-réseau|voir [NSGs](#Network-Security-Group)|
|**routeTable**|Table de routage appliqué au sous-réseau|voir [UDR](#Route-table)|
|**ipConfigurations**|Collection d’objets de configuration IP utilisées par carte réseau connectée au sous-réseau|voir [UDR](#Route-table)|


Exemple VNet au format JSON :

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Ressources supplémentaires

- Obtenir plus d’informations sur [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Consultez la [documentation de référence API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) pour VNets.
- Consultez la [documentation de référence API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) pour sous-réseaux.