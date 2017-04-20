<properties
    pageTitle="Ajouter un nouveau compte client Azure pile dans Azure Active Directory | Microsoft Azure"
    description="Après le déploiement de Microsoft Azure pile du contact, vous devez créer le compte d’utilisateur au moins un client afin que vous pouvez explorer le portail client."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Ajouter un nouveau compte client Azure pile dans Azure Active Directory

Après le [déploiement de la pile de Azure du contact](azure-stack-run-powershell-script.md), vous devez un compte d’utilisateur client afin de pouvoir Explorer le portail client et tester vos offres et les plans. Vous pouvez créer un compte client à [l’aide du portail Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) ou à [l’aide de PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Créer un compte client Azure pile à l’aide du portail Azure

Vous devez posséder un abonnement Azure à utiliser le portail Azure.

1. Se connecter à [Azure](http://manage.windowsazure.com).

2.  Dans la barre de navigation gauche de Microsoft Azure, cliquez sur **Active Directory**.

3.  Dans la liste répertoire, cliquez sur le répertoire que vous souhaitez utiliser pour pile Azure ou créez-en une.

4.  Dans cette page annuaire, cliquez sur **utilisateurs**.

5.  Cliquez sur **Ajouter un utilisateur**.

6.  Dans l’Assistant **Ajouter un utilisateur** , dans la liste **Type d’utilisateur** , sélectionnez **nouvel utilisateur de votre organisation**.

7.  Dans la zone **nom d’utilisateur** , tapez un nom pour l’utilisateur.

8.  Dans la **@** zone de, sélectionnez l’entrée appropriée.

9.  Cliquez sur la flèche suivant.

10.  Dans la page de **profil utilisateur** de l’Assistant, tapez un **prénom**, un **nom**et un **nom d’affichage**.

11. Dans la liste **rôle** , sélectionnez **utilisateur**.

12. Cliquez sur la flèche suivant.

13. Dans la page **obtenir le mot de passe temporaire** , cliquez sur **créer**.

14. Copiez le **nouveau mot de passe**.

15. Se connecter à Microsoft Azure avec le nouveau compte. Modifier le mot de passe lorsque vous y êtes invité.

16. Se connecter à `https://portal.azurestack.local` avec le nouveau compte pour afficher le portail du client.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Créer un compte client Azure pile à l’aide de PowerShell

Si vous n’avez un abonnement Azure, vous ne pouvez pas utiliser le portail Azure pour ajouter un compte d’utilisateur client. Dans ce cas, vous pouvez utiliser le Module Azure pour annuaire Active pour Windows PowerShell à la place.

> [AZURE.NOTE] Si vous utilisez Microsoft Account (Live ID) pour déployer Azure pile du contact, vous ne pouvez pas utiliser PowerShell AAD pour créer le compte client. 

1.  Installez l' [Assistant Microsoft Online Services se connecter pour les informaticiens RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Installez le [Azure Active Directory Module pour Windows PowerShell (version 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) et ouvrez-le.

3.  Exécutez les applets de commande suivantes :




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Se connecter à Microsoft Azure avec le nouveau compte. Modifier le mot de passe lorsque vous y êtes invité.

5.  Se connecter à `https://portal.azurestack.local` avec le nouveau compte pour afficher le portail du client.



