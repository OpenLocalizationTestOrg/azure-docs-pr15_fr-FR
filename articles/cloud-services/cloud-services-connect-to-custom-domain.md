<properties
  pageTitle="Connecter un Service Cloud à un contrôleur de domaine personnalisé | Microsoft Azure"
  description="Découvrez comment connecter vos rôles web/travail à un domaine AD personnalisé à l’aide de PowerShell et l’Extension de domaine Active Directory"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Rôles des Services Cloud Azure qui se connecte à un contrôleur de domaine Active Directory hébergé dans Azure personnalisé

Tout d’abord, nous allons définir un réseau virtuel (VNet) dans Azure. Nous allons ensuite ajouter un contrôleur de domaine Active Directory (hébergé sur un ordinateur virtuel Azure) à la VNet. Ensuite, nous ajouter des rôles de service cloud existant à le VNet prédéfini et les connecter par la suite au contrôleur de domaine.

Avant de commencer, dans le cadre d’éléments à garder à l’esprit :

1.  Ce didacticiel utilise PowerShell, veuillez Vérifiez que vous disposez de PowerShell Azure installé et prêt à être envoyé. Pour obtenir de l’aide avec la configuration d’Azure PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

2.  Vos instances de contrôleur de domaine Active Directory et le rôle de collaborateur/Web doivent être dans la VNet.

Suivez ce guide étape par étape et si vous rencontrez des problèmes, nous laisser un commentaire ci-dessous. Une personne obtiendrez vous (Oui, nous lire des commentaires).

3. Le réseau qui est référencé par le cloud service <mark>doit être</mark> un **réseau virtuel classique**.

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Vous pouvez créer un réseau virtuel dans Azure à l’aide du portail classique Azure ou PowerShell. Pour ce didacticiel, nous allons utiliser PowerShell. Pour créer un réseau virtuel à l’aide du portail classique Azure, voir [Créer un réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Créer une Machine virtuelle

Une fois que vous avez terminé la configuration du réseau virtuel, vous avez besoin créer un contrôleur de domaine Active Directory. Pour ce didacticiel, nous définirez un contrôleur de domaine Active Directory sur une Machine virtuelle Azure.

Pour ce faire, créez une machine virtuelle via PowerShell en utilisant les commandes ci-dessous :

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promouvoir votre Machine virtuelle à un contrôleur de domaine
Pour configurer la Machine virtuelle comme un contrôleur de domaine Active Directory, vous avez besoin pour vous connecter à la machine virtuelle et configurez-le.

Pour vous connecter à la machine virtuelle, vous pouvez obtenir le fichier RDP via PowerShell, utilisez les commandes ci-dessous.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Une fois que vous êtes connecté à la machine virtuelle, configurer votre Machine virtuelle comme un contrôleur de domaine Active Directory en suivant le guide étape par étape comment [configurer votre client contrôleur de domaine Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Ajouter votre Service Cloud au réseau virtuel

Ensuite, vous devez ajouter votre déploiement de service cloud à le VNet que vous venez de créer. Pour ce faire, modifiez votre cscfg service cloud en ajoutant des sections correspondantes dans votre cscfg à l’aide de Visual Studio ou l’éditeur de votre choix.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Cliquez ensuite générer votre projet de services cloud et déployer sur Azure. Pour obtenir de l’aide déployer votre package de services cloud Azure, Découvrez [comment créer et déployer un Service Cloud](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Connecter votre rôle (s) web/travail au domaine

Une fois que votre projet de service cloud est déployé sur Azure, connecter vos instances de rôles au domaine AD personnalisé à l’aide de l’Extension de domaine Active Directory. Pour ajouter l’Extension de domaine Active Directory à votre déploiement de services de cloud existant et joindre le domaine personnalisé, exécutez les commandes suivantes dans PowerShell :

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Et voilà.

Cloud services doivent maintenant être joints à votre contrôleur de domaine personnalisé. Si vous voulez en savoir plus sur les différentes options disponibles pour la configuration d’Extension de domaine Active Directory, consultez l’aide de PowerShell comme illustré ci-dessous.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
