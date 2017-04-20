<properties
   pageTitle="Créer une machine virtuelle avec plusieurs cartes réseau"
   description="Découvrez comment créer et configurer des machines virtuelles avec plusieurs cartes réseau"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Créer une machine virtuelle avec plusieurs cartes réseau

Vous pouvez créer des machines virtuelles (machines virtuelles) dans Azure et joindre plusieurs interfaces réseau (cartes réseau) vers chacun de vos ordinateurs virtuels. Multiples NIC est une obligation pour plusieurs appareils virtuel réseau, telles que la livraison des applications et des solutions d’optimisation WAN. Multi NIC propose plusieurs fonctionnalités de gestion du trafic réseau, y compris isolement du trafic entre un premier plan terminer carte réseau et sauvegarder fin cartes réseau, ou la séparation de plan du trafic de données à partir de trafic de gestion du plan.

![Carte réseau à plusieurs pour la machine virtuelle](./media/virtual-networks-multiple-nics/IC757773.png)

La figure ci-dessus montre une machine virtuelle avec trois cartes réseau, que chacune connectée à en choisir un.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- Pour Internet VIP (déploiements classiques) est uniquement prise en charge sur la carte de réseau « par défaut ». Il n'est qu’un seul VIP à l’adresse IP de la carte par défaut.
- Pour l’instant, les adresses Instance niveau Public IP (LPIP) (déploiements classiques) ne peuvent pas être à plusieurs machines virtuelles carte réseau.
- L’ordre des cartes de réseau à l’intérieur de la machine virtuelle est aléatoire et peut être modifié également entre Azure infrastructure mises à jour. Cependant, les adresses IP et l’ethernet correspondant MAC corrige will restent identiques. Par exemple, supposons **qu'eth1** contient les adresses IP 10.1.0.100 et MAC 00-0D-3A-B0-39-0D ; après une mise à jour Azure infrastructure et redémarrez, il peut être remplacé par **Eth2**, mais l’adresse IP et MAC le jumelage restera identique. Lorsqu’un redémarrage est exécutée par le client, l’ordre de carte réseau restera identique.
- L’adresse de chaque carte réseau sur chaque ordinateur virtuel doit se trouver dans un sous-réseau, plusieurs cartes réseau sur un seul ordinateur virtuel peut chacune être affectées adresses qui se trouvent dans le même sous-réseau.
- La taille de la mémoire virtuelle détermine le nombre de cartes réseau que vous pouvez créer pour une machine virtuelle. Référence du [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) et articles de tailles machine virtuelle [Linux](../virtual-machines/virtual-machines-linux-sizes.md) pour déterminer le nombre de cartes réseau chaque taille de la mémoire virtuelle prend en charge. 

## <a name="network-security-groups-nsgs"></a>Groupes de sécurité réseau (NSGs)
Dans un déploiement Gestionnaire de ressources, une carte sur un ordinateur virtuel peut-être être associée avec un réseau sécurité groupe (NSG), y compris les cartes réseau sur un ordinateur virtuel qui contient plusieurs cartes réseau activé. Si une carte réseau reçoit une adresse dans un sous-réseau où la constitue associé à un NSG, puis les règles NSG de sous-réseau également s’appliquent à cette carte réseau. Outre associant sous-réseaux NSGs, vous pouvez également associer une carte réseau avec un NSG.

Si un sous-réseau est associé à un NSG et une carte réseau dans ce sous-réseau est associé à un NSG individuellement, les règles NSG associées sont appliquées dans l' **ordre de flux** en fonction du sens du trafic passé ou disparition la carte réseau :

- **Le trafic entrant** dont la destination est la carte réseau en question est s’étale tout d’abord par le biais du sous-réseau déclencher de règles de NSG du sous-réseau, avant de passer dans la carte réseau, puis déclencher de règles de NSG de la carte réseau.
- **Le trafic sortant** dont la source est la carte réseau en question sort tout d’abord à partir de la carte, déclencher de règles de NSG de la carte réseau, avant passant par le sous-réseau, puis déclencher de règles de NSG du sous-réseau.

En savoir plus sur [Les groupes de sécurité réseau](virtual-networks-nsg.md) et comment ils sont appliqués selon les associations aux sous-réseaux machines virtuelles et des cartes réseau...

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Comment configurer un NIC VM à plusieurs dans un déploiement classique

Les instructions ci-dessous vous permettra de créer un multiple NIC VM contenant 3 cartes réseau : une carte réseau par défaut et deux cartes réseau supplémentaires. Les étapes de configuration créera un ordinateur virtuel qui sera configuré selon le fragment de fichier de configuration du service ci-dessous :

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Vous devez les conditions préalables suivantes avant d’essayer d’exécuter les commandes PowerShell dans l’exemple.

- Un abonnement Azure.
- Un réseau virtuel configuré. Pour plus d’informations sur VNets, voir [Vue d’ensemble du réseau virtuel](virtual-networks-overview.md) .
- La dernière version d’Azure PowerShell téléchargé et installé. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Pour créer une machine virtuelle avec plusieurs cartes réseau, procédez comme suit :

1. Sélectionnez une image de la machine virtuelle à partir de la galerie d’images machine virtuelle Azure. Notez que les images changent fréquemment et sont disponibles par région. L’image spécifiée dans l’exemple ci-dessous modifier ou peut est peut-être pas dans votre région, veillez donc à spécifier l’image que vous avez besoin.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Créer une configuration machine virtuelle.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Créer la connexion de l’administrateur par défaut.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Ajouter des cartes réseau à la configuration de la machine virtuelle.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Spécifier le sous-réseau et l’adresse IP pour la carte par défaut.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Créer la machine virtuelle dans votre réseau virtuel.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] VNet que vous spécifiez ici doit déjà exister (comme indiqué dans les conditions préalables). L’exemple ci-dessous spécifie un réseau virtuel nommé **MultiNIC VNet**.

## <a name="limitations"></a>Limitations

Les limitations suivantes sont applicables lors de l’utilisation de la fonctionnalité de carte réseau à plusieurs :

- Machines virtuelles à plusieurs cartes réseau doivent être créés dans des réseaux virtuels Azure (VNets). Machines virtuelles non VNet ne peut pas être configuré avec plusieurs cartes réseau.
- Tous les ordinateurs virtuels dans un jeu de disponibilité doivent utiliser soit carte unique ou à plusieurs cartes réseau Il ne peut pas être un mélange de machines virtuelles à plusieurs cartes réseau et unique carte réseau machines virtuelles au sein d’un ensemble de disponibilité. Règles applicables identiques pour les machines virtuelles dans un service cloud.
- Une machine virtuelle avec carte réseau unique ne peuvent pas être configurée avec plusieurs cartes réseau (et vice versa) une fois qu’elle est déployée, sans supprimer et recréer.


## <a name="secondary-nics-access-to-other-subnets"></a>Accès cartes réseau secondaire vers d’autres sous-réseaux

Par défaut cartes réseau secondaire ne sera pas configurée avec une passerelle par défaut, en raison de laquelle le flux de trafic sur les cartes réseau secondaires est limité à l’intérieur du même sous-réseau. Si les utilisateurs souhaitent activer cartes réseau secondaire communiquer avec l’extérieur leur propre sous, elles devront ajouter une entrée dans la table de routage pour configurer la passerelle comme décrit ci-dessous.

>[AZURE.NOTE] Machines virtuelles créés avant juillet 2015 peut avoir une passerelle par défaut configurée pour toutes les cartes réseau. La passerelle par défaut pour les cartes réseau secondaire n’est pas supprimée jusqu'à ce que ces machines virtuelles sont redémarrés. Dans les systèmes d’exploitation qui utilisent le modèle de routage hôte faible, par exemple Linux, connectivité Internet peut interrompre si le trafic entrant et sortant utilise différentes cartes réseau.

### <a name="configure-windows-vms"></a>Configurer des machines virtuelles Windows

Supposons que vous avez une machine virtuelle Windows avec deux cartes réseau comme suit :

- Adresse IP NIC principale : 192.168.1.4
- Adresse IP NIC secondaire : 192.168.2.5

La table de routage IPv4 pour cet ordinateur virtuel présente comme suit :

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Notez que l’itinéraire par défaut (0.0.0.0) n’est disponible pour la carte primaire réseau. Vous ne serez pas en mesure d’accéder à l’extérieur du sous de ressources pour la carte réseau secondaire, comme indiqué ci-dessous :

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Pour ajouter un itinéraire par défaut sur la carte réseau secondaire, procédez comme suit :

1. À partir d’une invite de commandes, exécutez la commande suivante pour identifier le numéro d’index de la carte réseau secondaire :

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Avez-vous remarqué la deuxième entrée dans la table, avec un index de 27 (dans cet exemple).
3. À partir de l’invite de commandes, exécutez la commande **itinéraire ajouter** comme illustré ci-dessous. Dans cet exemple, vous spécifiez 192.168.2.1 en tant que la passerelle par défaut pour la carte réseau secondaire :

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Pour tester la connectivité, revenir à l’invite de commandes et essayez de taper un sous-réseau différent de celui de la carte réseau secondaire en tant qu’int affichée é exemple ci-dessous :

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Vous pouvez également consulter votre table de routage pour vérifier l’itinéraire nouvellement ajouté, comme illustré ci-dessous :

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurer des ordinateurs virtuels Linux

Machines virtuelles Linux, étant donné que le comportement par défaut utilise hôte faible routage, il est recommandé que les cartes réseau secondaires sont limités aux flux de trafic uniquement au sein du même sous-réseau. Cependant si certains scénarios de connectivité l’extérieur du sous la demande, les utilisateurs doivent activer basée routage pour vous assurer que le trafic entrant et sortant utilise la même carte réseau.

## <a name="next-steps"></a>Étapes suivantes

- Déployer des [Machines virtuelles MultiNIC dans un scénario de l’application de niveau 2 dans un déploiement du Gestionnaire de ressources](virtual-network-deploy-multinic-arm-template.md).
- Déployer des [Machines virtuelles MultiNIC dans un scénario de l’application de niveau 2 dans un déploiement classique](virtual-network-deploy-multinic-classic-ps.md).
