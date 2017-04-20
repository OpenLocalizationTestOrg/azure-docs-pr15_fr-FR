<properties
    pageTitle="Accorder aux utilisateurs aux stratégies de laboratoire spécifique | Microsoft Azure"
    description="Apprenez à accorder aux utilisateurs aux stratégies de laboratoire spécifique dans ateliers DevTest en fonction des besoins de chaque utilisateur"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Accorder des autorisations utilisateur aux stratégies de laboratoire spécifique

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment utiliser PowerShell pour accorder aux utilisateurs à une stratégie d’atelier particulier. De cette façon, autorisations peuvent être appliquées en fonction des besoins de chaque utilisateur. Par exemple, vous souhaiterez peut-être accorder à un utilisateur particulier permet de modifier les paramètres de stratégie machine virtuelle, mais pas les stratégies de coût.

## <a name="policies-as-resources"></a>Stratégies comme des ressources

Comme indiqué dans l’article de [Contrôle d’accès basé sur rôle Azure](../active-directory/role-based-access-control-configure.md) , RBAC permet de gestion des accès permissions des ressources pour Azure. L’utilisation de RBAC, vous pouvez séparer les droits au sein de votre équipe DevOps et accorder uniquement la quantité d’accès aux utilisateurs dont ils ont besoin pour effectuer leur travail.

Dans les ateliers DevTest, une stratégie est un type de ressource qui permet à l’action RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Chaque atelier stratégie est une ressource dans le type de ressource de stratégie et peut être affectée à un rôle RBAC l’étendue.

Par exemple, pour accorder aux utilisateurs en lecture/écriture autorisé à la stratégie de **Tailles machine virtuelle autorisées** , vous devez créer un rôle personnalisé qui fonctionne avec la **Microsoft.DevTestLab/labs/policySets/policies/** * action, puis affecter les utilisateurs appropriés à ce rôle personnalisé dans l’étendue de * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Pour en savoir plus sur les rôles personnalisés dans RBAC, consultez le [contrôle d’accès rôles personnalisé](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Création d’un rôle personnalisé atelier à l’aide de PowerShell
Pour commencer, vous devez lire l’article suivant qui explique comment installer et configurer les applets de commande PowerShell Azure : [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Une fois que vous avez configuré les applets de commande PowerShell Azure, vous pouvez effectuer les tâches suivantes :

- Liste de toutes les opérations/actions pour un fournisseur de ressources
- Actions de liste dans un rôle particulier :
- Créer un rôle personnalisé

Le script PowerShell suivant montre des exemples de façon d’effectuer les tâches suivantes :

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Attribution d’autorisations à un utilisateur pour une stratégie spécifique à l’aide des rôles personnalisés
Une fois que vous avez défini vos rôles personnalisés, vous pouvez les affecter à des utilisateurs. Pour attribuer un rôle personnalisé à un utilisateur, vous devez d’abord obtenir **ObjectId** représentant cet utilisateur. Pour ce faire, utilisez l’applet de commande **Get-AzureRmADUser** .

Dans l’exemple suivant, l' **ObjectId** de l’utilisateur *SomeUser* est 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Une fois que vous avez **ObjectId** pour l’utilisateur et un nom de rôle personnalisé, vous pouvez attribuer ce rôle à l’utilisateur avec l’applet de commande **New-AzureRmRoleAssignment** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

Dans l’exemple précédent, la stratégie **AllowedVmSizesInLab** est utilisée. Vous pouvez utiliser une des opérations suivantes stratégies :

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez accordé les autorisations des utilisateurs aux stratégies de laboratoire spécifique, voici quelques étapes suivantes à prendre en compte :

- [Sécuriser l’accès à un laboratoire](devtest-lab-add-devtest-user.md).

- [Définir des stratégies de laboratoire](devtest-lab-set-lab-policy.md).

- [Créer un modèle de laboratoire](devtest-lab-create-template.md).

- [Créer des objets personnalisés pour vos ordinateurs virtuels](devtest-lab-artifact-author.md).

- [Ajouter une machine virtuelle avec des objets à un laboratoire](devtest-lab-add-vm-with-artifacts.md).
