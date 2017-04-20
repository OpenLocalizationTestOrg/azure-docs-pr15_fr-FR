<properties
    pageTitle="Modifier l’ID de client de l’archivage sécurisé clés après un abonnement déplacé | Microsoft Azure"
    description="Découvrez comment changer l’ID de client pour un archivage sécurisé clé après qu’un abonnement est déplacé vers un autre client"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modifier un ID de client de l’archivage sécurisé clés après déplacement d’un abonnement
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q : mon abonnement a été déplacé à partir d’un client vers client B. Comment modifier l’ID de client pour mon l’archivage sécurisé clé existant et définir les utilisateurs correctes pour les identités client B ?

Lorsque vous créez un archivage sécurisé clé nouveau dans un abonnement, il est automatiquement lié à l’ID de client Azure Active Directory par défaut pour cet abonnement. Toutes les entrées de stratégie d’accès sont également liées à ce code client. Lorsque vous déplacez votre abonnement Azure à partir d’un client à client B, votre chambres fortes touches existantes sont accessibles par les identités (utilisateurs et applications) dans le client B. Pour résoudre ce problème, vous devez :

- Modifier l’ID de client associé à tous les existants clés chambres fortes dans cet abonnement au client B.
- Supprimer toutes les entrées de stratégie d’accès.
- Ajouter de nouvelles entrées de stratégie d’accès qui sont associées au client B.

Par exemple, si vous avez l’archivage sécurisé clé « myvault » dans un abonnement qui a été déplacé à partir du client A à locataire B, voici comment modifier l’ID de client pour cette archivage sécurisé clé et supprimer les stratégies d’accès ancien.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). ResourceId $vault = Get-AzureRmResource – ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() Set-AzureRmResource - ResourceId $vaultResourceId-propriétés $vault. Propriétés
</pre>

Étant donné que ce archivage sécurisé lors du client A avant le déplacement, la valeur d’origine de **$vault. Properties.TenantId** est un client, tout en **(Get-AzureRmContext). Tenant.TenantId** est client B.

À présent que votre l’archivage sécurisé est associé à l’ID de client correcte et ancien entrées de stratégie d’accès sont supprimées, définissez l’accès nouvelles entrées de stratégie au [Jeu AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions sur l’archivage sécurisé de clé Azure, consultez les [Forums de l’archivage sécurisé Azure clé](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
