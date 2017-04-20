<properties
   pageTitle="Créer un Internet facing équilibrage de charge dans le Gestionnaire de ressources à l’aide de l’infrastructure du langage commun Azure | Microsoft Azure"
   description="Apprenez à créer un Internet facing équilibrage de charge dans le Gestionnaire de ressources à l’aide de l’infrastructure du langage commun Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Création d’un programme d’équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [découvrir comment créer un Internet facing équilibrage de charge à l’aide de déploiement classique](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Déploiement de la solution à l’aide de l’infrastructure du langage commun Azure

Les étapes suivantes montrent comment créer un Internet facing équilibrage de charge à l’aide du Gestionnaire de ressources Azure avec infrastructure du langage commun. Avec le Gestionnaire de ressources Azure chaque ressource est créé et configuré individuellement, puis place ensemble pour créer une ressource.

Vous devez créer et configurer les objets suivants pour déployer un équilibrage de charge :

- Configuration IP frontale - contient les adresses IP publiques pour le trafic réseau entrant.
- Adresse principale pool - contient interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau l’équilibrage de charge.
- L’équilibrage de charge des règles - contient des règles de mappage d’un port public sur l’équilibrage de charge sur le port du pool d’adresses principale.
- NAT règles de trafic entrant - contient les règles de mappage d’un port public sur l’équilibrage de charge vers un port pour une machine virtuelle spécifique du pool d’adresses principale.
- Teste - contient sondes santé utilisées pour vérifier la disponibilité d’instances de machines virtuelles du pool d’adresses principale.

Pour plus d’informations, voir [Prise en charge pour l’équilibrage de charge le Gestionnaire de ressources Azure](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurer l’infrastructure du langage commun à utiliser le Gestionnaire de ressources

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode directeur des ressources, comme illustré ci-dessous.

        azure config mode arm

    Résultat attendu :

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créer un réseau virtuel et une adresse IP publique pour le pool frontal d’IP

1. Créer un réseau virtuel (VNet) nommé *NRPVnet* à l’emplacement américain Extrême-Orient à l’aide d’un groupe de ressources nommé *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Créer un sous-réseau nommé *NRPVnetSubnet* avec un bloc CIDR 10.0.0.0/24 dans *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Créer une adresse IP publique nommée *NRPPublicIP* devant être utilisé par un pool frontal IP avec nom DNS *loadbalancernrp.eastus.cloudapp.azure.com*. La commande ci-dessous utilise le type d’allocation statique et le délai d’inactivité de 4 minutes.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]L’équilibrage de charge utilisera le nom de domaine de l’adresse IP publique en tant que son nom complet. Ce service une modification à partir de déploiement classique, qui utilise le cloud comme nom de domaine complet (FQDN) de l’équilibrage de charge.
    >Dans cet exemple, le nom de domaine complet est *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Créer un programme d’équilibrage de charge

La commande suivante crée un équilibrage de charge nommé *NRPlb* dans le groupe de ressources *NRPRG* aux *États-Unis* l’emplacement Azure.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Créer un pool frontal IP et un pool d’adresses serveur principal

Cet exemple montre comment créer le pool frontal IP qui reçoit le trafic réseau entrant sur l’équilibrage de charge et le pool d’IP serveur principal dans laquelle le pool frontal envoie le trafic réseau équilibré de charge.

1. Créer un pool frontal d’IP associer l’adresse IP publique créé dans l’étape précédente et l’équilibrage de charge.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configurer un pool d’adresses de principale utilisé pour recevoir le trafic entrant depuis le pool frontal d’IP.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Créer des règles kg, règles NAT et sonde

Cet exemple montre comment créer les éléments suivants.

- une règle NAT pour traduire tout le trafic entrant sur le port 21 port 22<sup>1</sup>
- une règle NAT pour traduire tout le trafic entrant sur le port 23 à la voie 22
- une règle d’équilibrage de charge équilibrer tout le trafic entrant sur le port 80 au port 80 sur les adresses dans la liste principale.
- une règle sonde pour vérifier l’état d’intégrité d’une page nommée *HealthProbe.aspx*.

<sup>1</sup> les règles NAT sont associés à une instance de machine virtuelle spécifique derrière l’équilibrage de charge. Le trafic réseau arrivant sur le port 21 est envoyé vers une machine virtuelle spécifique sur le port 22 associées à cette règle NAT. Vous devez spécifier un protocole (UDP ou TCP) pour une règle NAT. Les deux protocoles ne peut pas être affectées au même port.

1. Créez les règles NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Créer une règle d’équilibrage de charge.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Créer une sonde d’intégrité.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Vérifiez vos paramètres.

        azure network lb show nrprg nrplb

    Résultat attendu :

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Créer des cartes réseau

Vous avez besoin créer des cartes réseau (ou modifier des modèles existants) et associez à sondes, des règles d’équilibrage de charge et des règles NAT.

1. Créer une carte réseau nommée *être kg-nic1*et l’associer à cette règle NAT *rdp1* et du pool d’adresses de principale *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Résultat attendu :

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Créer une carte réseau nommée *être kg-carte réseau 2*et l’associer à cette règle NAT *rdp2* et du pool d’adresses de principale *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Créer une machine virtuelle (machine virtuelle) nommée *web1*et l’associer à la carte réseau nommée *être kg-nic1*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Machines virtuelles dans un programme d’équilibrage de charge doivent se trouver dans le même jeu de disponibilité. Utiliser `azure availset create` pour créer une disponibilité définie.

    La sortie doit être semblable à ce qui suit :

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] Le message d’information, **il s’agit d’une carte réseau sans publicIP configuré** est attendu depuis la carte réseau créée pour l’équilibrage de charge connexion à Internet à l’aide de l’adresse IP équilibrage de la charge.

    Dans la mesure où *l’être kg-nic1* carte réseau est associé à la règle NAT *rdp1* , vous pouvez vous connecter à *web1* à l’aide de RDP via le port 3441 sur l’équilibrage de charge.

4. Créer une machine virtuelle (machine virtuelle) nommée *web2*et l’associer à la carte réseau nommée *être kg-carte réseau 2*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Mettre à jour un équilibrage de charge existant

Vous pouvez ajouter des règles faisant référence à un équilibrage de charge existant. Dans l’exemple suivant, une nouvelle règle d’équilibrage de charge est ajoutée à un équilibrage de charge existant **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Supprimer un programme d’équilibrage de charge

Utilisez la commande suivante pour supprimer un programme d’équilibrage de charge :

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
