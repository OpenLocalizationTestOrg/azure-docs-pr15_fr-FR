## <a name="service-chaining---transit-through-peered-vnet"></a>Service Chaining - passage VNet ressources

Bien que l’utilisation des itinéraires système facilite le trafic automatiquement de votre déploiement, il existe des cas dans lequel vous souhaitez contrôler le routage de paquets via une application virtuelle.
Dans ce scénario, il existe deux VNets un abonnement, HubVNet et VNet1 comme décrit dans l’illustration ci-dessous. Vous déployez réseau virtuel Appliance(NVA) dans VNet HubVNet. Après avoir établi VNet peering entre HubVNet et VNet1, vous pouvez définir des itinéraires définies par l’utilisateur et spécifier le prochain saut à couloirs dans la HubVNet.

![Voies à couloirs](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Pour la simplicité, supposons que tous les VNets ici se trouvent dans le même abonnement. Mais il fonctionne également pour le scénario de manière croisée abonnement.

La propriété de clé pour activer le routage voies est le paramètre « Autoriser le trafic de transférés ». Cela permet d’accepter et l’envoi du trafic aux couloirs à partir de/vers le ressources VNet.  
