<properties
   pageTitle="Créer VNet Peering à l’aide du Gestionnaire de ressources modèles | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel peering l’utilisation des modèles dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
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
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Créer VNet Peering à l’aide de modèles de gestionnaire de ressources

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet peering en utilisant le Gestionnaire de ressources des modèles, suivez les étapes suivantes :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) et suivez les instructions tout à fait à la fin pour vous connecter à Azure et sélectionnez votre abonnement.

    > [AZURE.NOTE] L’applet de commande PowerShell pour la gestion des VNet peering est fourni avec [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Le texte ci-dessous montre la définition d’un lien d’homologation VNet pour VNet1 à VNet2, en fonction du scénario ci-dessus. Copiez le contenu ci-dessous et enregistrez-le dans un fichier nommé VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. La section ci-dessous montre la définition d’un lien d’homologation VNet pour VNet2 à VNet1, en fonction du scénario ci-dessus.  Copiez le contenu ci-dessous et enregistrez-le dans un fichier nommé VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Comme indiqué dans le modèle ci-dessus, il existe quelques propriétés configurables pour VNet peering :

  	|Option|Description|Par défaut|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Ou non l’espace d’adressage d’un homologue VNet est inclus dans le cadre de l’indicateur virtual_network.|Oui|
  	|AllowForwardedTraffic|Si le trafic ne proviennent ne pas une VNet ressources est accepté ou rejeté.|N°|
  	|AllowGatewayTransit|Permet à l’hôte VNet à utiliser votre passerelle VNet.|N°|
  	|UseRemoteGateways|Utiliser la passerelle de VNet de votre collègue. L’homologue VNet doit comporter une passerelle est configurée et AllowGatewayTransit sélectionné. Vous ne pouvez pas utiliser cette option si vous avez une configuration de passerelle.|N°|

    Chaque lien dans VNet peering comporte l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess à vrai pour le lien d’homologation VNet VNet1 VNet2 et définissez sur False pour le lien d’homologation VNet dans le sens inverse.


4. Pour déployer le fichier de modèle, vous pouvez exécuter l’applet de commande New-AzureRmResourceGroupDeployment pour créer ou mettre à jour le déploiement. Pour plus d’informations sur l’utilisation des modèles de gestionnaire de ressources, reportez-vous à cet [article](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Remplacez le groupe modèle et un nom de fichier de ressources selon le cas.

    Sous exemple est basé sur le scénario ci-dessus :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Sortie indique :

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Sortie indique :

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Lorsque le déploiement est terminé, vous pouvez exécuter l’applet de commande ci-dessous pour afficher l’état homologation :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Sortie indique :

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Une fois la peering établie dans ce scénario, vous devriez pouvoir initier les connexions à partir de n’importe quelle machine virtuelle à toute machine virtuelle dans les deux VNets. Par défaut, AllowVirtualNetworkAccess a la valeur vrai et VNet peering configurera les utilisateurs appropriés pour autoriser la communication entre VNets. Vous pouvez toujours appliquer des règles de groupe (NSG) de sécurité de réseau pour bloquer la connectivité entre sous-réseaux spécifiques ou machines virtuelles à prendre le contrôle précis d’accès entre deux réseaux virtuels.  Pour plus d’informations de création de règles NSG, reportez-vous à cet [article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer un VNet peering abonnements, suivez les étapes suivantes :

1. Se connecter à Azure avec privilèges utilisateur A de compte dans l’abonnement A et exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Ce n’est pas obligatoire, peering peut être établie même si les utilisateurs déclenchent individuellement demandes homologation pour leur Vnets correspondantes dans la mesure où les demandes correspond à. Ajout d’un utilisateur privilégié de l’autre VNet en tant qu’utilisateurs dans le VNet local rend plus facile d’effectuer la configuration.

2. Se connecter Azure avec compte dotés de privilèges à l’utilisateur B pour abonnement-b et exécutez l’applet de commande suivante :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Dans utilisateur A de session de connexion, exécutée la cmdlet suivante :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Voici comment le fichier JSON est défini.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. Session de connexion de l’utilisateur-B, exécutez l’applet de commande suivante :

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Voici comment le fichier JSON est défini :

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Une fois la peering établie dans ce scénario, vous devriez pouvoir initier les connexions à partir de n’importe quelle machine virtuelle à toute machine virtuelle de deux VNets différents abonnements.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez déployer le modèle d’exemple ci-dessous pour établir la VNet peering.  Vous devez définir la propriété AllowForwardedTraffic true, qui permet à l’application virtuelle réseau dans le ressources VNet pour envoyer et recevoir le trafic.

    Voici le modèle pour la création d’un VNet peering à partir de HubVNet à VNet1. Notez que AllowForwardedTraffic est définie sur false.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Voici le modèle pour la création d’un VNet peering à partir de VNet1 à HubVnet. Notez que AllowForwardedTraffic est défini sur true.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Une fois la peering établie, vous pouvez faire référence à cet [article](virtual-network-create-udr-arm-ps.md) pour définir routes(UDR) définies par l’utilisateur afin de rediriger le trafic VNet1 via une application virtuelle à utiliser ses fonctions. Lorsque vous spécifiez l’adresse du saut suivant dans l’itinéraire, vous pouvez le configurer pour l’adresse IP de l’application dans l’homologue VNet HubVNet virtuelle.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Pour créer un peering entre les réseaux virtuels à partir de modèles de déploiement différentes, procédez comme suit :
1. Le texte ci-dessous montre la définition d’un lien d’homologation VNet pour VNET1 à VNET2 dans ce scénario. Seul lien est requis pour un réseau virtuel classique à un réseau virtuel Gestionnaire de ressources Azure d’homologues.

    Veillez à placer dans votre ID d’abonnement pour où se trouve le réseau virtuel classique ou VNET2 et définissez MyResouceGroup sur le nom du groupe ressources approprié.

    {« $schema » : « https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json# », « contentVersion » : « 1.0.0.0 », « paramètres » : {}, « variables » : {}, « ressources » : [{« apiVersion » : « 2016-06-01 », « type » : « Microsoft.Network/virtualNetworks/virtualNetworkPeerings », « nom » : « VNET1/LinkToVNET2 », « emplacement » : « [resourceGroup () .location] », « propriétés » : {« allowVirtualNetworkAccess » : true, « allowForwardedTraffic » : false, « allowGatewayTransit » : false, « useRemoteGateways » : false, « remoteVirtualNetwork » : {« id » : « [resourceId (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')] »}}}]}

2. Pour déployer le fichier de modèle, exécutez l’applet de commande suivante pour créer ou mettre à jour le déploiement.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Une fois le déploiement établie, vous pouvez exécuter l’applet de commande suivante pour afficher l’état homologation :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Une fois la peering établie entre un VNet classique et un gestionnaire de ressources VNet, vous devriez pouvoir établir des connexions à partir d’un ordinateur virtuel de VNET1 à toute machine virtuelle dans VNET2 et vice versa.
