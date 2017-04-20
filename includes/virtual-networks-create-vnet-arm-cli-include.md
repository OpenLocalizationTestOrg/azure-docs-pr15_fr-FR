## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Comment créer un VNet à l’aide de l’infrastructure du langage commun Azure

Vous pouvez utiliser l’infrastructure du langage commun Azure pour gérer vos ressources Azure à partir de l’invite de commande à partir de n’importe quel ordinateur exécutant Windows, Linux ou OSX. Pour créer un VNet à l’aide de l’infrastructure du langage commun Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé le Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécutez la commande **mode config azure** pour passer en mode directeur des ressources, comme illustré ci-dessous.

        azure config mode arm

    Voici le résultat attendu de la commande ci-dessus :

        info:    New mode is arm

3. Si nécessaire, exécutez le **groupe azure créer** pour créer un nouveau groupe de ressources, comme illustré ci-dessous. Notez la sortie de la commande. La liste visible après la sortie explique les paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

        azure group create -n TestRG -l centralus

    Voici le résultat attendu de la commande ci-dessus :

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (ou--nom)**. Nom du nouveau groupe de ressources. Pour notre scénario, *TestRG*.
    - **-l (ou--emplacement)**. Région Azure où sera créé le nouveau groupe de ressources. Pour notre scénario, *centralus*.

4. Exécutez la commande **vnet réseau azure créer** pour créer un VNet et un sous-réseau, comme illustré ci-dessous. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Voici le résultat attendu de la commande ci-dessus :

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (ou--groupe de ressources)**. Nom du groupe de ressources où le VNet sera créé. Pour notre scénario, *TestRG*.
    - **-n (ou--nom)**. Nom de la VNet à créer. Pour notre scénario, *TestVNet*
    - **-un (ou--préfixes d’adresse)**. Liste de blocs CIDR utilisés pour l’espace d’adressage VNet. Pour notre scénario, *192.168.0.0/16*
    - **-l (ou--emplacement)**. Région Azure où le VNet sera créé. Pour notre scénario, *centralus*.

5. Exécutez la commande **vnet sous-réseau azure créer** pour créer un sous-réseau comme illustré ci-dessous. Notez la sortie de la commande. La liste visible après la sortie explique les paramètres utilisés.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Voici le résultat attendu de la commande ci-dessus :

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (ou nom-vnet--**. Nom de la VNet où le sous-réseau est créé. Pour notre scénario, *TestVNet*.
    - **-n (ou--nom)**. Nom du nouveau sous-réseau. Pour notre scénario, *FrontEnd*.
    - **-un (ou--préfixe d’adresse)**. Bloc CIDR sous-réseau. Quatre notre scénario, *192.168.1.0/24*.

6. Répétez l’étape 5 ci-dessus pour créer d’autres sous-réseaux, le cas échéant. Pour notre scénario, exécutez la commande suivante pour créer le sous-réseau *principal* .

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Exécutez la commande **vnet réseau azure afficher** pour afficher les propriétés de la nouvelle vnet, comme illustré ci-dessous.

        azure network vnet show -g TestRG -n TestVNet

    Voici le résultat attendu de la commande ci-dessus :

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
