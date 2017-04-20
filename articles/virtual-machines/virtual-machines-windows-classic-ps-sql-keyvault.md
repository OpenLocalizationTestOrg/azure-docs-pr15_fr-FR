<properties
    pageTitle="Configurer l’intégration de l’archivage sécurisé clés Azure pour SQL Server sur Azure machines virtuelles (classique)"
    description="Découvrez comment automatiser la configuration de chiffrement de SQL Server pour une utilisation avec l’archivage sécurisé de clé Azure. Cette rubrique explique comment utiliser l’intégration de l’archivage sécurisé clé Azure avec SQL Server machines virtuelles créer dans le modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Configurer l’intégration de l’archivage sécurisé clés Azure pour SQL Server sur Azure machines virtuelles (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
- [Classique](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Vue d’ensemble
Il existe plusieurs fonctionnalités de chiffrement de SQL Server, telles que [le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [chiffrement de niveau de colonne (Effacer)](https://msdn.microsoft.com/library/ms173744.aspx)et le [chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces formes de chiffrement nécessitent vous permettent de gérer et stocker les clés de chiffrement utilisé pour le chiffrement. Le service Azure clé l’archivage sécurisé (AKV) est conçu pour améliorer la sécurité et la gestion de ces touches dans un emplacement sécurisé et hautement disponible. Le [Connecteur SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server à utiliser ces raccourcis clavier de l’archivage sécurisé de clé Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Si vous exécutez SQL Server avec les ordinateurs locaux, il existe des [étapes à suivre pour accéder à l’archivage sécurisé de clé Azure à partir de votre ordinateur SQL Server locale](https://msdn.microsoft.com/library/dn198405.aspx). Mais pour SQL Server dans Azure machines virtuelles, vous pouvez gagner du temps en utilisant la fonctionnalité *d’Intégration de l’archivage sécurisé Azure clé* . Avec quelques Azure cmdlets pour activer cette fonctionnalité, vous pouvez automatiser la configuration nécessaire pour une VM SQL accéder à votre l’archivage sécurisé clé.

Lorsque cette fonctionnalité est activée, il automatiquement installe le connecteur SQL Server, configure le fournisseur EKM pour accéder à l’archivage sécurisé de clé Azure et crée les informations d’identification pour vous permettre d’accéder à votre l’archivage sécurisé. Si vous avez examiné les étapes décrites dans la documentation mentionné précédemment en local, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. Vous devez toujours faire manuellement rendues consiste à créer les touches et l’archivage sécurisé clé. À partir de là, la configuration de votre SQL VM entière est automatique. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter les instructions T-SQL pour commencer le chiffrement de vos bases de données ou des sauvegardes comme d’habitude.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurer l’intégration AKV
Utiliser PowerShell pour configurer l’intégration de l’archivage sécurisé Azure clé. Les sections suivantes fournissent une vue d’ensemble des paramètres requis, puis sur un exemple de script PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Installez l’Extension SQL Server IaaS

Tout d’abord, [installez l’Extension SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Comprendre les paramètres d’entrée
Le tableau suivant répertorie les paramètres requis pour exécuter le script PowerShell dans la section suivante.

|Paramètre|Description|Exemple|
|---|---|---|
|**$akvURL**|**L’URL de l’archivage sécurisé clés**|« https://contosokeyvault.vault.azure.net/ »|
|**$spName**|**Nom Principal de service**|« 5-4e11-af04eb07b669ccf2 fde2b411 - 33d »|
|**$spSecret**|**Code secret Principal du service**|« 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM = »|
|**$credName**|**Nom d’identification**: intégration AKV crée une information d’identification dans SQL Server, ce qui permet de la machine virtuelle à accéder à l’archivage sécurisé clé. Choisissez un nom pour cette information d’identification.|« mycred1 »|
|**$vmName**|**Nom de la machine virtuelle**: le nom d’un VM SQL créé précédemment.|« myvmname »|
|**$serviceName**|**Nom du service**: nom du Service Cloud associé à la VM SQL.|« mycloudservicename »|

### <a name="enable-akv-integration-with-powershell"></a>Activer l’intégration AKV avec PowerShell
L’applet de commande **New-AzureVMSqlServerKeyVaultCredentialConfig** crée un objet de configuration de la fonctionnalité d’intégration de l’archivage sécurisé Azure clé. **Jeu AzureVMSqlServerExtension** configure cette intégration avec le paramètre **KeyVaultCredentialSettings** . Les étapes suivantes montrent comment utiliser ces commandes.

1. Dans Azure PowerShell, configurez tout d’abord les paramètres d’entrée avec vos valeurs spécifiques comme décrit dans les sections précédentes de cette rubrique. Le script suivant est un exemple.

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  Puis utilisez le script suivant pour configurer et activer l’intégration AKV.

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L’Extension de l’Agent SQL IaaS met à jour la VM SQL avec cette nouvelle configuration.

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
