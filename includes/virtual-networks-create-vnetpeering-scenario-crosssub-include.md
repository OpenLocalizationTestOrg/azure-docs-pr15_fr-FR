## <a name="peering-across-subscriptions"></a>Peering abonnements

Dans ce scénario, vous allez créer un peering entre deux VNets appartenant à différents abonnements.

![scénario sub croisée](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet peering repose sur contrôle d’accès basé sur un rôle (RBAC) pour l’autorisation. Pour le scénario de manière croisée abonnements, vous devez tout d’abord octroi d’autorisations suffisantes aux utilisateurs qui crée le lien d’homologation :

> [AZURE.NOTE] Si le même utilisateur dispose du privilège sur les deux abonnements, vous pouvez ignorer les étapes 1 à 4 ci-après.
