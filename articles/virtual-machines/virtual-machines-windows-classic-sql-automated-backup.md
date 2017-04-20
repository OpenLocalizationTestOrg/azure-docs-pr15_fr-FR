<properties
    pageTitle="Sauvegarde automatique pour les Machines virtuelles SQL Server (standard) | Microsoft Azure"
    description="Explication de la fonction de sauvegarde automatique pour SQL Server en cours d’exécution dans Machines virtuelles Azure à l’aide du Gestionnaire de ressources. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Effectuer des sauvegardes automatiques pour SQL Server dans des Machines virtuelles Azure (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
- [Classique](virtual-machines-windows-classic-sql-automated-backup.md)

Effectuer des sauvegardes automatiques configure automatiquement [Des sauvegardes gérées à Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données existants et nouveaux sur un ordinateur Azure virtuel qui exécute SQL Server 2014 Standard ou entreprise. Cela vous permet de configurer des sauvegardes régulières de la base de données qui utilisent le stockage d’objets blob Azure résistant. Effectuer des sauvegardes automatiques dépendant de l' [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, consultez [Sauvegarde automatique pour SQL Server dans le Gestionnaire de ressources Machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser la sauvegarde automatique, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server/Édition**:

- SQL Server 2014 Standard
- SQL Server 2014 entreprise

>[AZURE.NOTE] SQL Server 2016 n’est pas encore pris en charge pour la sauvegarde automatique.

**Configuration de base de données**:

- Bases de données cible doivent utiliser le modèle de récupération complète.

**Azure PowerShell**:

- [Installer les dernières commandes PowerShell Azure](../powershell-install-configure.md).

**Extension SQL Server IaaS**:

- [Installez l’Extension SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour la sauvegarde automatique. Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

|Paramètre|Plage (par défaut)|Description|
|---|---|---|
|**Effectuer des sauvegardes automatiques**|Activer/désactiver (désactivé)|Active ou désactive la sauvegarde automatique pour une machine virtuelle Azure qui exécute SQL Server 2014 Standard ou entreprise.|
|**Période de rétention**|1-30 jours (30 jours)|Le nombre de jours pour conserver une sauvegarde.|
|**Compte de stockage**|Compte de stockage Azure (le compte de stockage créé pour la machine virtuelle spécifiée)|Un compte de stockage Azure à utiliser pour le stockage de fichiers de sauvegarde automatique dans le stockage blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La dénomination du fichier de sauvegarde inclut la date, l’heure et nom de l’ordinateur.|
|**Chiffrement**|Activer/désactiver (désactivé)|Active ou désactive le chiffrement. Lorsque le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent sur le compte de stockage spécifié dans le même conteneur automaticbackup à l’aide de la même convention d’appellation. Si le mot de passe est modifiée, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat reste pour restaurer des sauvegardes préalables.|
|**Mot de passe**|Texte de mot de passe (aucun)|Un mot de passe pour les clés de chiffrement. Cette option n’est obligatoire si le chiffrement est activé. Afin de restaurer une sauvegarde chiffrée, vous devez le mot de passe correct et le certificat associé ayant servi à la fois que la sauvegarde a été effectuée.|

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Dans l’exemple suivant PowerShell, sauvegarde automatique est configuré pour une existante 2014 machine virtuelle SQL Server. La commande **New-AzureVMSqlServerAutoBackupConfig** configure les paramètres de sauvegarde automatique pour stocker des sauvegardes dans le compte de stockage Azure spécifié par la variable $storageaccount. Ces sauvegardes sont conservées pendant 10 jours. La commande **Set-AzureVMSqlServerExtension** met à jour la machine virtuelle Azure spécifié avec ces paramètres.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Cela peut prendre plusieurs minutes pour installer et configurer l’Agent de SQL Server IaaS.

Pour activer le chiffrement, modifiez le script précédent pour passer le paramètre EnableEncryption avec un mot de passe (chaîne sécurisée) pour le paramètre CertificatePassword. Le script suivant active les paramètres de sauvegarde automatique dans l’exemple précédent et ajoute le chiffrement.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pour désactiver la sauvegarde automatique, exécutez le script, même sans le **-Activer** paramètre pour **Nouveau AzureVMSqlServerAutoBackupConfig**. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver la sauvegarde automatique.

>[AZURE.NOTE] Désactivation et désinstallation de l’Agent SQL Server IaaS ne suppriment pas les paramètres des sauvegardes gérées précédemment configurés. Vous devez désactiver la sauvegarde automatique avant la désactivation ou la désinstallation de l’Agent SQL Server IaaS.

## <a name="next-steps"></a>Étapes suivantes

Effectuer des sauvegardes automatiques configure des sauvegardes gérées sur machines virtuelles Azure. Il est donc important pour [consulter la documentation de sauvegardes gérées](https://msdn.microsoft.com/library/dn449496.aspx) à comprendre le comportement et les implications.

Vous pouvez rechercher sauvegarde supplémentaire et restaurer des conseils pour SQL Server sur machines virtuelles Azure dans la rubrique suivante : [sauvegarder et restaurer pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur d’autres tâches automation disponibles, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur machines virtuelles Azure, voir [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
