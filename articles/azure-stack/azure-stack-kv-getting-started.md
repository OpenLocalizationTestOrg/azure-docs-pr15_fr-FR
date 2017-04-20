<properties
    pageTitle="Prise en main l’archivage sécurisé clés pile Azure | Microsoft Azure"
    description="Commencer à utiliser l’archivage sécurisé Azure pile clé"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Prise en main l’archivage sécurisé clé

Cette section décrit les étapes pour créer un archivage sécurisé, gérer les clés et secrets ainsi qu’autoriser des utilisateurs ou des applications pour les opérations dans l’archivage sécurisé dans Azure pile d’appel. Les étapes suivantes impliquent un abonnement client existe et KeyVault service est enregistré au sein de cet abonnement. Toutes les commandes de l’exemple sont basées sur les applets de commande KeyVault disponibles dans le cadre du Kit de développement PowerShell Azure.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>L’activation de l’abonnement client pour les opérations de l’archivage sécurisé 

Avant de pouvoir émettre opérations contre toute l’archivage sécurisé, vous devez vous assurer que votre abonnement est activé pour les opérations de l’archivage sécurisé. Vous pouvez confirmer qu’à l’aide de la commande PowerShell suivante :

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

Le résultat de la commande ci-dessus signalez « Registered » pour l’état « Inscription » de chaque ligne.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Si qui n’est pas le cas, vous devez appeler la commande suivante pour enregistrer le service KeyVault au sein de votre abonnement :

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

Et comme est le résultat de la commande :
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Si vous rencontrez l’erreur : «*l’abonnement n’est pas enregistré avec l’archivage sécurisé de clé Azure*» lors de l’appel des applets de commande KeyVault, confirmez que vous avez activé le fournisseur de ressources KeyVault par instructions ci-dessus.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Création d’un conteneur renforcé (un archivage sécurisé) dans la pile Azure pour stocker et gérer des clés de chiffrement et secrets

Afin de créer un archivage sécurisé, un client devez d’abord créer un groupe de ressources. Les commandes PowerShell suivantes créent un groupe de ressources, puis un archivage sécurisé dans ce groupe de ressources. L’exemple inclut également les sorties à partir de cette applet de commande.

### <a name="creating-a-resource-group"></a>Création d’un groupe de ressources :

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Résultat :

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Création d’un archivage sécurisé :


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Résultat :

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
Le résultat de cette applet de commande affiche les propriétés de l’archivage sécurisé clé que vous venez de créer. Les deux propriétés les plus importantes sont :

-   **Nom de l’archivage sécurisé**: dans l’exemple, il s’agit de **vault010**. Vous allez utiliser ce nom pour d’autres applets de commande de l’archivage sécurisé clé.

-   **URI de l’archivage sécurisé**: dans l’exemple, il s’agit de https://vault010.vault.azurestack.local. Applications qui utilisent votre l’archivage sécurisé via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer les opérations sur cette archivage sécurisé clé. Personne d’autre n’est encore.


## <a name="operating-on-keys-and-secrets"></a>Fonctionne sur les touches et Secrets

Après avoir créé un archivage sécurisé, suivez les opérations suivantes pour créer, gérer clés et secrets :

### <a name="creating-a-key"></a>Création d’une clé

Afin de créer une clé, utilisez **Ajouter AzureKeyVaultKey** par l’exemple ci-dessous. Après la création de clés réussie, l’applet de commande affiche les informations clé nouvellement créées.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

Voici le résultat de l’applet de commande *Add-AzureKeyVaultKey* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Vous pouvez désormais faire référence à cette clé que vous avez créé ou téléchargé dans l’archivage sécurisé Azure clé, à l’aide de son URI. **Touches/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** permet de toujours télécharger la version actuelle ; et **https://vault010.vault.azurestack.local:443/touches/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** permet d’obtenir cette version spécifique.

### <a name="retrieving-a-key"></a>Récupération d’une clé

Utiliser **Get-AzureKeyVaultKey** pour récupérer une clé et ses détails par l’exemple suivant :

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

Voici le résultat de Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Définir un secret

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Sortie

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Récupération d’un code secret

    Get-AzureKeyVaultSecret -VaultName vault010

Sortie

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Votre clé et l’archivage sécurisé clé ou le secret est désormais prêt pour les applications à utiliser.
Vous devez autoriser les applications à utiliser ces.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisez l’application à utiliser la clé ou le code secret 

Pour autoriser l’application pour accéder à la clé ou secret dans l’archivage sécurisé, utilisez l’applet de commande Set -**AzureRmKeyVaultAccessPolicy** .

Par exemple, si votre nom de l’archivage sécurisé est *ContosoKeyVault* et l’application que vous souhaitez autoriser a un *ID Client* *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*, et que vous voulez autoriser l’application à déchiffrer et connectez-vous avec touches dans votre l’archivage sécurisé, exécutez la commande suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si vous souhaitez autoriser cette application même à lire secrets dans votre l’archivage sécurisé, exécutez la commande suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Étapes suivantes
[Déployer une machine virtuelle avec un mot de passe de l’archivage sécurisé clé](azure-stack-kv-deploy-vm-with-secret.md)

[Déployer une machine virtuelle avec un certificat de l’archivage sécurisé clé](azure-stack-kv-push-secret-into-vm.md)