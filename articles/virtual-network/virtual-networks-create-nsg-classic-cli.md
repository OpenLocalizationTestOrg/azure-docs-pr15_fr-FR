<properties
   pageTitle="Comment créer des NSGs en mode classique à l’aide de l’infrastructure du langage commun Azure | Microsoft Azure"
   description="Découvrez comment créer et déployer NSGs en mode classique à l’aide de l’infrastructure du langage commun Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Comment créer NSGs (classique) dans l’infrastructure du langage commun Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [créer NSGs dans le modèle de déploiement du Gestionnaire de ressources](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes Azure infrastructure du langage commun ci-dessous vous attendez un environnement simple déjà créé à partir du scénario ci-dessus. Si vous souhaitez exécuter les commandes qu’ils s’affichent dans ce document, commencez par créer l’environnement de test en [créant un VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Comment créer le NSG pour le sous-réseau frontal
Pour créer un NSG nommé nommée **NSG FrontEnd** en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécuter le **`azure config mode`** commande pour passer en mode classique, comme illustré ci-dessous.

        azure config mode asm

    Résultat attendu :

        info:    New mode is asm

3. Exécuter le **`azure network nsg create`** commande pour créer un NSG.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Résultat attendu :

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Paramètres :

    - **-l (ou--emplacement)**. Région Azure où le nouveau NSG est créé. Pour notre scénario, *westus*.
    - **-n (ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG FrontEnd*.

4. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 3389 (RDP) à partir d’Internet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Résultat attendu :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Paramètres :

    - **-un (ou--nsg nom)**. Nom de la NSG dans lequel la règle est créée. Pour notre scénario, *NSG FrontEnd*.
    - **-n (ou--nom)**. Nom de la nouvelle règle. Pour notre scénario, *rdp règle*.
    - **-c (ou--action)**. Niveau d’accès de la règle (autoriser ou refuser).
    - **-p (ou--protocol)**. Protocole (Tcp, Udp, ou *) pour la règle.
    - **-r (ou--type)**. Sens de connexion (entrant ou sortant).
    - **-y (ou--priorité)**. Priorité pour la règle.
    - **-f (ou--préfixe d’adresse source)**. Préfixe d’adresse source CIDR ou en utilisant les balises par défaut.
    - **+ o (ou--plage de port source)**. Port source ou plage de ports.
    - **-e (ou--préfixe d’adresse destination)**. Préfixe d’adresse de destination CIDR ou en utilisant les balises par défaut.
    - **-u (ou--plage de port de destination)**. Port de destination, ou plage de ports.

5. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 80 (HTTP) à partir d’Internet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Putput attendu :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Exécuter le **`azure network nsg subnet add`** commande à lier le NSG au sous-réseau frontal.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Résultat attendu :

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Comment créer le NSG pour le sous-réseau principal
Pour créer un NSG nommée nommée *NSG principal* en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`azure network nsg create`** commande pour créer un NSG.

        azure network nsg create -l uswest -n NSG-BackEnd

    Résultat attendu :

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Paramètres :

    - **-l (ou--emplacement)**. Région Azure où le nouveau NSG est créé. Pour notre scénario, *westus*.
    - **-n (ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG FrontEnd*.

4. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui autorise l’accès au port 1433 (SQL) à partir du sous-réseau frontal.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Résultat attendu :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Exécuter le **`azure network nsg rule create`** commande pour créer une règle qui refuse l’accès à Internet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Putput attendu :

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Exécuter le **`azure network nsg subnet add`** commande à lier le NSG au sous-réseau principal.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Résultat attendu :

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
