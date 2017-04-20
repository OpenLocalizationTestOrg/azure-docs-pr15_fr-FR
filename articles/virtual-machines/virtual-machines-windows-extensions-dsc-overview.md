<properties
   pageTitle="Vous le souhaitez état Configuration pour une vue d’ensemble Azure | Microsoft Azure"
   description="Vue d’ensemble d’utilisation le Gestionnaire d’extension Microsoft Azure pour la Configuration de l’état PowerShell vous le souhaitez. Y compris les conditions préalables, architecture, applets de commande..."
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

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduction au gestionnaire d’extension Azure souhaité état Configuration #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

L’Agent de machine virtuelle Azure et Extensions associées font partie de Microsoft Azure Infrastructure Services. Machine virtuelle Extensions sont des composants logiciels qui étendent les fonctionnalités de machine virtuelle et de simplifient diverses opérations de gestion de machine virtuelle. Par exemple, l’extension VMAccess peut être utilisée pour réinitialiser votre mot de passe d’administrateur, ou l’extension de Script personnalisé peut être utilisée pour exécuter un script sur l’ordinateur virtuel.

Cet article présente l’Extension PowerShell souhaité état Configuration (DSC) pour les machines virtuelles Azure dans le cadre du Kit de développement PowerShell Azure. Vous pouvez utiliser les nouvelles applets de commande pour télécharger et appliquer une configuration DSC PowerShell sur un ordinateur virtuel Azure activé avec l’extension DSC PowerShell. Les appels extension DSC PowerShell dans DSC PowerShell pour mettre la configuration DSC reçue sur l’ordinateur virtuel. Cette fonctionnalité est également disponible via le portail d’Azure.

## <a name="prerequisites"></a>Conditions préalables ##
**Ordinateur local** Pour interagir avec l’extension Azure machine virtuelle, vous devez utiliser le portail Azure ou le Kit de développement de PowerShell Azure. 

**Agent d’invité** La machine virtuelle Azure qui sera configuré par la configuration DSC doit être un système d’exploitation qui prend en charge Windows Management Framework (WMF) 4.0 ou 5.0. La liste complète des versions du système d’exploitation pris en charge, consultez l' [Historique des versions DSC Extension](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termes et concepts ##
Ce guide suppose que vous connaissez les concepts suivants :

Configuration - un document de configuration DSC. 

Nœud - une cible pour une configuration DSC. Dans ce document, « nœud » toujours fait référence à une machine virtuelle Azure.

Configuration - un .psd1 du fichier de données contenant des données pour une configuration de l’environnement

## <a name="architectural-overview"></a>Présentation de l’architecture ##

L’extension DSC Azure utilise l’infrastructure d’Azure machine virtuelle Agent doit fournir, arrêter et créer des rapports sur les configurations DSC s’exécutant sur machines virtuelles Azure. L’extension DSC attend un fichier .zip contenant au moins un document de configuration et d’un ensemble de paramètres fourni par le biais du Kit de développement de PowerShell Azure ou via le portail d’Azure.

Lorsque l’extension est appelée pour la première fois, il s’exécute un processus d’installation. Ce processus installe une version de la gestion des Framework WMF (Windows) à l’aide de la logique suivante :

1. Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est considérée. Windows Server 2016 a déjà la dernière version de PowerShell installé.
2. Si la `wmfVersion` propriété est spécifiée, cette version de la WMF est installée, sauf si elle est incompatible avec le système d’exploitation de l’ordinateur.
3. Si aucune `wmfVersion` propriété est spécifiée, la dernière version applicable pour la WMF est installée.

Installation de la WMF nécessite un redémarrage. Après le redémarrage, l’extension télécharge le fichier .zip spécifié dans le `modulesUrl` propriété. Si cet emplacement est dans le stockage blob Azure, un jeton de sa peut être indiqué dans la `sasToken` propriété pour accéder au fichier. Une fois que le .zip est téléchargé et décompressé, la fonction configuration définis dans `configurationFunction` est exécuté pour générer le fichier MOF. L’extension s’exécute puis `Start-DscConfiguration -Force` sur le fichier MOF généré. L’extension capture sortie et écriture dans la source pour le canal état Azure. À ce stade, la PPCM DSC gère la surveillance et correction comme d’habitude. 

## <a name="powershell-cmdlets"></a>Applets de commande PowerShell ##

Applets de commande PowerShell peut être utilisé avec processeur ou ASM du package, publier et surveiller les déploiements extension DSC. Les applets de commande répertoriés sont les modules ASM, mais « Azure » peut être remplacé par « AzureRm » pour utiliser le modèle de processeur. Par exemple, `Publish-AzureVMDscConfiguration` utilise ASM, où `Publish-AzureRmVMDscConfiguration` utilise processeur. 

`Publish-AzureVMDscConfiguration`vous permet d’accéder dans un fichier de configuration, l’analyse des ressources DSC dépendantes et crée un fichier .zip contenant la configuration et les ressources DSC nécessaires pour appliquer la configuration. Il peut également créer le package localement à l’aide du `-ConfigurationArchivePath` paramètre. Dans le cas contraire, elle publie le fichier .zip à Azure blob storage et sécuriser avec un jeton associations de sécurité.

Le fichier .zip créé par cette applet de commande comporte le script de configuration .ps1 à la racine du dossier archive. Ressources ont le dossier module placé dans le dossier d’archivage. 

`Set-AzureVMDscExtension`injecte les paramètres requis par l’extension DSC PowerShell dans un objet de configuration machine virtuelle, qui peut ensuite être appliqué à une machine virtuelle Azure avec `Update-AzureVM`.

`Get-AzureVMDscExtension`extrait l’état d’extension DSC d’un ordinateur virtuel particulier. 

`Get-AzureVMDscExtensionStatus`extrait l’état de la configuration de DSC entrent en vigueur par le Gestionnaire d’extension DSC. Cette action peut être effectuée à un groupe de machines virtuelles ou machine virtuelle unique.

`Remove-AzureVMDscExtension`Supprime le Gestionnaire d’extension d’une machine virtuelle donnée. Cette applet de commande ne **pas** supprimer la configuration, désinstaller la WMF ou modifier les paramètres appliqués sur l’ordinateur virtuel. Il supprime uniquement le Gestionnaire d’extension. 

**Principales différences dans les applets de commande ASM et processeur**

- Applets de commande processeur sont synchrones. Applets de commande ASM sont asynchrones.
- ResourceGroupName VMName, ArchiveStorageAccountName et la Version emplacement sont tous les nouveaux paramètres requis.
- ArchiveResourceGroupName est un paramètre facultatif pour processeur. Vous pouvez spécifier ce paramètre lorsque votre compte de stockage appartient à un groupe de ressources autre que celui où la machine virtuelle est créée.
- ConfigurationArchive est appelé ArchiveBlobName dans processeur
- ContainerName est appelée ArchiveContainerName dans processeur
- StorageEndpointSuffix est appelé ArchiveStorageEndpointSuffix dans processeur
- Le commutateur de mise à jour automatique a été ajouté au processeur pour activer la mise à jour automatique du Gestionnaire d’extension vers la dernière version en tant qu’et lorsqu’elle est disponible. NRemarquez que ce paramètre est susceptible d’avoir redémarré sur l’ordinateur virtuel lorsqu’une nouvelle version de la WMF est disponible. 


## <a name="azure-portal-functionality"></a>Fonctionnalité de portail Azure ##
Accédez à une machine virtuelle classique. Sous Paramètres -> cliquez sur Général « Extensions ». Un nouveau volet est créé. Cliquez sur « Ajouter » et sélectionnez DSC PowerShell.

Le portail requiert une entrée.
**Module de configuration ou un Script**: ce champ est obligatoire. Nécessite un fichier .ps1 contenant un script de configuration, ou un fichier .zip avec un script de configuration .ps1 à la racine et toutes les ressources dépendantes dans des sous-dossiers module du .zip. Il peut être créé avec le `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` applet de commande inclus dans le Kit de développement de PowerShell Azure. Le fichier .zip téléchargé dans votre stockage d’objets blob utilisateur sécurisé par un jeton associations de sécurité. 

**Fichier de PSD1 de données de configuration**: ce champ est facultatif. Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour le sélectionner et téléchargez-le sur votre stockage d’objets blob utilisateur, où il est sécurisé par un jeton associations de sécurité. 
 
**Nom de la Configuration de Module-Qualified**: .ps1 fichiers peuvent contenir plusieurs fonctions de configuration. Entrez le nom de la configuration de script .ps1 suivi d’un «\' et le nom de la fonction de configuration. Par exemple, si votre script .ps1 comporte le nom « configuration.ps1 », et la configuration est « IisInstall », vous devez entrer :`configuration.ps1\IisInstall`

**Arguments de configuration**: si la fonction configuration prend des arguments, entrez-les ici dans le format `argumentName1=value1,argumentName2=value2`. Remarque : ce format est un format différent de celui comment accepte les arguments de configuration via les applets de commande PowerShell ou des modèles de gestionnaire de ressources. 

## <a name="getting-started"></a>Prise en main ##

L’extension DSC Azure prend dans les documents de configuration DSC et Active les sur machines virtuelles Azure. Vous trouverez ci-dessous un exemple simple d’une configuration. Enregistrer localement en tant que « IisInstall.ps1 » :

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Les étapes suivantes placer le script IisInstall.ps1 sur l’ordinateur virtuel spécifié, exécutez la configuration et rapport sur l’état.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Journalisation ##

Les journaux sont placées dans :

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[numéro de Version]

## <a name="next-steps"></a>Étapes suivantes ##

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Pour rechercher des fonctionnalités supplémentaires vous pouvez gérer DSC PowerShell, [parcourez la galerie de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) à d’autres ressources DSC.

Pour plus d’informations sur le passage de paramètres sensibles en configurations, voir [Gérer les informations d’identification en toute sécurité avec le Gestionnaire d’extension DSC](virtual-machines-windows-extensions-dsc-credentials.md).
