<properties
   pageTitle="Créer et modifier un circuit ExpressRoute en utilisant le Gestionnaire de ressources et PowerShell | Microsoft Azure"
   description="Cet article décrit comment créer, mettre en service, vérifiez, mettre à jour, supprimer et deprovision un circuit ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Créer et modifier un circuit ExpressRoute

> [AZURE.SELECTOR]
[Portail Azure - Gestionnaire de ressources](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-circuit-arm.md)
[PowerShell - classique](expressroute-howto-circuit-classic.md)


Cet article décrit comment créer un circuit Azure ExpressRoute à l’aide des applets de commande Windows PowerShell et le modèle de déploiement d’Azure le Gestionnaire de ressources. Cet article vous montrent également comment vérifier l’état du circuit, mettre à jour, ou supprimer et il deprovision.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Avant de commencer


- Obtenir la dernière version des modules PowerShell Azure (au moins version 1.0). Pour des instructions détaillées sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell, suivez les instructions de la rubrique [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

- Passez en revue les [conditions requises](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

## <a name="create-and-provision-an-expressroute-circuit"></a>Créer et mettre en service un circuit ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Connectez-vous à votre compte Azure et sélectionnez votre abonnement

Pour commencer votre configuration, connectez-vous à votre compte Azure. Pour plus d’informations sur PowerShell, voir [Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md). Utilisez les exemples suivants pour vous aider à vous connecter :

    Login-AzureRmAccount

Vérifier les abonnements pour le compte :

    Get-AzureRmSubscription

Sélectionnez l’abonnement auquel vous souhaitez créer un circuit ExpressRoute pour :

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtenir la liste de fournisseurs pris en charge, les emplacements et bande passante

Avant de créer un circuit ExpressRoute, vous devez la liste de fournisseurs de connectivité pris en charge, les emplacements et les options de bande passante.

L’applet de commande PowerShell `Get-AzureRmExpressRouteServiceProvider` renvoie ces informations, vous allez utiliser dans les étapes suivantes :

    Get-AzureRmExpressRouteServiceProvider

Vérifiez si votre fournisseur de connectivité s’il figure. Notez les informations suivantes, car vous en aurez besoin ultérieurement lorsque vous créez un circuit :

- Nom

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. créer un circuit ExpressRoute

Si vous n’avez pas un groupe de ressources, vous devez créer une avant de créer votre circuit ExpressRoute. Vous pouvez le faire en exécutant la commande suivante :


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


L’exemple suivant montre comment créer un Mbps 200 circuit ExpressRoute via Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, remplacez ces informations lorsque vous effectuez votre requête. Voici un exemple de demande pour une nouvelle clé de service :

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Assurez-vous que vous spécifiez la couche de référence (SKU) correct et famille de référence (SKU) :

- Niveau de référence (SKU) détermine si une norme ExpressRoute ou un module complémentaire premium ExpressRoute est activé. Vous pouvez spécifier *Standard* pour obtenir la référence (SKU) ou *Premium* standard pour le composant additionnel premium.

- Famille de référence (SKU) détermine le type de facturation. Vous pouvez spécifier *Metereddata* pour un plan de données limitée et *Unlimiteddata* pour un plan de données illimité. Notez que vous pouvez modifier le type de facturation de *Metereddata* à *Unlimiteddata*, mais vous ne pouvez pas modifier le type de *Unlimiteddata* à *Metereddata*.


>[AZURE.IMPORTANT] Votre circuit ExpressRoute est facturé depuis le moment où qu'une clé de service est émise. Assurez-vous que vous effectuez cette opération lorsque le fournisseur de connectivité est prêt à mettre en service le circuit.

La réponse contient la clé du service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. répertorier tous les circuits ExpressRoute

Pour obtenir une liste de tous les circuits ExpressRoute que vous avez créé, exécutez la `Get-AzureRmExpressRouteCircuit` commande :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

La réponse est semblable à l’exemple suivant :


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Vous pouvez récupérer ces informations à tout moment en utilisant la `Get-AzureRmExpressRouteCircuit` applet de commande. Effectuer l’appel sans paramètres répertorie tous les circuits. Votre clé de service est répertorié dans le champ de *clé de service* :


    Get-AzureRmExpressRouteCircuit


La réponse est semblable à l’exemple suivant :


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. envoyer la clé du service à votre fournisseur de connectivité de mise en service

*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de mise en service sur le côté de fournisseur de services. État indique l’état sur le côté de Microsoft. Pour plus d’informations sur circuit mise en service des États, voir l’article de [flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Lorsque vous créez un nouveau circuit ExpressRoute, le circuit sera dans l’état suivant :


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Le circuit changent à l’état suivant lorsque le fournisseur de connectivité est en train d’activation pour vous :

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Pour pouvoir être en mesure d’utiliser un circuit ExpressRoute, il doit être placé dans l’état suivant :

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. régulièrement vérifier l’état et l’état de la touche circuit

Vérification de l’état et l’état de la touche circuit vous permet de déterminer si votre fournisseur a activé votre circuit. Après le circuit a été configuré, *ServiceProviderProvisioningState* apparaît sous la forme *Provisioned*, comme illustré dans l’exemple suivant :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse est semblable à l’exemple suivant :


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                    }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Créez votre configuration de routage

Pour obtenir des instructions détaillées, voir l’article [configuration du routage circuit ExpressRoute](expressroute-howto-routing-arm.md) pour créer et modifier des circuit peerings.


>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement à circuits créés avec les fournisseurs de services qui proposent des services de connectivité 2 calque. Si vous utilisez un fournisseur de services qui propose gérées layer 3 services (généralement un IP VPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. lier un réseau virtuel à un circuit ExpressRoute

Ensuite, lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article [réseaux virtuels Linking aux circuits ExpressRoute](expressroute-howto-linkvnet-arm.md) lorsque vous travaillez avec le modèle de déploiement du Gestionnaire de ressources.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtention de l’état d’un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment en utilisant la `Get-AzureRmExpressRouteCircuit` applet de commande. Effectuer l’appel sans paramètres répertorie tous les circuits.

    Get-AzureRmExpressRouteCircuit


La réponse sera semblable à l’exemple suivant :


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en passant le nom de groupe de ressources et circuit comme un paramètre à l’appel :


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse est semblable à l’exemple suivant :


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modifier un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d’un circuit ExpressRoute sans ayant un impact sur la connectivité.

Vous pouvez effectuer les opérations suivantes avec sans interruption de service :

- Activer ou désactiver un module complémentaire premium ExpressRoute pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la bande passante d’un circuit de mise à niveau n’est pas pris en charge.
- Modifier le plan de mesure à partir des données limitées aux données illimité. Notez que le plan de contrôle de la modification de données illimité aux données limitées n’est pas pris en charge.
-  Vous pouvez activer et désactiver *Autoriser les opérations classique*.

Pour plus d’informations sur les limites et limitations, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le module complémentaire premium ExpressRoute

Vous pouvez activer le module complémentaire premium ExpressRoute pour votre circuit existant à l’aide de l’extrait de PowerShell suivant :

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Le circuit aura alors les fonctionnalités de module complémentaire premium ExpressRoute activées. Notez que nous commencera vous facturation pour la fonctionnalité de composant additionnel premium dès que la commande a été exécutée.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le module complémentaire premium ExpressRoute

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources qui sont supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Avant de vous mettre à niveau à partir de premium en standard, vous devez vous assurer que le nombre de réseaux virtuels auquel sont liés au circuit est inférieure à 10. Si vous ne faites ceci, votre demande de mise à jour échoue, et vous êtes facturé au taux de prime.

- Vous devez supprimer le lien tous les réseaux virtuels dans d’autres régions géopolitiques. Si vous ne faites ceci, votre demande de mise à jour échouera, et vous êtes facturé au taux de prime.

- Votre table itinéraire doit être inférieure à 4 000 itinéraires pour peering privé. Si votre taille de la table itinéraire est supérieure à 4 000 routes, la session BGP place et ne réactiver jusqu'à ce que le nombre de préfixes publiés devient inférieur à 4 000.

Vous pouvez désactiver le module complémentaire premium ExpressRoute pour le circuit existant à l’aide de l’applet de commande PowerShell suivante :


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Mettre à jour la bande passante circuit ExpressRoute

Pour les options de bande passante pris en charge pour votre fournisseur, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n’importe quelle taille supérieure à la taille de votre circuit existant.

>[AZURE.IMPORTANT] Impossible de réduire la bande passante d’un circuit ExpressRoute sans interruption de service. Mise à niveau de bande passante, vous devez deprovision le circuit ExpressRoute et puis reprovisionner un nouveau circuit ExpressRoute.

Après avoir décidé taille que vous avez besoin, utilisez la commande suivante pour redimensionner votre circuit :


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


A la taille votre circuit vers le haut sur le côté de Microsoft. Ensuite, vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations latéralement pour correspondre à cette modification. Après avoir effectué cette notification, nous commencera vous facturation pour l’option de mise à jour de la bande passante.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Pour déplacer la référence (SKU) de limitées illimité

Vous pouvez modifier la référence (SKU) d’un circuit ExpressRoute à l’aide de l’extrait de PowerShell suivant :

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Pour contrôler l’accès aux environnements de gestionnaire de ressources et la classique  

Passez en revue les instructions de [circuits ExpressRoute déplacer de la classique au modèle de déploiement de gestionnaire de ressources](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation et suppression d’un circuit ExpressRoute

Notez les points suivants :

- Vous devez supprimer le lien tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si les réseaux virtuels sont liés au circuit.

- Si l’état de mise en service de fournisseur de service de circuit ExpressRoute est **Provisioning** ou **Provisioned** vous devez travailler avec votre fournisseur de services à deprovision le circuit sur leur côté. Nous continuent à réserver des ressources et vous les facturer jusqu'à ce que le fournisseur de services valide le circuit d’annulation et nous avertit.

- Si le fournisseur de services a annulé le circuit (l’état de mise en service de fournisseur de service est défini sur **non mis en service**) vous pouvez ensuite supprimer le circuit. Cette commande arrête de facturation pour le circuit

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, vérifiez que vous effectuez le des opérations suivantes :

- [Créer et modifier la gamme pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Créer un lien vers votre circuit ExpressRoute votre réseau virtuel](expressroute-howto-linkvnet-arm.md)
