<properties
    pageTitle="Ajouter des propriétaires et les utilisateurs dans Azure DevTest ateliers | Microsoft Azure"
    description="Ajouter des propriétaires et les utilisateurs dans Azure DevTest ateliers à l’aide de PowerShell ou portail Azure"
    services="devtest-lab,virtual-machines"
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
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Ajouter des propriétaires et les utilisateurs dans Azure DevTest ateliers

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

Access dans Azure DevTest ateliers est contrôlée par le [Contrôle d’accès Azure Role-Based (RBAC)](../active-directory/role-based-access-control-what-is.md). L’utilisation de RBAC, vous pouvez séparer les droits au sein de votre équipe dans des *rôles* dans laquelle vous octroyer uniquement la quantité d’accès nécessaire pour les utilisateurs à effectuer leurs tâches. Trois de ces rôles RBAC sont *propriétaire*, *DevTest ateliers utilisateur*et *Collaboration*. Dans cet article, vous Découvrez quelles actions peuvent être effectuées dans chacune des trois rôles RBAC principales. À partir de là, vous découvrez comment ajouter des utilisateurs à un laboratoire - à la fois via le portail et via un script PowerShell et comment ajouter des utilisateurs au niveau de l’abonnement.

## <a name="actions-that-can-be-performed-in-each-role"></a>Actions pouvant être effectuées dans chaque rôle

Il existe trois rôles principales que vous pouvez affecter un utilisateur :

- Propriétaire
- DevTest ateliers utilisateur
- Collaboration

Le tableau suivant montre les actions pouvant être effectuées par les utilisateurs de chacun de ces rôles :

| **Actions que les utilisateurs dans ce rôle peuvent effectuer** | **DevTest ateliers utilisateur**            | **Propriétaire** | **Collaboration** |
|---|---|---|---|
| **Tâches de l’atelier**                          |                              |       |             |
| Ajouter des utilisateurs à un laboratoire                     | N°                           | Oui   | N°          |
| Mettre à jour les paramètres des coûts                   | N°                           | Oui   | Oui         |
| **Tâches de base de machine virtuelle**                      |                              |       |             |
| Ajouter et supprimer des images personnalisées           | N°                           | Oui   | Oui         |
| Ajouter, mettre à jour et supprimer des formules       | Oui                          | Oui   | Oui         |
| Images d’autorisation Azure Marketplace     | N°                           | Oui   | Oui         |
| **Tâches de machine virtuelle**                           |                              |       |             |
| Créer des ordinateurs virtuels                             | Oui                          | Oui   | Oui         |
| Démarrer, arrêter et supprimer des machines virtuelles            | Uniquement les ordinateurs virtuels créés par l’utilisateur | Oui   | Oui         |
| Mettre à jour les stratégies de machine virtuelle                     | N°                           | Oui   | Oui         |
| Ajouter/supprimer des disques de données vers ou à partir d’ordinateurs virtuels      | Uniquement les ordinateurs virtuels créés par l’utilisateur | Oui   | Oui         |
| **Tâches de l’objet**                     |                              |       |             |
| Ajouter et supprimer des référentiels objet   | N°                           | Oui   | Oui         |
| Appliquer des objets                        | Oui                          | Oui   | Oui         |

> [AZURE.NOTE] Lorsqu’un utilisateur crée une machine virtuelle, cet utilisateur reçoit automatiquement le rôle de **propriétaire** de la machine virtuelle créée.

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Ajouter un propriétaire ou un utilisateur au niveau de l’atelier

Les propriétaires et les utilisateurs peuvent être ajoutés au niveau du laboratoire via le portail Azure. Cela inclut les utilisateurs externes avec un [compte Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)valide.
La procédure suivante vous guide dans la procédure d’ajout d’un propriétaire ou un utilisateur à un laboratoire dans Azure DevTest ateliers :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **plusieurs services**, puis **Ateliers DevTest** dans la liste.

1. Dans la liste des ateliers, sélectionnez le laboratoire souhaité.

1. Dans la carte du laboratoire, sélectionnez **Configuration**. 

1. Dans la carte de **Configuration** , sélectionnez **utilisateurs**.

1. Dans la carte **utilisateurs** , sélectionnez **+ Ajouter**.

    ![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Dans la carte **Sélectionner un rôle** , sélectionnez le rôle souhaité. La section [Actions pouvant être effectuées dans chaque rôle](#actions-that-can-be-performed-in-each-role) répertorie les diverses actions pouvant être effectuées par les utilisateurs du propriétaire, DevTest utilisateur et les rôles de collaboration.

1. Sur la carte **d’Ajouter des utilisateurs** , entrez l’adresse de messagerie ou le nom de l’utilisateur que vous souhaitez ajouter dans le rôle que vous avez spécifié. Si l’utilisateur ne peut pas être trouvé, un message d’erreur explique le problème. Si l’utilisateur est trouvé, cet utilisateur est répertorié et sélectionné. 

1. Sélectionnez **Sélectionner**.

1. Sélectionnez **OK** pour fermer la carte **Ajouter access** .

1. Lorsque vous revenez à la carte **d’utilisateurs** , l’utilisateur a été ajouté.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Ajouter un utilisateur externe à un laboratoire à l’aide de PowerShell

Outre l’ajout d’utilisateurs dans le portail Azure, vous pouvez ajouter un utilisateur externe à votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, simplement modifier les valeurs de paramètre sous le commentaire **à modifier les valeurs** .
Vous pouvez récupérer la `subscriptionId`, `labResourceGroup`, et `labName` valeurs à partir de la carte d’atelier dans le portail Azure.

> [AZURE.NOTE]
> L’exemple de script suppose que l’utilisateur spécifié a été ajouté en tant qu’invité à l’annuaire Active Directory et échouera si ce n’est pas le cas. Pour ajouter un utilisateur pas dans l’annuaire Active Directory à un laboratoire, utiliser le portail Azure pour affecter l’utilisateur à un rôle comme illustré dans la section [Ajouter un propriétaire ou utilisateur au niveau du laboratoire](#add-an-owner-or-user-at-the-lab-level).   

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName
    
    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Ajouter un propriétaire ou un utilisateur au niveau de l’abonnement

Autorisations Azure sont propagées à partir de la portée parent à portée enfant dans Azure. Par conséquent, propriétaires d’un abonnement Azure contenant des ateliers sont automatiquement des propriétaires de ces ateliers. Ils possèdent également les ordinateurs virtuels et autres ressources créés par les utilisateurs du laboratoire et le service Azure DevTest ateliers. 

Vous pouvez ajouter des propriétaires supplémentaires à un laboratoire via carte du laboratoire dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Toutefois, étendue le propriétaire ajouté d’administration est plus étroite que l’étendue du propriétaire de l’abonnement. Par exemple, les propriétaires ajoutés n’ont pas accès complet à certaines des ressources qui sont créés par le service DevTest ateliers dans l’abonnement. 

Pour ajouter un propriétaire à un abonnement Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Autres Services**, puis **abonnements** dans la liste.

1. Sélectionnez l’abonnement de votre choix.

1. Sélectionnez l’icône **Access** . 

    ![Utilisateurs d’Access](./media/devtest-lab-add-devtest-user/access-users.png)

1. Dans la carte **utilisateurs** , sélectionnez **Ajouter**.

    ![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Sur la carte **Sélectionner un rôle** , sélectionnez **propriétaire**.

1. Sur la carte **d’Ajouter des utilisateurs** , entrez l’adresse de messagerie ou le nom de l’utilisateur que vous voulez ajouter en tant que propriétaire. Si l’utilisateur ne peut pas être trouvé, vous obtenez un message d’erreur expliquant le problème. Si l’utilisateur est trouvé, cet utilisateur est répertorié sous la zone de texte **utilisateur** .

1. Sélectionnez le nom d’utilisateur trouve.

1. Sélectionnez **Sélectionner**.

1. Sélectionnez **OK** pour fermer la carte **Ajouter access** .

1. Lorsque vous revenez à la carte **d’utilisateurs** , l’utilisateur a été ajouté en tant que propriétaire. Cet utilisateur est désormais un propriétaire de n’importe quel ateliers créé sous cet abonnement et donc être en mesure d’effectuer les tâches de propriétaire. 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
