<properties
   pageTitle="Flux de travail pour configurer un circuit ExpressRoute | Microsoft Azure"
   description="Cette page vous guide dans les flux de travail pour la configuration peerings et circuit ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Flux de travail ExpressRoute pour circuit États et de mise en service de circuit

Cette page vous guide dans le service de mise en service et le routage de flux de travail de configuration à un niveau élevé.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

L’illustration suivante et les étapes affichent les tâches que vous devez suivre pour avoir un circuit ExpressRoute sa mise en service de bout en bout. 

1. Utiliser PowerShell pour configurer un circuit ExpressRoute. Suivez les instructions de l’article [circuits créer ExpressRoute](expressroute-howto-circuit-classic.md) pour plus d’informations.

2. Commander connectivité auprès du fournisseur de services. Ce processus varie. Contactez votre fournisseur de connectivité pour plus d’informations sur l’ordre de connectivité.

3. Assurez-vous que le circuit ait été configuré correctement en vérifiant le circuit ExpressRoute état via PowerShell de mise en service. 

4. Configurer des domaines de routage. Si votre fournisseur de connectivité gère Layer 3 pour vous, qu’ils configure le routage pour votre circuit. Si votre fournisseur de connectivité propose uniquement Layer 2 services, vous devez configurer routage selon les directives décrites dans les pages [Configuration requise pour le routage](expressroute-routing.md) et la [configuration du routage](expressroute-howto-routing-classic.md) .

    -  Activer peering privé Azure : vous devez activer cette peering pour se connecter aux machines virtuelles / cloud services déployés au sein de réseaux virtuels.
    -  Activer peering public Azure : vous devez activer peering public Azure si vous souhaitez vous connecter aux services Azure hébergés sur des adresses IP publiques. Cette procédure est nécessaire pour accéder aux ressources Azure si vous avez choisi d’activer le routage par défaut pour Azure peering privé.
    -  Activer Microsoft peering - vous devez activer cette option accès à Office 365 et des services en ligne CRM. 
    
    >[AZURE.IMPORTANT] Vous devez vous assurer que vous utilisez un proxy distincts / bord pour vous connecter à Microsoft que celui que vous utilisez pour Internet. À l’aide du même bord pour ExpressRoute et Internet entraîne routage asymétriques et entraîner des pannes de connexion pour votre réseau.

    ![](./media/expressroute-workflows/routing-workflow.png)


5. Liaison réseaux virtuels circuits ExpressRoute - vous pouvez lier des réseaux virtuels à votre circuit ExpressRoute. Suivez les instructions [pour créer un lien VNets](expressroute-howto-linkvnet-arm.md) à votre circuit. Ces VNets peuvent être dans le même abonnement Azure en tant que le circuit ExpressRoute ou peut se trouver dans un autre abonnement.


## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute circuit mise en service des États

Chaque circuit ExpressRoute comporte deux états :

- État du service de mise en service fournisseur
- État

État représente l’état de mise en service de Microsoft. Cette propriété est définie sur activé lorsque vous créez un circuit Expressroute

L’état de mise en service de fournisseur de connectivité représente l’état de côté du fournisseur connectivité. Il peut être *NotProvisioned*, *Provisioning*ou *Provisioned*. Le circuit ExpressRoute doit être dans un état Provisioned pour pouvoir l’utiliser.

### <a name="possible-states-of-an-expressroute-circuit"></a>États possibles d’un circuit ExpressRoute

Cette section répertorie les états possibles pour un circuit ExpressRoute.

#### <a name="at-creation-time"></a>Lors de sa création

Vous verrez le circuit ExpressRoute dans l’état suivant dès que vous exécutez l’applet de commande PowerShell pour créer le circuit ExpressRoute.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


#### <a name="when-connectivity-provider-is-in-the-process-of-provisioning-the-circuit"></a>Lorsque fournisseur de connectivité est en cours de la mise en service du circuit

Vous verrez le circuit ExpressRoute dans l’état suivant dès que vous passez à la clé du service au fournisseur de connectivité et qu’ils ont commencé le processus de configuration.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


#### <a name="when-connectivity-provider-has-completed-the-provisioning-process"></a>Lorsque le fournisseur de connectivité a terminé le processus de mise en service

Vous verrez le circuit ExpressRoute dans l’état suivant dès que le fournisseur de connectivité a terminé le processus de mise en service.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Sa mise en service et activé est que le seul état le circuit peut se trouver dans pour pouvoir l’utiliser. Si vous utilisez un fournisseur Layer 2, vous pouvez configurer le routage pour votre circuit uniquement lorsqu’il est dans cet état.

#### <a name="when-connectivity-provider-is-deprovisioning-the-circuit"></a>Lorsque le fournisseur de connectivité est hors service le circuit

Si vous avez demandé le fournisseur de services à deprovision le circuit ExpressRoute, vous verrez le circuit défini sur l’état suivant une fois que le fournisseur de services a terminé le processus d’annulation.


    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Vous pouvez choisir de le réactiver si nécessaire, ou exécuter les applets de commande PowerShell pour supprimer le circuit.  

>[AZURE.IMPORTANT] Si vous exécutez l’applet de commande PowerShell pour supprimer le circuit lorsque le ServiceProviderProvisioningState est mise en service ou Provisioned l’opération échouera. Contactez votre fournisseur de connectivité à deprovision le circuit ExpressRoute tout d’abord, puis supprimez le circuit. Microsoft continuent à facturer le circuit jusqu'à ce que vous exécutez l’applet de commande PowerShell pour supprimer le circuit.


## <a name="routing-session-configuration-state"></a>État de la configuration session routage

L’état de mise en service de BGP vous permet de déterminer si la session BGP a été activée sur le bord de Microsoft. L’état doit être activée pour que vous êtes en mesure d’utiliser le peering.

Il est important de vérifier l’état de session BGP en particulier pour Microsoft peering. Outre BGP mise en service d’état, il existe une autre état appelé *publiés état préfixes public*. L’état publié préfixes public doit être placé dans l’état *configuré* , à la fois pour la session BGP à être opérationnel et pour votre routage pour travailler de bout en bout. 

Si l’état de préfixe public publié est défini sur un état *validation nécessaire* , la session BGP n’est pas activée, car les préfixes publiés correspondent pas le nombre en tant qu’un des bureaux d’ordre routage. 

>[AZURE.IMPORTANT] Si l’état publié préfixes public est dans l’état de *validation manuelle* , vous devez ouvrir une tickets de support avec [Microsoft prend en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et prouver que vous possédez les adresses IP publiés le long avec le numéro de système autonome associé.


## <a name="next-steps"></a>Étapes suivantes

- Configurer votre connexion ExpressRoute.

    - [Créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurer le routage](expressroute-howto-routing-arm.md)
    - [Lier un VNet à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)
