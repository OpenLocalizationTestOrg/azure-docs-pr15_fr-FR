<properties
    pageTitle="Créer un Internet facing équilibrage de charge du protocole IPv6 dans Gestionnaire de ressources Azure à l’aide de l’infrastructure du langage commun Azure | Microsoft Azure"
    description="Apprenez à créer un Internet facing équilibrage de charge du protocole IPv6 dans Gestionnaire de ressources Azure à l’aide de l’infrastructure du langage commun Azure"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="le protocole IPv6, équilibrage de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Créer un Internet facing équilibrage de charge du protocole IPv6 dans Gestionnaire de ressources Azure à l’aide de l’infrastructure du langage commun Azure

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure infrastructure du langage commun](./load-balancer-ipv6-internet-cli.md)
- [Modèle](./load-balancer-ipv6-internet-template.md)

Un équilibrage de charge Azure est un équilibrage de charge calque-4 (TCP et UDP). L’équilibrage de charge offre une grande disponibilité en distribuer le trafic entrant entre les instances de service exact dans les services en nuage ou machines virtuelles dans un jeu d’équilibrage de charge. Équilibrage de charge Azure peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

## <a name="example-deployment-scenario"></a>Exemple de scénario de déploiement

Le diagramme suivant illustre la solution d’équilibrage de charge déployées à l’aide de l’exemple de modèle décrit dans cet article.

![Scénario d’équilibrage de charge](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Dans ce scénario, vous allez créer les ressources Azure suivantes :

- deux machines virtuelles (machines virtuelles)
- une interface de réseau virtuel pour chaque machine virtuelle avec des adresses IPv4 et IPv6 affectées
- un équilibrage de charge via Internet IPv4 et une adresse IP publique du protocole IPv6
- un ensemble de disponibilité à qui contient les deux ordinateurs virtuels
- deux charger équilibrage de charge des règles pour mapper la VIP publique aux points de terminaison privés

## <a name="deploying-the-solution-using-the-azure-cli"></a>Déploiement de la solution à l’aide de l’infrastructure du langage commun Azure

Les étapes suivantes montrent comment créer un Internet facing équilibrage de charge à l’aide du Gestionnaire de ressources Azure avec infrastructure du langage commun. Avec le Gestionnaire de ressources Azure, chaque ressource est créé et configuré individuellement et mettez ensuite ensemble pour créer une ressource.

Pour déployer un équilibrage de charge, vous créez et configurez les objets suivants :

- Configuration IP frontale - contient les adresses IP publiques pour le trafic réseau entrant.
- Adresse principale pool - contient interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau l’équilibrage de charge.
- L’équilibrage de charge des règles - contient des règles de mappage d’un port public sur l’équilibrage de charge sur le port du pool d’adresses principale.
- NAT règles de trafic entrant - contient les règles de mappage d’un port public sur l’équilibrage de charge vers un port pour une machine virtuelle spécifique du pool d’adresses principale.
- Teste - contient sondes santé utilisées pour vérifier la disponibilité d’instances de machines virtuelles du pool d’adresses principale.

Pour plus d’informations, voir [Gestionnaire de ressources Azure prend en charge pour l’équilibrage de charge](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configurer votre environnement d’infrastructure du langage commun pour utiliser le Gestionnaire de ressources Azure

Dans cet exemple, nous utilisons les outils de l’infrastructure du langage commun dans une fenêtre de commande PowerShell. Nous n’utilisons pas les applets de commande PowerShell Azure, mais nous utiliser les fonctionnalités de script de PowerShell pour améliorer la lisibilité et les réutiliser.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../../articles/xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.

2. Exécutez la commande **mode config azure** pour passer en mode Gestionnaire de ressources.

        azure config mode arm

    Résultat attendu :

        info:    New mode is arm

3. Se connecter à Azure et obtenir une liste des abonnements.

        azure login

    Entrez vos informations d’identification Azure lorsque vous y êtes invité.

        azure account list

    Sélectionnez l’abonnement que vous voulez utiliser. Prenez note de l’Id de l’abonnement à l’étape suivante.

4. Configurer les variables PowerShell pour une utilisation avec les commandes de l’infrastructure du langage commun.

        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Créer un groupe de ressources, un équilibrage de charge, un réseau virtuel et sous-réseaux

1. Créer un groupe de ressources

        azure group create $rgName $location

2. Créer un programme d’équilibrage de charge

        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName

3. Créer un réseau virtuel (VNet).

        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix

    Créez deux sous-réseaux dans cette VNet.

        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Créer des adresses IP publiques pour le pool frontal

1. Configurer les variables PowerShell

        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"

2. Créer une adresse IP publique le pool frontal d’IP.

        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel

    >[AZURE.IMPORTANT]L’équilibrage de charge utilise le nom de domaine de l’adresse IP publique en tant que son nom complet. Ce nom une modification à partir de déploiement classique, qui utilise le service en nuage comme l’équilibrage de charge nom de domaine complet.
    >Dans cet exemple, le nom de domaine complet est *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Créer des pools frontale et principale

Cet exemple crée le pool frontal IP qui reçoit le trafic réseau entrant sur l’équilibrage de charge et le pool d’IP principale dans laquelle le pool frontal envoie le trafic réseau équilibré de charge.

1. Configurer les variables PowerShell

        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"

2. Créer un pool frontal d’IP associer l’adresse IP publique créé dans l’étape précédente et l’équilibrage de charge.

        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Créer la sonde, NAT et les règles kg

Cet exemple montre comment créer les éléments suivants :

- une règle sonde pour vérifier la connectivité au port TCP 80
- une règle NAT pour traduire tout le trafic entrant sur le port 3389 au port 3389 pour RDP<sup>1</sup>
- une règle NAT pour traduire tout le trafic entrant sur le port 3391 vers Port3389 pour RDP<sup>1</sup>
- une règle d’équilibrage de charge équilibrer tout le trafic entrant sur le port 80 au port 80 sur les adresses dans la liste principale.

<sup>1</sup> les règles NAT sont associés à une instance de machine virtuelle spécifique derrière l’équilibrage de charge. Le trafic réseau arrivant sur le port 3389 est envoyé à la machine virtuelle spécifique et le port associé à la règle NAT. Vous devez spécifier un protocole (UDP ou TCP) pour une règle NAT. Les deux protocoles ne peut pas être affectées au même port.

1. Configurer les variables PowerShell

        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"

2. Créer la sonde

    L’exemple suivant crée une sonde TCP vérifie pour la connectivité au port TCP principale 80 toutes les 15 secondes. Il marque la ressource principale indisponibles après deux échecs consécutifs.

        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName

3. Créer des règles NAT entrants qui autorise les connexions RDP aux ressources principale

        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName

4. Créer un programme d’équilibrage de charge des règles qui acheminer le trafic vers des ports principale différents selon le frontal a reçu la demande

        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName

5. Vérifiez vos paramètres

        azure network lb show --resource-group $rgName --name $lbName

    Résultat attendu :

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show


## <a name="create-nics"></a>Créer des cartes réseau

Créer des cartes réseau et associez-les à sondes, des règles d’équilibrage de charge et des règles NAT.

1. Configurer les variables PowerShell

        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"

2. Créer une carte réseau pour chaque principale et ajouter une configuration IPv6.

        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Créez les ressources machine virtuelle principale, joignez chaque carte réseau

Pour créer des ordinateurs virtuels, vous devez disposer d’un compte de stockage. Pour l’équilibrage de charge, les machines virtuelles doivent être membres d’un jeu de disponibilité. Pour plus d’informations sur la création de machines virtuelles, voir [créer une machine virtuelle Azure à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Configurer les variables PowerShell

        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"

    >[AZURE.WARNING] Cet exemple utilise le nom d’utilisateur et mot de passe pour les ordinateurs virtuels en texte clair. Soins appropriés doivent être prises lors de l’utilisation d’informations d’identification en clair. Pour une méthode plus sécurisée de gestion des informations d’identification dans PowerShell, voir l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

2. Créer le jeu de compte et la disponibilité de stockage

    Vous pouvez utiliser un compte de stockage existant lorsque vous créez les ordinateurs virtuels. La commande suivante crée un nouveau compte de stockage.

        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"

    Ensuite, créez le jeu de disponibilité.

        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location

3. Créer des machines virtuelles avec les cartes réseau associés

        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
