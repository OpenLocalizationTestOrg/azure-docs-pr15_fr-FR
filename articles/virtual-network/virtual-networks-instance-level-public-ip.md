<properties 
   pageTitle="Instance niveau IP Public (ILPIP) | Microsoft Azure"
   description="Présentation des ILPIP (PIP) et comment les gérer"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Instance niveau public IP vue d’ensemble
Une instance IP public au niveau (ILPIP) est une adresse IP publique que vous pouvez affecter directement à votre instance machine virtuelle ou le rôle, plutôt que du service cloud résidant dans votre instance machine virtuelle ou rôle. Cela ne prend la place de l’adresse IP virtuelle (adresse IP virtuelle) est affectée à votre service cloud. Au lieu de cela, il est une adresse IP supplémentaire que vous pouvez utiliser pour se connecter directement à votre instance machine virtuelle ou rôle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md). 

Assurez-vous que vous savez comment fonctionnent les [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

>[AZURE.NOTE] Par le passé, un ILPIP était appelé un PIP, ce qui signifie IP public. 

![Différence entre ILPIP et VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Comme indiqué dans la Figure 1, le service en nuage est accessible à l’aide d’une adresse VIP, tandis que les ordinateurs virtuels individuels sont normalement accessibles à l’aide de VIP :&lt;numéro de port&gt;. En assignant une ILPIP pour une machine virtuelle spécifique, que machine virtuelle sont accessibles directement à l’aide de cette adresse IP.

Lorsque vous créez un service cloud dans Azure, les enregistrements DNS A correspondants sont créées automatiquement pour autoriser l’accès au service via un nom de domaine complet (FQDN) au lieu d’utiliser l’adresse IP virtuelle réel. La même procédure deviennent ILPIP, ce qui permet d’accéder à l’instance machine virtuelle ou rôle par nom de domaine complet au lieu de la ILPIP. Par exemple, si vous créez un service cloud nommé *contosoadservice*et que vous configurez un rôle web nommé *contosoweb* avec deux instances, Azure enregistrer ce qui suit enregistrements A pour les instances :

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Vous pouvez affecter qu’un seul ILPIP pour chaque instance machine virtuelle ou rôle. Vous pouvez utiliser jusqu'à 5 ILPIP par abonnement. Pour l’instant, ILPIP n’est pas prise en charge pour les machines virtuelles plusieurs cartes.

## <a name="why-should-i-request-an-ilpip"></a>Pourquoi dois-je demander un ILPIP ?
Si vous voulez être en mesure de vous connecter à votre instance machine virtuelle ou rôle par une adresse IP est affectée directement à, plutôt que d’utiliser le cloud service VIP :&lt;numéro de port&gt;, demander une ILPIP pour votre ordinateur virtuel ou votre instance de rôle.
- **FTP passif** - en copiant une ILPIP sur votre ordinateur virtuel, vous pouvez recevoir le trafic pratiquement n’importe quel port, vous n’avez pas à ouvrir un point de terminaison pour recevoir le trafic. Cela permet de scénarios comme FTP passif où les ports sont sélectionnés de façon dynamique.
- **IP sortantes** - le trafic sortant provenant de la machine virtuelle éteint avec la ILPIP comme source et cette chaîne identifie la machine virtuelle à des entités externes.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Comment demander une ILPIP lors de la création de la machine virtuelle
Le script PowerShell ci-dessous crée un service cloud nommé *FTPService*, puis récupère une image à partir d’Azure, crée une machine virtuelle nommée *FTPInstance* à l’aide de l’image récupérée, définit la machine virtuelle pour utiliser un ILPIP et ajoute la machine virtuelle au nouveau service :

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Comment faire pour récupérer des informations ILPIP pour une machine virtuelle
Pour afficher les informations ILPIP pour la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell suivante et observez les valeurs pour *PublicIPAddress* et *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Comment supprimer un ILPIP d’une machine virtuelle
Pour supprimer la ILPIP ajouté à la machine virtuelle dans le script ci-dessus, exécutez la commande PowerShell suivante :
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Comment ajouter un ILPIP à un ordinateur virtuel existant
Pour ajouter un ILPIP de la machine virtuelle créée en utilisant le script ci-dessus, exécutez la commande suivante :

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Comment associer un ILPIP pour une machine virtuelle à l’aide d’un fichier de configuration de service
Vous pouvez également associer un ILPIP pour une machine virtuelle à l’aide d’un fichier de configuration (CSCFG) service. L’exemple xml ci-dessous montre comment configurer un service cloud pour utiliser un ILPIP nommé *MyPublicIP* pour une instance de rôle : 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le fonctionnement de [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) dans le modèle de déploiement classique.

- Découvrez les [adresses IP réservé](virtual-networks-reserved-public-ip.md).
 