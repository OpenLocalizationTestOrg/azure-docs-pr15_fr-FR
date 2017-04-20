<properties
    pageTitle="Restaurer secret et la clé de l’archivage sécurisé clé pour les machines virtuelles chiffrés à l’aide de sauvegarde Azure | Microsoft Azure"
    description="Découvrez comment restaurer la clé de l’archivage sécurisé touche et le code secret dans sauvegarde Azure à l’aide de PowerShell"
    services="backup"
    documentationCenter=""
    authors="JPallavi"
    manager="vijayts"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="JPallavi" />

# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurer secret et la clé de l’archivage sécurisé clé pour les machines virtuelles chiffrés à l’aide de sauvegarde Azure
Cet article traite de l’utilisation de sauvegardes de machine virtuelle Azure restauration de machines virtuelles Azure chiffré, si votre secret et la clé n’existent pas dans l’archivage sécurisé clé. Ces étapes peuvent également servir si vous voulez conserver une copie distincte de touche (clé de chiffrement) et un code secret (clé de chiffrement BitLocker) pour la machine virtuelle restaurée.

## <a name="pre-requisites"></a>Conditions préalables

1. **Sauvegarde cryptée machines virtuelles** - chiffrées des machines virtuelles Azure ont été sauvegardés à l’aide de sauvegarde Azure. Pour plus d’informations sur la sauvegarde de machines virtuelles Azure chiffré, voir l’article [Gérer sauvegarder et restaurer des machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md) .

2. **Configuration de l’archivage sécurisé clé Azure** – Assurez-vous que l’archivage sécurisé auquel clés et secrets devront être restaurés de clé est déjà présent. Consultez l’article de la [Prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md) pour plus d’informations sur la gestion des clés de l’archivage sécurisé.

## <a name="setup-recovery-services-vault"></a>Configurer l’archivage sécurisé des services de récupération 
Suivez les étapes suivantes pour se connecter au PowerShell et définir le contexte de l’archivage sécurisé de services de récupération

### <a name="log-in-to-azure-powershell"></a>Se connecter au PowerShell Azure 

Connectez-vous au compte Azure à l’aide de l’applet de commande suivante

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Définir le contexte l’archivage sécurisé services récupération

Une fois connecté, utilisez l’applet de commande suivante pour obtenir la liste de vos abonnements disponibles

```
PS C:\> Get-AzureRmSubscription
```

Sélectionnez l’abonnement dans lequel les ressources sont disponibles

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Définir le contexte de l’archivage sécurisé à l’aide de l’archivage sécurisé des Services de récupération où la sauvegarde a été activée pour les machines virtuelles chiffrés

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Récupérer un point de restauration 

Sélectionnez le conteneur dans l’archivage sécurisé qui représente la machine virtuelle Azure cryptée

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

À l’aide de ce conteneur, revenir article pour la machine virtuelle correspondante

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtenir un tableau de points de récupération pour la sauvegarde sélectionnée dans le rp variable

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurer machine virtuelle cryptée
Utilisez les étapes suivantes pour restaurer machine virtuelle chiffré, son secret et la clé.

### <a name="restore-key"></a>Restaurer la clé

Le tableau ci-dessus, $rp est trié dans l’ordre inverse de temps avec le dernier point de récupération à l’index 0. Par exemple : $rp [0] sélectionne le dernier point de récupération.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [AZURE.NOTE]
Une fois que cette applet de commande s’exécute correctement, un fichier blob est généré dans le dossier spécifié sur l’ordinateur sur lequel il s’exécute. Ce fichier blob représente clé chiffrées dans un format chiffré.

Restaurer clé précédent dans l’archivage sécurisé clé à l’aide de l’applet de commande suivante. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurer secret

Restaurer les données confiant de point de récupération obtenu ci-dessus

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [AZURE.NOTE]
Le texte avant vault.azure.net représente le nom de l’archivage sécurisé clé d’origine. Le texte après secrets / représente nom secrète. 

Obtenez le nom secret et la valeur de la sortie de l’applet de commande Exécuter ci-dessus, au cas où vous souhaitez utiliser le même nom secrète. Dans d’autres cas, $secretname ci-dessous doit être mis à jour pour utiliser le nouveau nom secrète. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Définir des balises pour le code secret, au cas où machine virtuelle doit être restauré également. Pour la balise DiskEncryptionKeyFileName, valeur doit contenir le nom du code secret que vous prévoyez d’utiliser. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [AZURE.NOTE]
Valeur de DiskEncryptionKeyFileName est identique au nom confiant obtenu ci-dessus. Valeur pour DiskEncryptionKeyEncryptionKeyURL peut provenir de l’archivage sécurisé clé après restauration des clés de retour et à l’aide d’applet de commande [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)   

Définir l’arrière secrète à l’archivage sécurisé clé

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurer machine virtuelle
Les applets de commande PowerShell ci-dessus vous aident restaurer clé et secrète précédent dans l’archivage sécurisé clé, si vous avez sauvegardé machine virtuelle chiffré à l’aide de sauvegarde de machine virtuelle Azure. Après restauration, consultez l’article [Gérer sauvegarder et restaurer des machines virtuelles Azure à l’aide de PowerShell](backup-azure-vms-automation.md) pour restaurer des machines virtuelles chiffrés.
