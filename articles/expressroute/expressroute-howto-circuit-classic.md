<properties
   pageTitle="Créer et modifier un circuit ExpressRoute à l’aide du modèle de déploiement classique et PowerShell | Microsoft Azure"
   description="Cet article décrit les étapes de création et de mise en service un circuit ExpressRoute. Cet article vous montre comment créer votre circuit et vérifier le statut, la mise à jour ou la supprimer."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Créer et modifier un circuit ExpressRoute

> [AZURE.SELECTOR]
[Portail Azure - Gestionnaire de ressources](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-circuit-arm.md)
[PowerShell - classique](expressroute-howto-circuit-classic.md)

Cet article vous explique comment procéder pour créer un circuit Azure ExpressRoute à l’aide des applets de commande PowerShell et le modèle de déploiement classique. Cet article vous montrent également comment créer un circuit ExpressRoute et vérifier le statut, la mise à jour ou la supprimer.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Avant de commencer

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. passer en revue les conditions préalables et les articles de flux de travail

Vérifiez que vous avez vérifié le [flux de travail](expressroute-workflows.md) et les [conditions préalables](expressroute-prerequisites.md) avant de commencer la configuration.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. installer les dernières versions des modules PowerShell Azure 

Suivez les instructions dans [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour des instructions détaillées sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell Azure.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. Connectez-vous à votre compte Azure et sélectionnez un abonnement

1. Exécutez l’applet de commande suivante à l’aide d’une invite de Windows PowerShell élevée :

        Add-AzureAccount
2. Dans l’écran de connexion qui s’affiche, connectez-vous à votre compte.

3. Obtenir une liste de vos abonnements.

        Get-AzureSubscription
4. Sélectionnez l’abonnement que vous voulez utiliser.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Créer et mettre en service un circuit ExpressRoute

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importer les modules PowerShell pour ExpressRoute

 Si vous ne le n'avez pas déjà fait, vous devez importer les modules Azure et ExpressRoute dans la session PowerShell afin de commencer à utiliser les applets de commande ExpressRoute. Vous importez les modules à l’emplacement où ils ont été installés sur votre ordinateur local. Selon la méthode utilisée pour installer les modules, l’emplacement peut être différent de celui de l’exemple suivant. Modifier l’exemple si nécessaire.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. obtenir la liste de fournisseurs pris en charge, les emplacements et bande passante

Avant de créer un circuit ExpressRoute, vous devez la liste de fournisseurs de connectivité pris en charge, les emplacements et les options de bande passante.

L’applet de commande PowerShell `Get-AzureDedicatedCircuitServiceProvider` renvoie ces informations, vous allez utiliser dans les étapes suivantes :

    Get-AzureDedicatedCircuitServiceProvider

Vérifiez si votre fournisseur de connectivité s’il figure. Notez les informations suivantes, car vous en aurez besoin ultérieurement lorsque vous créez un circuit :

- Nom

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.         

### <a name="3-create-an-expressroute-circuit"></a>3. créer un circuit ExpressRoute

L’exemple suivant montre comment créer un Mbps 200 circuit ExpressRoute via Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, remplacez ces informations lorsque vous effectuez votre requête.

>[AZURE.IMPORTANT] Votre circuit ExpressRoute est facturé depuis le moment où qu'une clé de service est émise. Assurez-vous que vous effectuez cette opération lorsque le fournisseur de connectivité est prêt à mettre en service le circuit.


Voici un exemple de demande pour une nouvelle clé de service :

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Ou, si vous souhaitez créer un circuit ExpressRoute avec le module complémentaire premium, utilisez l’exemple suivant. Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus d’informations sur le module complémentaire premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La réponse contiendra la clé du service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. répertorier tous les circuits ExpressRoute

Vous pouvez exécuter le `Get-AzureDedicatedCircuit` commande pour obtenir une liste de tous les circuits ExpressRoute que vous avez créé :


    Get-AzureDedicatedCircuit

La réponse sera doit ressembler à l’exemple suivant :

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez récupérer ces informations à tout moment en utilisant la `Get-AzureDedicatedCircuit` applet de commande. Effectuer l’appel sans paramètres répertorie tous les circuits. Votre clé de service apparaît dans le champ de *clé de service* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. envoyer la clé du service à votre fournisseur de connectivité de mise en service


*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de mise en service sur le côté de fournisseur de services. *État* indique l’état sur le côté de Microsoft. Pour plus d’informations sur circuit mise en service des États, voir l’article de [flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Lorsque vous créez un nouveau circuit ExpressRoute, le circuit sera dans l’état suivant :

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Le circuit est dirigés vers l’état suivant lorsque le fournisseur de connectivité est en train d’activation pour vous :

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuit ExpressRoute doit être placé dans l’état suivant pour pouvoir l’utiliser :

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. régulièrement vérifier l’état et l’état de la touche circuit

Cela vous permet de déterminer si votre fournisseur a activé votre circuit. Une fois le circuit a été configuré, *ServiceProviderProvisioningState* affichera comme *Provisioned* comme le montre l’exemple suivant :

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. Créez votre configuration de routage

Reportez-vous à la [ExpressRoute de configuration de routage circuit (créer et modifier des circuit peerings)](expressroute-howto-routing-classic.md) article pour obtenir des instructions étape par étape.

>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement à circuits créés avec les fournisseurs de services qui proposent des services de connectivité 2 calque. Si vous utilisez un fournisseur de services qui propose gérées layer 3 services (généralement un IP VPN, comme MPLS), votre fournisseur de connectivité configurer et gérer le routage pour vous.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. lier un réseau virtuel à un circuit ExpressRoute

Ensuite, lier un réseau virtuel à votre circuit ExpressRoute. Pour des instructions détaillées, voir [circuits ExpressRoute liaison à des réseaux virtuels](expressroute-howto-linkvnet-classic.md) . Si vous avez besoin créer un réseau virtuel en utilisant le modèle de déploiement classique pour ExpressRoute, voir [créer un réseau virtuel pour ExpressRoute](expressroute-howto-vnet-portal-classic.md) pour obtenir des instructions.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtention de l’état d’un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment en utilisant la `Get-AzureCircuit` applet de commande. Effectuer l’appel sans paramètres répertorie tous les circuits.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en passant la clé du service en tant que paramètre à l’appel :

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modifier un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d’un circuit ExpressRoute sans ayant un impact sur la connectivité.

Vous pouvez effectuer les opérations suivantes avec sans interruption de service :

- Activer ou désactiver un module complémentaire premium ExpressRoute pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la bande passante d’un circuit de mise à niveau n’est pas pris en charge.
- Modifier le plan de mesure à partir des données limitées aux données illimité. Notez que le plan de contrôle de la modification de données illimité aux données limitées n’est pas pris en charge.
- Vous pouvez activer et désactiver *Autoriser les opérations classique*.

Consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md) pour plus d’informations sur les limites et limitations.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le module complémentaire premium ExpressRoute

Vous pouvez activer le module complémentaire premium ExpressRoute pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Votre circuit aura alors les fonctionnalités de module complémentaire premium ExpressRoute activées. Notez que nous allons commencer vous facturation pour la fonctionnalité de composant additionnel premium dès que la commande a été exécutée.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le module complémentaire premium ExpressRoute

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources qui sont supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Vous devez vous assurer que le nombre de réseaux virtuels lié au circuit est inférieure à 10 avant de vous mettre à niveau à partir de premium en standard. Si vous ne faites ceci, votre demande de mise à jour échouera et vous serez facturé les taux de prime.

- Vous devez supprimer le lien tous les réseaux virtuels dans d’autres régions géopolitiques. Si vous ne faites ceci, votre demande de mise à jour échouera et vous serez facturé les taux de prime.

- Votre table itinéraire doit être inférieure à 4 000 itinéraires pour peering privé. Si votre taille de la table itinéraire est supérieure à 4 000 routes, la session BGP va supprimer et ne réactiver jusqu'à ce que le nombre de préfixes publiés devient inférieur à 4 000.

Vous pouvez désactiver le module complémentaire premium ExpressRoute pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Mettre à jour la bande passante circuit ExpressRoute

Consultez le [Forum aux questions ExpressRoute](expressroute-faqs.md) pour les options de bande passante pris en charge pour votre fournisseur. Vous pouvez choisir n’importe quelle taille est supérieure à la taille de votre circuit existant dans la mesure où le port physique (sur lequel votre circuit est créé) permet.

>[AZURE.IMPORTANT] Impossible de réduire la bande passante d’un circuit ExpressRoute sans interruption de service. Mise à niveau de bande passante vous demandera deprovision le circuit ExpressRoute et puis reprovisionner un nouveau circuit ExpressRoute.

Après avoir décidé taille que vous avez besoin, vous pouvez utiliser la commande suivante pour redimensionner votre circuit :

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Votre circuit sera ont été redimensionné vers le haut sur le côté de Microsoft. Vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations latéralement pour correspondre à cette modification. Notez que nous allons commencer facturation vous pour l’option de bande passante mis à jour à partir de ce point sur.

Si vous voyez le message d’erreur suivant lorsque vous augmentez la bande passante circuit, cela signifie il ne reste aucune bande passante suffisante sur le port physique où votre circuit existant est créée. Vous devez supprimer ce circuit et créer un nouveau circuit de la taille souhaitée. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation et suppression d’un circuit ExpressRoute

Notez les points suivants :

- Vous devez supprimer le lien tous les réseaux virtuels du circuit ExpressRoute pour cette opération aboutisse. Vérifiez si vous avez un réseau virtuel auquel est lié au circuit si cette opération échoue.

- Si l’état de mise en service de fournisseur de service de circuit ExpressRoute est **Provisioning** ou **Provisioned** vous devez travailler avec votre fournisseur de services à deprovision le circuit sur leur côté. Nous continuent à réserver des ressources et vous les facturer jusqu'à ce que le fournisseur de services valide le circuit d’annulation et nous avertit.

- Si le fournisseur de services a annulé le circuit (l’état de mise en service de fournisseur de service est défini sur **non mis en service**) vous pouvez ensuite supprimer le circuit. Cette commande arrête de facturation pour le circuit.

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, vérifiez que vous effectuez le des opérations suivantes :

- [Créer et modifier la gamme pour votre circuit ExpressRoute](expressroute-howto-routing-classic.md)
- [Créer un lien vers votre circuit ExpressRoute votre réseau virtuel](expressroute-howto-linkvnet-classic.md)
