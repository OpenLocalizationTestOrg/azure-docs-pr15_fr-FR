<properties
   pageTitle="Plusieurs adresses IP pour machines virtuelles - PowerShell | Microsoft Azure"
   description="Découvrez comment attribuer plusieurs adresses IP pour une machine virtuelle à l’aide de PowerShell Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Affecter plusieurs adresses IP aux machines virtuelles

Une Machine virtuelle Azure (machine virtuelle) peut avoir une ou plusieurs interfaces réseau (carte réseau) est jointes à. Une carte peut avoir une ou plusieurs publics ou privés adresses IP est affectées à. Si vous n’êtes pas familiarisé avec des adresses IP dans Azure, consultez l’article [adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md) pour en savoir plus sur les. Cet article explique comment utiliser PowerShell Azure pour affecter plusieurs adresses IP pour une machine virtuelle dans le modèle de déploiement d’Azure le Gestionnaire de ressources.

Attribution de plusieurs adresses IP pour une machine virtuelle active les fonctionnalités suivantes :

- Hébergement de plusieurs sites Web ou services avec différentes adresses IP, les certificats SSL sur un serveur unique.
- Servir un matériel virtuel réseau, par exemple un pare-feu ou l’équilibrage de charge.
- La fonctionnalité permettant d’ajouter des adresses IP privées pour une des cartes réseau à un pool d’équilibrage de charge Azure principale. Par le passé, uniquement l’adresse IP principale pour la carte réseau principale peut être ajoutée à une liste principale.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Pour vous inscrire pour l’aperçu, envoyer un message électronique à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID d’abonnement et les utilisations.

## <a name="scenario"></a>Scénario

Dans cet article, vous allez associer trois configurations IP à une interface réseau.
Les configurations d’exemple suivantes sont créées et affectées à une carte réseau qui aura trois adresses IP privées et une adresse IP publique est affectée à :

- IPConfig-1 : Une adresse IP privée dynamique (par défaut) et un public d’adresses IP à partir de la ressource d’adresse IP publique nommée PIP1.
- IPConfig-2 : Une adresse IP privée et aucune adresse IP publique.
- IPConfig-3 : Une adresse IP privée dynamique et aucune adresse IP publique.

    ![Texte de remplacement d’image](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Ce scénario part du principe que vous avez un groupe de ressources appelé *RG1* dans lequel il y a un VNet appelé *VNet1* et un sous-réseau appelé *Subnet1*. Par ailleurs, il suppose que vous avez une machine virtuelle appelée *VM1*, une interface réseau appelée *VM1 NIC1* associé à et une adresse IP publique nommée *PIP1*.

[Cet article](./virtual-machines/virtual-machines-windows-ps-create.md ) décrit comment créer les ressources présentés ci-dessus au cas où vous n’avez pas créé auparavant.

## <a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

1. Ouvrez une invite de commande PowerShell et effectuez les étapes restantes dans cette section en une seule session PowerShell. Si vous n’avez pas installé et configuré de PowerShell, effectuez les étapes décrites dans l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Modifier les valeurs « » de la $Variables suivants à [l’emplacement](https://azure.microsoft.com/regions) d' Azure que votre réseau virtuel est, le nom de votre [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), la VNet dans le groupe de ressources, le sous-réseau que vous voulez vous connecter la carte réseau à et le nom de la carte réseau. Suivez les étapes pour ajouter plusieurs adresses IP à n’importe quel carte réseau connectée à un ordinateur virtuel, que vous le souhaitez.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Si vous ne connaissez pas le nom d’un emplacement Azure existant ou un groupe de ressources, tapez les commandes suivantes :

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>La carte réseau doit être connectée à un sous-réseau au sein d’un réseau virtuel Azure existant (VNet). Les trois composants : carte réseau, sous-réseau et VNet, doivent tous exister dans la même région et [l’abonnement](../azure-glossary-cloud-terminology.md#subscription).  Si vous ne connaissez pas VNets, consultez l’article [vue d’ensemble du réseau virtuel](virtual-networks-overview.md) pour en savoir plus sur les ou consultez l’article [créer une VNet](virtual-networks-create-vnet-arm-ps.md) pour découvrir comment en créer un.

    Si vous ne connaissez pas le nom d’un VNet existant, entrez la commande suivante et remplacez *VNet1* dans la variable précédente par le nom d’un VNet :

        Get-AzureRmVirtualNetwork | Format-Table Name

    Si la liste retournée est vide, vous devez créer un VNet. Pour en savoir plus, voir l’article [créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) .

    Tapez les commandes suivantes pour obtenir le nom des sous-réseaux existants au sein de la VNet et remplacez *Subnet1* ci-dessus par le nom d’un sous-réseau :

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Entrez la commande suivante pour récupérer le sous-réseau et affectez-le à une variable.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Définir les configurations IP que vous souhaitez affecter à la carte réseau. Chaque configuration peut avoir une statique ou dynamique adresse IP privée et une associé public ressource d’adresse IP à une adresse statique ou dynamique.

    Ajouter ou supprimer un certain nombre de configurations qui suivent selon le nombre d’adresses IP que vous voulez associer à la carte réseau et les paramètres que vous voulez configurer.

    **IPConfig-1**

    Définissez la valeur *PIP1* sur le nom de ressource adresse IP existant public qui existe dans l’emplacement que vous créez la carte réseau d’et qui n’est pas associé à une autre carte réseau. Modification *RG1* le nom du groupe de ressources la ressource d’adresse IP public existe dans. Remplacez le nom que vous voulez donner à la première configuration IP *IPConfig-1* . Entrez les commandes suivantes :

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Remarque la *-principal* basculer. Lorsque vous affectez plusieurs configurations IP à une carte réseau, doit être affectée à une configuration comme le *principal*. Si vous ne connaissez pas le nom d’une ressource d’adresse IP public existant, entrez la commande suivante : Get-AzureRMPublicIPAddress | Nom de la Table de format, emplacement, adresse IP, IPFichier

    Si la colonne **IPFichier** a aucune valeur dans le résultat retourné, la ressource d’adresse IP publique n’est pas associée à une carte existante et peut être utilisée. Si la liste est vide, ou aucune ressource d’adresse IP public disponible, vous pouvez créer un à l’aide de la commande **New-AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Adresses IP publiques incluent des frais nominal. Pour en savoir plus sur les tarifs d’adresse IP, lisez la page [tarifs d’adresse IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Remplacez le nom que vous souhaitez attribuer à la configuration IP deuxième et modifier *10.0.0.5* à une adresse IP inutilisée valide pour le sous-réseau à que vous affectez la carte réseau *IPConfig-2* . Entrez les commandes suivantes :

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Entrez la commande suivante, si vous ne connaissez pas l’adresse IP plage affectée au sous-réseau d’adresses :

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Modifier *IPConfig-3* le nom que vous souhaitez attribuer à la configuration d’IP tiers et entrez les commandes suivantes :

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Vous pouvez affecter jusqu'à 250 adresse IP privée à une carte réseau. Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisés dans un abonnement. Pour plus d’informations, consultez l’article [Azure limite](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Créer la carte réseau en utilisant les configurations IP définies dans l’étape précédente.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Joindre la carte réseau lors de la création d’une machine virtuelle en suivant les étapes décrites dans l’article [créer une machine virtuelle](../virtual-machines/virtual-machines-windows-ps-create.md) . Bien que l’article crée un ordinateur exécutant Windows Server, les étapes sont les mêmes pour un VM Linux, autres que la sélection d’un autre système d’exploitation. Effectuez les étapes 1 à 3 de l’article. Ignorez les étapes 4 et 5 et puis exécuté l’étape 6 de la créer un article de la machine virtuelle.

    >[AZURE.WARNING] Étape 6 de la créer un article de la machine virtuelle échouera si vous avez modifié la variable nommée $nic par une autre à l’étape 6 de cet article, ou que vous n’avez pas terminé les étapes précédentes de cet article.

8. Afficher l’adresse IP privé adresses que DHCP Azure affecté à la carte réseau et la ressource d’adresse IP publique affectée à la carte réseau en entrant la commande suivante :

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Ajouter manuellement toutes les adresses IP privées (y compris le principal) à la configuration TCP/IP dans le système d’exploitation. 

**Windows**

1. À partir d’une invite de commandes, tapez *ipconfig/all*.  Vous voyez uniquement l’adresse IP privée *principale* (via DHCP).
2. Ensuite, tapez *ncpa.cpl* dans la fenêtre d’invite de commandes. Une nouvelle fenêtre s’ouvre.
3. Ouvrez les propriétés de **Connexion au réseau Local**.
4. Double-cliquez sur protocole Internet version 4 (IPv4)
5. Sélectionnez **utiliser l’adresse IP suivante** , entrez les valeurs suivantes :
    - **Adresse IP**: entrez l’adresse IP privée *principale*
    - **Masque**: jeu basé sur votre sous-réseau. Par exemple, si constitue un /24 constitue puis masque 255.255.255.0.
    - **Passerelle par défaut**: la première adresse IP du sous-réseau. Si votre constitue 10.0.0.0/24, l’adresse IP de passerelle est 10.0.0.1.
    - Cliquez sur **utiliser l’adresse de serveur DNS suivante** et entrez les valeurs suivantes :
        - **Serveur DNS préféré :** Entrez 168.63.129.16 si vous n’utilisez pas de votre serveur DNS.  Si vous êtes, entrez l’adresse IP de votre serveur DNS.
    - Cliquez sur le bouton **Avancé** et ajoutez des adresses IP supplémentaires. Ajoutez les adresses IP privé secondaire indiquée à l’étape 8 et la carte réseau avec le même sous-réseau spécifié pour l’adresse IP principale.
    - Cliquez sur **OK** pour fermer les paramètres TCP/IP, puis sur **OK** pour fermer les paramètres de la carte. Ceci permet de puis rétablir votre connexion RDP.

6. À partir d’une invite de commandes, tapez *ipconfig/all*. Vous avez ajouté toutes les adresses IP sont présentées et DHCP est désactivé.


**Linux (Ubuntu)**

1. Ouvrez une fenêtre de terminal.
2. Vérifiez que vous êtes l’utilisateur racine. Si vous n’êtes pas le cas, vous pouvez procédez comme suit à l’aide de la commande suivante :

            sudo -i
3. Mettre à jour le fichier de configuration de l’interface de réseau (en supposant que « eth0 »).
    - Conserver la ligne de facturation existante pour dhcp. Cela configurera l’adresse IP principale tel qu’il est utilisé pour être antérieure.
    - Ajouter une configuration pour une adresse IP statique supplémentaire avec les commandes suivantes :

                cd /etc/network/interfaces.d/
                ls

        Vous devriez voir un fichier .cfg.
4. Ouvrez le fichier : vi *nom de fichier*.

    Vous devriez voir les lignes suivantes à la fin du fichier :

            auto eth0
            iface eth0 inet dhcp
5. Ajoutez les lignes suivantes après les lignes qui existent dans ce fichier :

            iface eth0 inet static
            address <your private IP address here>
6. Enregistrez le fichier à l’aide de la commande suivante :

            :wq
7.  Réinitialiser l’interface réseau avec la commande suivante :

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Exécuter ifdown et ifup dans la même ligne si vous utilisez une connexion à distance.
8. Vérifier que l’adresse IP est ajoutée à l’interface réseau avec la commande suivante :

            ip addr list eth0

    Vous devez voir l’adresse IP que vous avez ajouté dans le cadre de la liste.

**Linux (Redhat, CentOS et autres)**

1. Ouvrez une fenêtre de terminal.
2. Vérifiez que vous êtes l’utilisateur racine. Si vous n’êtes pas le cas, vous pouvez procédez comme suit à l’aide de la commande suivante :

            sudo -i
3. Entrez votre mot de passe, puis suivez les instructions à l’invite. Une fois que vous êtes l’utilisateur racine, accédez au dossier scripts réseau avec la commande suivante :

            cd /etc/sysconfig/network-scripts
4. Répertorier les fichiers ifcfg associées à l’aide de la commande suivante :

            ls ifcfg-*

    Vous devriez voir *ifcfg eth0* dans l’un des fichiers.
5. Copiez le fichier *ifcfg eth0* et nommez-la *ifcfg-eth0:0* avec la commande suivante :

            cp ifcfg-eth0 ifcfg-eth0:0
6. Modifier la *ifcfg-eth0:0* fichier avec la commande suivante :

            vi ifcfg-eth1
7. Modifier le périphérique pour le nom approprié dans le fichier ; *eth0:0* dans ce cas, avec la commande suivante :

            DEVICE=eth0:0
8. Modifier la *IPADDR = YourPrivateIPAddress* ligne afin de refléter l’adresse IP.
9. Enregistrez le fichier avec la commande suivante :

            :wq
10. Redémarrez les services de réseau et vérifiez que les modifications sont réussies en exécutant les commandes suivantes :

            /etc/init.d/network restart
            Ipconfig

    Vous devez voir l’adresse IP que vous avez ajouté, *eth0:0*, dans la liste renvoyée.

## <a name="add"></a>Ajouter des adresses IP pour une machine virtuelle existante

Procédez comme suit pour ajouter des adresses IP supplémentaires à une carte existante :

1. Ouvrez une invite de commande PowerShell et effectuez les étapes restantes dans cette section en une seule session PowerShell. Si vous n’avez pas installé et configuré de PowerShell, effectuez les étapes décrites dans l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Modifier les valeurs « » de la $Variables suivant le nom de la carte réseau que vous souhaitez ajouter des adresses IP et le groupe de ressources et l’emplacement de dans que la carte réseau existe :

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Si vous ne connaissez pas le nom de la carte réseau que vous voulez modifier, entrez les commandes suivantes, puis modifiez les valeurs des variables précédents :

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Créer une variable et le configurer pour la carte réseau existante en tapant la commande suivante :

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Extraire l’ID de sous-réseau la carte réseau est connectée à en procédant [l’étape 3](#subnet) de créer une machine virtuelle avec plusieurs section adresses IP de cet article.

5. Créer les configurations IP que vous voulez ajouter au réseau en suivant les instructions à [l’étape 5](#ipconfigs) de créer une machine virtuelle avec plusieurs section adresses IP de cet article.

6. Modifiez *$IPConfigName4* le nom de la configuration d’IP que vous avez créé à l’étape précédente. Pour ajouter la configuration, entrez la commande suivante :

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Pour définir la carte réseau avec la configuration IP, entrez la commande suivante :

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Ajoutez les adresses IP que vous avez ajouté à la carte réseau pour le système d’exploitation machine virtuelle en suivant les instructions à [l’étape 9](#os) de créer une machine virtuelle avec plusieurs section adresses IP de cet article.
