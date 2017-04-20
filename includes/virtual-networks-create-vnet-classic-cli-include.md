## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Comment créer un VNet classique à l’aide d’infrastructure du langage commun Azure

Vous pouvez utiliser l’infrastructure du langage commun Azure pour gérer vos ressources Azure à partir de l’invite de commande à partir de n’importe quel ordinateur exécutant Windows, Linux ou OSX. Pour créer un VNet à l’aide de l’infrastructure du langage commun Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécutez la commande **vnet réseau azure créer** pour créer un VNet et un sous-réseau, comme illustré ci-dessous. La liste visible après la sortie explique les paramètres utilisés.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Résultat attendu :

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **--vnet**. Nom de la VNet à créer. Pour notre scénario, *TestVNet*
    - **-e (ou--espace d’adressage)**. Espace d’adressage VNet. Pour notre scénario, *192.168.0.0*
    - **-i (ou - cidr)**. Masque de réseau au format CIDR. Pour notre scénario, *16*.
    - **- n (ou--nom de sous-réseau**). Nom du premier sous-réseau. Pour notre scénario, *FrontEnd*.
    - **-p (ou--sous-réseau-début-ip)**. Adresse IP de début pour sous-réseau ou un espace d’adressage sous-réseau. Pour notre scénario, *192.168.1.0*.
    - **-r (ou--sous-réseau cidr)**. Masque de réseau au format CIDR pour sous-réseau. Pour notre scénario, *24*.
    - **-l (ou--emplacement)**. Région Azure où le VNet sera créé. Pour notre scénario, *États-Unis centre*.

3. Exécutez la commande **vnet sous-réseau azure créer** pour créer un sous-réseau comme illustré ci-dessous. La liste visible après la sortie explique les paramètres utilisés.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Voici le résultat attendu de la commande ci-dessus :

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (ou nom-vnet--**. Nom de la VNet où le sous-réseau est créé. Pour notre scénario, *TestVNet*.
    - **-n (ou--nom)**. Nom du nouveau sous-réseau. Pour notre scénario, *serveur principal*.
    - **-un (ou--préfixe d’adresse)**. Bloc CIDR sous-réseau. Quatre notre scénario, *192.168.2.0/24*.

4. Exécutez la commande **vnet réseau azure afficher** pour afficher les propriétés de la nouvelle vnet, comme illustré ci-dessous.

            azure network vnet show

    Voici le résultat attendu de la commande ci-dessus :

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
