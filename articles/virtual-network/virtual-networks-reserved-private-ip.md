<properties 
   pageTitle="Comment définir une adresse IP privée interne statique"
   description="Présentation des adresses IP statique (DIP) internes et comment les gérer"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Comment définir une interne privée adresse IP à l’aide de PowerShell (classique)
Dans la plupart des cas, vous n’aurez pas besoin spécifier une adresse IP statique interne de votre machine virtuelle. Machines virtuelles dans un réseau virtuel seront voit attribuer une adresse IP interne à partir d’une plage que vous spécifiez. Mais dans certains cas, spécifiant une adresse IP statique pour une machine virtuelle particulier a du sens. Par exemple, si votre ordinateur virtuel exécutera DNS ou être un contrôleur de domaine. Une adresse IP interne statique reste avec la machine virtuelle même par le biais d’un état arrêt/annulation. 

> [AZURE.IMPORTANT] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article décrit l’utilisation du modèle de déploiement classique. Microsoft recommande la plupart des nouveaux déploiements d’utiliser le [modèle de déploiement Gestionnaire de ressources](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Comment vérifier si une adresse IP spécifique est disponible
Pour vérifier si la d’adresse IP *10.0.0.7* est disponible dans un vnet nommé *TestVnet*, exécutez la commande PowerShell suivante et vérifiez que la valeur pour *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Si vous souhaitez tester la commande ci-dessus dans un environnement approuvés, procédez comme les instructions de [créer un réseau virtuel](virtual-networks-create-vnet-classic-portal.md) pour créer un vnet nommé *TestVnet* et vérifiez qu’il utilise l’espace d’adressage *10.0.0.0/8* .

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Comment spécifier une adresse IP interne statique lorsque vous créez une machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *TestService*, puis récupère une image à partir d’Azure, puis crée une machine virtuelle nommée *TestVM* dans le nouveau service cloud à l’aide de l’image récupérée, définit la machine virtuelle se trouver dans un sous-réseau nommé *sous-réseau-1*et définit *10.0.0.7* sous la forme d’une adresse IP interne statique de la machine virtuelle :

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Comment récupérer des informations IP internes statiques pour une machine virtuelle
Pour afficher les informations IP internes statiques de la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell suivante et observez les valeurs pour *adresse IP*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Comment supprimer une adresse IP interne statique d’une machine virtuelle
Pour supprimer l’adresse IP statique interne ajouté à la machine virtuelle dans le script ci-dessus, exécutez la commande PowerShell suivante :
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Comment ajouter une adresse IP interne statique à un ordinateur virtuel existant
Pour ajouter un mappage statique interne IP de la machine virtuelle créé en utilisant le script ci-dessus, exécutez la commande suivante :

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

[IP réservée](virtual-networks-reserved-public-ip.md)

[IP Public au niveau de l’instance (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST de IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
