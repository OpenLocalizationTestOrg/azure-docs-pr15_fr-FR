<properties
    pageTitle="Pour les Machines virtuelles SQL Server (Gestionnaire de ressources) des sauvegardes automatiques | Microsoft Azure"
    description="Explication de la fonction de sauvegarde automatique pour SQL Server en cours d’exécution dans Machines virtuelles Azure à l’aide du Gestionnaire de ressources. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Sauvegarde automatique pour SQL Server dans des Machines virtuelles Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-backup.md)
- [Classique](virtual-machines-windows-classic-sql-automated-backup.md)

Effectuer des sauvegardes automatiques configure automatiquement [Des sauvegardes gérées à Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) pour toutes les bases de données existants et nouveaux sur un ordinateur Azure virtuel qui exécute SQL Server 2014 Standard ou entreprise. Cela vous permet de configurer des sauvegardes régulières de la base de données qui utilisent le stockage d’objets blob Azure résistant. Effectuer des sauvegardes automatiques dépendant de l' [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, consultez [Sauvegarde automatique pour SQL Server dans Azure Machines virtuelles classique](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser la sauvegarde automatique, prenez en compte les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server/Édition**:

- SQL Server 2014 Standard
- SQL Server 2014 entreprise

**Configuration de base de données**:

- Bases de données cible doivent utiliser le modèle de récupération complète

**Azure PowerShell**:

- [Installer les dernières commandes PowerShell Azure](../powershell-install-configure.md) si vous envisagez de configurer la sauvegarde automatique avec PowerShell.

>[AZURE.NOTE] Effectuer des sauvegardes automatiques dépend de l’Extension de l’Agent de SQL Server IaaS. Images de la galerie actuelles machine virtuelle SQL ajouter cette extension par défaut. Pour plus d’informations, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour la sauvegarde automatique. Les étapes de configuration varient selon que vous utilisez le portail Azure ou les commandes PowerShell de Windows Azure.

|Paramètre|Plage (par défaut)|Description|
|---|---|---|
|**Effectuer des sauvegardes automatiques**|Activer/désactiver (désactivé)|Active ou désactive la sauvegarde automatique pour une machine virtuelle Azure qui exécute SQL Server 2014 Standard ou entreprise.|
|**Période de rétention**|1-30 jours (30 jours)|Le nombre de jours pour conserver une sauvegarde.|
|**Compte de stockage**|Compte de stockage Azure (le compte de stockage créé pour la machine virtuelle spécifiée)|Un compte de stockage Azure à utiliser pour le stockage de fichiers de sauvegarde automatique dans le stockage blob. Un conteneur est créé à cet emplacement pour stocker tous les fichiers de sauvegarde. La dénomination du fichier de sauvegarde inclut la date, l’heure et nom de l’ordinateur.|
|**Chiffrement**|Activer/désactiver (désactivé)|Active ou désactive le chiffrement. Lorsque le chiffrement est activé, les certificats utilisés pour restaurer la sauvegarde se trouvent sur le compte de stockage spécifié dans le même conteneur automaticbackup à l’aide de la même convention d’appellation. Si le mot de passe est modifiée, un nouveau certificat est généré avec ce mot de passe, mais l’ancien certificat reste pour restaurer des sauvegardes préalables.|
|**Mot de passe**|Texte de mot de passe (aucun)|Un mot de passe pour les clés de chiffrement. Cette option n’est obligatoire si le chiffrement est activé. Afin de restaurer une sauvegarde chiffrée, vous devez le mot de passe correct et le certificat associé ayant servi à la fois que la sauvegarde a été effectuée.|

## <a name="configuration-in-the-portal"></a>Configuration du portail
Vous pouvez utiliser le portail Azure pour configurer la sauvegarde automatique au cours de la mise en service ou pour machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Utiliser le portail Azure pour configurer la sauvegarde automatique lorsque vous créez une nouvelle Machine virtuelle SQL Server 2014 dans le modèle de déploiement du Gestionnaire de ressources.

Dans la carte de **paramètres de SQL Server** , sélectionnez **des sauvegardes automatiques**. La Azure portail capture d’écran suivante montre la carte de **Sauvegarde automatique SQL** .

![Configuration de sauvegarde automatique SQL Azure portail](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Pour le contexte, consultez la rubrique complète sur [une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour des machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Sélectionnez ensuite la section **configuration de SQL Server** de la cuillère **paramètres** .

![Sauvegarde automatique de SQL pour machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Dans la carte de **configuration SQL Server** , cliquez sur le bouton **Modifier** dans la section de sauvegarde automatique.

![Configurer la sauvegarde automatique SQL pour machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** en bas de la cuillère de **configuration SQL Server** pour enregistrer vos modifications.

Si vous activez la sauvegarde automatique pour la première fois, Azure configure l’Agent SQL Server IaaS en arrière-plan. Pendant ce temps, le portail Azure figuriez pas que la sauvegarde automatique est configuré. Patientez quelques minutes pour que l’agent soit installé, configuré. Une fois que le portail Azure refléteront les nouveaux paramètres.

>[AZURE.NOTE] Vous pouvez également configurer à l’aide d’un modèle de sauvegarde automatique. Pour plus d’informations, voir [démarrage rapide Azure modèle pour la sauvegarde automatique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Après la configuration de votre SQL VM, utiliser PowerShell pour configurer la sauvegarde automatique.

Dans l’exemple suivant PowerShell, sauvegarde automatique est configuré pour une existante 2014 machine virtuelle SQL Server. La commande **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** configure les paramètres de sauvegarde automatique pour stocker des sauvegardes dans le compte de stockage Azure associé à la machine virtuelle. Ces sauvegardes sont conservées pendant 10 jours. La commande **Set-AzureRmVMSqlServerExtension** met à jour la machine virtuelle Azure spécifié avec ces paramètres.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Cela peut prendre plusieurs minutes pour installer et configurer l’Agent de SQL Server IaaS.

Pour activer le chiffrement, modifiez le script précédent pour passer le paramètre **EnableEncryption** avec un mot de passe (chaîne sécurisée) pour le paramètre **CertificatePassword** . Le script suivant active les paramètres de sauvegarde automatique dans l’exemple précédent et ajoute le chiffrement.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pour désactiver la sauvegarde automatique, exécutez le script, même sans le **-Activer** paramètre à la commande **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** . L’absence de la **-Activer** paramètre indique la commande pour désactiver la fonctionnalité. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver la sauvegarde automatique.

>[AZURE.NOTE] Suppression de l’Agent SQL Server IaaS ne supprime pas les paramètres de sauvegarde automatique précédemment configurés. Vous devez désactiver la sauvegarde automatique avant la désactivation ou la désinstallation de l’Agent SQL Server IaaS.

## <a name="next-steps"></a>Étapes suivantes

Effectuer des sauvegardes automatiques configure des sauvegardes gérées sur machines virtuelles Azure. Il est donc important pour [consulter la documentation de sauvegardes gérées](https://msdn.microsoft.com/library/dn449496.aspx) à comprendre le comportement et les implications.

Vous pouvez rechercher sauvegarde supplémentaire et restaurer des conseils pour SQL Server sur machines virtuelles Azure dans la rubrique suivante : [sauvegarder et restaurer pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

Pour plus d’informations sur d’autres tâches automation disponibles, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur machines virtuelles Azure, voir [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
