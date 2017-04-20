<properties
   pageTitle="Déplacer des circuits ExpressRoute de classique au Gestionnaire de ressources | Microsoft Azure"
   description="Cette page décrit comment déplacer un circuit classique vers le modèle de déploiement du Gestionnaire de ressources."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Atteindre le modèle de déploiement du Gestionnaire de ressources de circuits ExpressRoute de la classique

## <a name="configuration-prerequisites"></a>Conditions préalables de configuration

- Vous avez besoin de la dernière version des modules PowerShell Azure (au moins version 1.0).
- Vérifiez que vous avez examiné les [conditions préalables](expressroute-prerequisites.md), [Configuration requise pour le routage](expressroute-routing.md)et [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Avant avant de poursuivre, passez en revue les informations qui sont fournies sous [déplacer un circuit ExpressRoute de classique au Gestionnaire de ressources](expressroute-move.md). Vérifiez que vous avez parfaitement compris les limites et les limites de ce qui est possible.
- Si vous voulez déplacer un circuit Azure ExpressRoute à partir du modèle de déploiement classique vers le modèle de déploiement Azure le Gestionnaire de ressources, vous devez le circuit correctement configuré et opérationnel dans le modèle de déploiement classique.
- Vérifiez que vous disposez d’un groupe de ressources qui a été créé dans le modèle de déploiement du Gestionnaire de ressources.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Atteindre le circuit ExpressRoute le modèle de déploiement du Gestionnaire de ressources

Vous devez déplacer un circuit ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources afin que vous pouvez l’utiliser dans plusieurs standard et les modèles de déploiement Gestionnaire de ressources. Vous pouvez le faire en exécutant les commandes PowerShell suivantes.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Étape 1 : Collecter les détails de circuit sur le modèle de déploiement classique

Vous devez rassembler des informations concernant votre circuit ExpressRoute tout d’abord.

Se connecter à l’environnement classique Azure et rassemblez la clé du service. Vous pouvez utiliser l’extrait de PowerShell suivant pour collecter les informations :

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copiez la **clé du service** du circuit que vous souhaitez déplacer vers le modèle de déploiement du Gestionnaire de ressources.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Étape 2 : Se connecter à l’environnement du Gestionnaire de ressources et créer un nouveau groupe de ressources

Vous pouvez créer un nouveau groupe de ressources à l’aide de l’extrait de code suivante :

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Vous pouvez également utiliser un groupe de ressources existant si vous disposez déjà d’une.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Étape 3 : Déplacer le circuit ExpressRoute vers le modèle de déploiement Gestionnaire de ressources

Vous êtes maintenant prêt à déplacer sur votre circuit ExpressRoute de standard pour le modèle de déploiement du Gestionnaire de ressources. Passez en revue les informations fournies sous [déplacer un circuit ExpressRoute à partir de la classique au modèle de déploiement de gestionnaire de ressources](expressroute-move.md) avant de continuer.

Vous pouvez effectuer ceci en exécutant l’extrait de code suivante :

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Une fois le déplacement terminé, le nouveau nom qui figure dans l’applet de commande précédent servira à la ressource d’adresses. Le circuit est essentiellement renommé.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Activer un circuit ExpressRoute pour les deux modèles de déploiement

Vous devez déplacer votre circuit ExpressRoute pour le modèle de déploiement du Gestionnaire de ressources avant de contrôler l’accès au modèle de déploiement.

Exécutez l’applet de commande suivante pour activer l’accès aux deux modèles de déploiement :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous serez en mesure d’afficher le circuit dans le modèle de déploiement classique.

Exécutez la commande suivante pour obtenir les détails du circuit ExpressRoute :

    get-azurededicatedcircuit

Vous devez être en mesure de voir la clé du service répertoriée. Vous pouvez maintenant gérer les liens vers le circuit ExpressRoute à l’aide de vos commandes de modèle de déploiement classique standard pour VNets classique et vos commandes processeur standards pour processeur VNETs. Les articles suivants vous guidera tout au long de la gestion des liens vers le circuit ExpressRoute :

- [Créer un lien votre réseau virtuel vers votre circuit ExpressRoute dans le modèle de déploiement du Gestionnaire de ressources](expressroute-howto-linkvnet-arm.md)
- [Créer un lien votre réseau virtuel vers votre circuit ExpressRoute dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Désactiver le circuit ExpressRoute pour le modèle de déploiement classique

Exécutez l’applet de commande suivante pour désactiver l’accès au modèle de déploiement classique :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous ne serez pas en mesure d’afficher le circuit dans le modèle de déploiement classique.

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, vérifiez que vous effectuez le des opérations suivantes :

- [Créer et modifier la gamme pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Créer un lien vers votre circuit ExpressRoute votre réseau virtuel](expressroute-howto-linkvnet-arm.md)
