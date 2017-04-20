<properties
   pageTitle="Transmission des informations d’identification à Azure à l’aide de DSC | Microsoft Azure"
   description="Vue d’ensemble sur le passage d’informations d’identification en toute sécurité Azure machines virtuelles à l’aide de la Configuration de l’état PowerShell vous le souhaitez"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Transmission des informations d’identification au gestionnaire d’extension DSC Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Cet article décrit l’extension de Configuration de l’état souhaité pour Azure. Vous trouverez une vue d’ensemble du Gestionnaire d’extension DSC à [Introduction au gestionnaire d’extension Configuration de l’état Azure vous le souhaitez](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Transmission des informations d’identification
Dans le cadre du processus de configuration, il est possible que pour configurer des comptes d’utilisateurs, accéder aux services, ou que vous installez un programme dans un contexte utilisateur. Pour effectuer ces opérations, vous devez fournir les informations d’identification. 

DSC permet de configurations paramétrées dans lequel les informations d’identification sont transmises à la configuration et en toute sécurité stockées dans des fichiers MOF. Le Gestionnaire d’Extension Azure simplifie la gestion des informations d’identification grâce à la gestion automatique des certificats. 

Prenons le script de configuration DSC suivant qui crée un compte d’utilisateur local avec le mot de passe :

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Il est important d’inclure le *nœud hôte local* dans le cadre de la configuration. Si cette déclaration est manquante, les étapes suivantes ne fonctionnent pas comme le Gestionnaire d’extension recherche spécifiquement une instruction nœud hôte local. Il est également important d’inclure la conversion de type *[PsCredential]*, comme ce type spécifique déclenche l’extension pour chiffrer les informations d’identification. 

Publier ce script vers le stockage blob :

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Définir l’extension DSC Azure et fournissez les informations d’identification :

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Comment les informations d’identification sont sécurisées
Exécuter ce code vous invite à une information d’identification. Une fois qu’elle est fournie, il est stocké en mémoire brièvement. Lorsqu’elle est publié avec `Set-AzureVmDscExtension` applet de commande, il est transmis via HTTPS de la machine virtuelle, l’emplacement dans lequel Azure qu’il stocke chiffrées sur disque, en utilisant le certificat de machine virtuelle local. Il est brièvement déchiffré en mémoire, puis nouveau chiffrée pour passer à DSC.

Ce comportement est différent de celui [à l’aide de configurations sécurisées sans le Gestionnaire d’extension](https://msdn.microsoft.com/powershell/dsc/securemof). L’environnement Azure permet de transmettre des données de configuration en toute sécurité au moyen de certificats. Lorsque vous utilisez le Gestionnaire d’extension DSC, il est inutile de fournir $CertificatePath ou un $CertificateID / $Thumbprint entrée dans ConfigurationData.


## <a name="next-steps"></a>Étapes suivantes ##

Pour plus d’informations sur le Gestionnaire d’extension DSC Azure, voir [Introduction au gestionnaire d’extension Configuration de l’état Azure vous le souhaitez](virtual-machines-windows-extensions-dsc-overview.md). 

Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension DSC](virtual-machines-windows-extensions-dsc-template.md).

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Pour rechercher des fonctionnalités supplémentaires vous pouvez gérer DSC PowerShell, [parcourez la galerie de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) à d’autres ressources DSC.
