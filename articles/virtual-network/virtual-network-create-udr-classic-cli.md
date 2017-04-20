<properties 
   pageTitle="Contrôler le routage et utiliser l’authentification multifacteur à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Découvrez comment contrôler routage dans VNets à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Contrôler le routage et utiliser l’authentification multifacteur (classique) à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [contrôle routage et utiliser l’authentification multifacteur dans le modèle de déploiement du Gestionnaire de ressources](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes Azure infrastructure du langage commun ci-dessous vous attendez un environnement simple déjà créé à partir du scénario ci-dessus. Si vous souhaitez exécuter les commandes qu’ils s’affichent dans ce document, créez l’environnement [créer un VNet (classique) à l’aide de l’infrastructure du langage commun Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Créer la UDR pour le sous-réseau frontal
Pour créer la table de routage et la gamme nécessaires pour le sous-réseau frontal en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécuter le **`azure config mode`** pour passer en mode classique.

        azure config mode asm

    Résultat :

        info:    New mode is asm

3. Exécuter le **`azure network route-table create`** commande pour créer une table de route pour le sous-réseau frontal.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Résultat :

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Paramètres :
    - **-l (ou--emplacement)**. Région Azure où le nouveau NSG est créé. Pour notre scénario, *westus*.
    - **-n (ou--nom)**. Nom de la nouvelle NSG. Pour notre scénario, *NSG FrontEnd*.

4. Exécuter le **`azure network route-table route set`** commande pour créer un itinéraire dans la table d’itinéraires créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau principal (192.168.2.0/24) pour la **FW1** machine virtuelle (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Résultat :

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Paramètres :
    - **-r (ou--itinéraire de nom de table)**. Nom de la table itinéraire où l’itinéraire sera ajouté. Pour notre scénario, *UDR FrontEnd*.
    - **-un (ou--préfixe d’adresse)**. Préfixe d’adresse pour le sous-réseau où les paquets sont destinées à. Pour notre scénario, *192.168.2.0/24*.
    - **-t (ou--type du saut suivant)**. Type de trafic objet lui être envoyé. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *Aucun*.
    - **-p (ou--saut suivant-adresse ip**). Adresse IP de saut suivant. Pour notre scénario, *192.168.0.4*.

5. Exécuter le **`azure network vnet subnet route-table add`** commande pour associer la table itinéraire créée avec le sous-réseau **FrontEnd** ci-dessus.

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Résultat :

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Paramètres :
    - **-t (ou--vnet-name)**. Nom de la VNet où se trouve le sous-réseau. Pour notre scénario, *TestVNet*.
    - **- n (ou--nom de sous-réseau**. Nom du sous-réseau la table d’itinéraires est ajouté à. Pour notre scénario, *FrontEnd*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer la UDR pour le sous-réseau principal
Pour créer la table de routage et la gamme nécessaires pour le sous-réseau back-end en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécuter le **`azure network route-table create`** commande pour créer une table de route pour le sous-réseau principal.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Exécuter le **`azure network route-table route set`** commande pour créer un itinéraire dans la table d’itinéraires créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) pour la **FW1** machine virtuelle (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Exécuter le **`azure network vnet subnet route-table add`** commande pour associer la table itinéraire créée avec le **serveur principal** sous-réseau ci-dessus.

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

