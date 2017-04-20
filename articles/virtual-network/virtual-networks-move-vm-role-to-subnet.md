<properties 
   pageTitle="Comment faire pour déplacer une instance de machine virtuelle ou rôle à en choisir un"
   description="Découvrez comment déplacer des machines virtuelles et des instances de rôle à en choisir un"
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

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Comment faire pour déplacer une instance de machine virtuelle ou rôle à en choisir un

Vous pouvez utiliser PowerShell pour déplacer vos ordinateurs virtuels d’un sous-réseau à un autre dans le même réseau virtuel (VNet). Instances de rôle peuvent être déplacés par le CSCFG d’édition, plutôt que de l’utilisation de PowerShell.

>[AZURE.NOTE] Cet article contient des informations qui sont relatif Azure déploiements classiques uniquement.

Pourquoi déplacer des machines virtuelles à un autre sous-réseau ? Migration de sous-réseau est utile lorsque le sous-réseau plus ancien est trop petit et ne peut pas être développé en raison des machines virtuelles en cours d’exécution existantes dans ce sous-réseau. Dans ce cas, vous pouvez créer un nouveau, plus grande sous-réseau et migrer les ordinateurs virtuels dans le nouveau sous-réseau, puis une fois la migration terminée, vous pouvez supprimer l’ancien sous-réseau vide.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Comment déplacer un ordinateur virtuel vers un autre sous-réseau

Pour déplacer une machine virtuelle, exécutez l’applet de commande PowerShell Set-AzureSubnet, à l’aide de l’exemple ci-dessous, en tant que modèle. Dans l’exemple ci-dessous, nous fournissons TestVM à partir de son sous-réseau présenter, sous-réseau 2. Veillez à modifier l’exemple pour refléter votre environnement. Notez que chaque fois que vous exécutez l’applet de commande de mise à jour AzureVM dans le cadre d’une procédure, il redémarrer votre ordinateur virtuel dans le cadre du processus de mise à jour.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Si vous spécifiez une adresse IP privée interne statique pour votre machine virtuelle, vous devrez désactivez ce paramètre avant de pouvoir déplacer la machine virtuelle sur un nouveau sous-réseau. Dans ce cas, utiliser le des opérations suivantes :

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Pour déplacer une instance de rôle à un autre sous-réseau

Pour déplacer une instance de rôle, modifiez le fichier CSCFG. Dans l’exemple ci-dessous, nous fournissons « Role0 » dans le réseau virtuel *VNETName* à partir de son sous-réseau présenter *sous-réseau 2*. Étant donné que l’instance de rôle a déjà été déployé, vous allez simplement modifier le nom de sous-réseau = sous-réseau 2. Veillez à modifier l’exemple pour refléter votre environnement.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
