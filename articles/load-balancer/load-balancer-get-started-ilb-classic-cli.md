<properties
   pageTitle="Créer un équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Créer un équilibrage de charge interne (standard) à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Pour créer un programme d’équilibrage de charge interne définissez pour les machines virtuelles

Pour créer un jeu d’équilibrage de charge interne et les serveurs qui vous envoie le trafic vers celui-ci, vous devez procédez comme suit :

1. Créer une instance d’interne l’équilibrage de charge qui sera le point de terminaison de trafic entrant pour être équilibrée sur les serveurs d’un ensemble d’équilibrage de charge.

1. Ajouter des points de terminaison correspondant aux machines virtuelles qui recevront le trafic entrant.

1. Configurer les serveurs qui enverront le trafic vers charge être équilibrée pour envoyer leur le trafic vers l’adresse IP virtuelle (VIP) de l’instance d’équilibrage de charge interne.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Création d’un équilibrage de charge interne à l’aide d’infrastructure du langage commun étape par étape

Ce guide montre comment créer un équilibrage de charge interne en fonction du scénario ci-dessus.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode classique, comme illustré ci-dessous.

        azure config mode asm

    Résultat attendu :

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Créer le point de terminaison et charger ensemble d’équilibrage

Le scénario suppose que les ordinateurs virtuels dégressif « 1 » et « DB2 » dans un service cloud appelée « mytestcloud ». Les deux machines virtuelles utilisent un réseau virtuel appelé Mon « testvnet » avec sous-réseau « sous-réseau-1 ».

Ce guide crée un jeu d’équilibrage de charge interne en utilisant le port 1433 comme port privé et 1433 comme port local.

Il s’agit d’un scénario courant dans lequel vous avez machines virtuelles SQL sur le serveur principal à l’aide d’un programme d’équilibrage de charge interne afin de garantir que les serveurs de base de données ne sera pas exposés directement à l’aide d’une adresse IP publique.


### <a name="step-1"></a>Étape 1

Créer un équilibrage de charge interne à l’aide de `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Paramètres utilisés :

**-r** - nom de service de nuage<BR>
**-n** - nom d’équilibrage de charge interne<BR>
**-t** - nom de sous-réseau (même sous réseau par les machines virtuelles que vous ajouterez à l’équilibrage de charge interne)<BR>
**-a** - (facultatif) ajouter une adresse IP privée<BR>

Consultez `azure service internal-load-balancer --help` pour plus d’informations.

Vous pouvez vérifier les propriétés d’équilibrage de charge interne à l’aide de la commande `azure service internal-load-balancer list` *nom du service cloud*.

Cet exemple suit un exemple de sortie :

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Étape 2

Vous configurez le jeu d’équilibrage de charge interne lorsque vous ajoutez le premier point de terminaison. Vous allez associer le point de terminaison, la machine virtuelle et le port sonde à l’ensemble d’équilibrage de charge interne dans cette étape.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Paramètres utilisés :

**-k** - port local machine virtuelle<BR>
**-t** - sonde port<BR>
**-r** - sonde protocole<BR>
**-e** - intervalle sonde en secondes<BR>
**-f** - délai d’expiration en secondes <BR>
**-i** - nom d’équilibrage de charge interne <BR>


## <a name="step-3"></a>Étape 3

Vérifier la configuration d’équilibrage de charge à l’aide `azure vm show` *nom de la machine virtuelle*

    azure vm show DB1

Le résultat sera :

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Créer un point de terminaison de bureau à distance pour une machine virtuelle

Vous pouvez créer un point de terminaison de bureau à distance pour transférer le trafic réseau à partir d’un port public à un port local pour une machine virtuelle spécifique à l’aide `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Supprimer la machine virtuelle d’équilibrage de charge

Vous pouvez supprimer une machine virtuelle à partir d’un équilibrage de charge interne définir en supprimant le point de terminaison associé. Une fois que le point de terminaison est supprimé, la machine virtuelle n’appartiennent à l’équilibrage de charge définir plus.

 À l’aide de l’exemple ci-dessus, vous pouvez supprimer le point de terminaison créé pour machine virtuelle dégressif « 1 » à partir d’équilibrage de charge interne « ilbset » à l’aide de la commande `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Consultez `azure vm endpoint --help` pour plus d’informations.


## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge à l’aide de l’affinité IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)