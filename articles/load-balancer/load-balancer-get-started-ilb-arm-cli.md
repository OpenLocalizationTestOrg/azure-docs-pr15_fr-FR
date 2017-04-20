<properties
   pageTitle="Créer un programme d’équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer un programme d’équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure dans le Gestionnaire de ressources"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Créer un programme d’équilibrage de charge interne à l’aide de l’infrastructure du langage commun Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Déployer la solution à l’aide de l’infrastructure du langage commun Azure

Les étapes suivantes montrent comment créer un équilibrage de charge via Internet en utilisant le Gestionnaire de ressources Azure avec infrastructure du langage commun. Avec le Gestionnaire de ressources Azure, chaque ressource est créé et configuré individuellement et mettez ensuite ensemble pour créer une ressource.

Vous devez créer et configurer les objets suivants pour déployer un équilibrage de charge :

- **Configuration IP front**: contient les adresses IP publiques pour le trafic réseau entrant
- **Pool d’adresses principale**: contient des interfaces réseau (cartes réseau) qui permettent les machines virtuelles recevoir le trafic réseau l’équilibrage de charge
- **Règles d’équilibrage de charge**: contient des règles qui mapper un port public sur l’équilibrage de charge sur le port du pool d’adresses principale
- **Règles de trafic entrant NAT**: contient des règles qui mapper un port public sur l’équilibrage de charge à un port pour une machine virtuelle spécifique du pool d’adresses principale
- **Teste**: contient sondes santé qui sont utilisés pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principale

Pour plus d’informations, voir [Gestionnaire de ressources Azure prend en charge pour l’équilibrage de charge](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurer l’infrastructure du langage commun à utiliser le Gestionnaire de ressources

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md). Suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode directeur des ressources, comme suit :

        azure config mode arm

    Résultat attendu :

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Créer un programme d’équilibrage de charge interne étape par étape

1. Se connecter à Azure.

        azure login

    Lorsque vous y êtes invité, entrez vos informations d’identification Azure.

2. Modifier les outils de commande en mode Azure le Gestionnaire de ressources.

        azure config mode arm

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Toutes les ressources dans le Gestionnaire de ressources Azure sont associés à un groupe de ressources. Si vous n’avez pas déjà fait, créez un groupe de ressources.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Créer un jeu d’équilibrage de charge interne

1. Créer un programme d’équilibrage de charge interne

    Dans le scénario suivant, un groupe de ressources nommé nrprg est créé dans la région des États-Unis Extrême-Orient.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Toutes les ressources pour un programmes d’équilibrage de charge interne, telles que les réseaux virtuels et sous-réseaux réseau virtuel, doivent être dans le même groupe de ressources et dans la même région.

2. Créer une adresse IP frontale pour l’équilibrage de charge interne.

    L’adresse IP que vous utilisez doit figurer dans la plage de sous-réseau de votre réseau virtuel.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Créer le pool d’adresses principale.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Après avoir défini une adresse IP frontale et un pool d’adresses principale, vous pouvez créer des règles d’équilibrage de charge, règles de trafic entrant NAT, et teste santé personnalisées.

4. Créer une règle d’équilibrage de charge pour l’équilibrage de charge interne.

    Lorsque vous suivez la procédure précédente, la commande crée une règle d’équilibrage de charge pour écouter le port 1433 dans le pool frontal et envoi d’équilibrage de charge le trafic réseau au pool d’adresses principale, utilisant également le port 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Créer des règles de trafic entrant NAT.

    Règles de trafic entrant NAT sont utilisés pour créer des points de terminaison dans un équilibrage de charge atteindre une instance de machine virtuelle spécifique. Les étapes précédentes créés des deux règles NAT Bureau à distance.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Créer des tests d’intégrité pour l’équilibrage de charge.

    Une sonde santé vérifie toutes les instances de machine virtuelle pour vous assurer que vous pouvez envoyer le trafic réseau. L’instance machine virtuelle avec vérifications de sondages échec est supprimé de l’équilibrage de charge jusqu'à ce qu’il est en ligne et une vérification sonde détermine qu’il est correct.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]La plate-forme Microsoft Azure utilise une adresse IPv4 statique publiquement prenant pour divers scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par un pare-feu, car cela peut entraîner un comportement inattendu.
    >En ce qui concerne Azure interne l’équilibrage de charge, cette adresse IP est utilisée par la surveillance sondes à partir de l’équilibrage de charge pour déterminer l’état d’intégrité des machines virtuelles dans un jeu d’équilibrage de charge. Si un groupe de sécurité réseau est utilisé pour restreindre le trafic vers Azure machines virtuelles dans un jeu d’équilibrage de charge en interne ou est appliqué à un sous-réseau virtuel, assurez-vous qu’une règle de sécurité réseau est ajoutée à autoriser le trafic de 168.63.129.16.

## <a name="create-nics"></a>Créer des cartes réseau

Vous avez besoin créer des cartes réseau (ou modifier des modèles existants) et associez à sondes, des règles d’équilibrage de charge et des règles NAT.

1. Créer une carte réseau nommée *être kg-nic1*, puis l’associer à la règle NAT *rdp1* et du pool d’adresses de principale *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Créer une carte réseau nommée *être kg-carte réseau 2*, puis l’associer à la règle NAT *rdp2* et du pool d’adresses de principale *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Créer une machine virtuelle nommée *dégressif 1*et puis l’associer à la carte réseau nommée *être kg-nic1*. Un compte de stockage appelé *web1nrp* est créé avant l’exécution de la commande suivante :

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Machines virtuelles dans un programme d’équilibrage de charge doivent se trouver dans le même jeu de disponibilité. Utiliser `azure availset create` pour créer une disponibilité définie.

4. Créer une machine virtuelle (machine virtuelle) nommée *DB2*et puis l’associer à la carte réseau nommée *être kg-carte réseau 2*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande suivante.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Supprimer un programme d’équilibrage de charge

Pour supprimer un programme d’équilibrage de charge, utilisez la commande suivante :

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge à l’aide de l’affinité IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
