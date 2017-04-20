<properties
    pageTitle="Prise en main de l’authentification Multifacteur Azure dans le cloud | Microsoft Azure"
    description="Il s’agit de la page de l’authentification multifacteur de Microsoft Azure qui décrit comment commencer à utiliser l’authentification Multifacteur Azure dans le cloud."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Prise en main l’authentification multifacteur Azure dans le cloud
Cet article décrit comment commencer à utiliser l’authentification multifacteur Azure dans le cloud.

> [AZURE.NOTE]  La documentation suivante fournit des informations sur l’activation des utilisateurs à l’aide du **Portail classique Azure**. Si vous recherchez des informations sur la façon de configurer l’authentification multifacteur Azure pour les utilisateurs d’Office 365, voir [configurer l’authentification multifacteur pour Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![Authentification Multifacteur dans le Cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Conditions préalables
Les composants requis suivants sont requis avant de pouvoir activer l’authentification multifacteur Azure pour vos utilisateurs.


1. [Inscrivez-vous à un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/) - si vous n’avez pas déjà un abonnement Azure, vous devez d’abonnement pour une. Si vous êtes simplement débutent et à l’aide de l’authentification Multifacteur Azure, vous pouvez utiliser un abonnement d’évaluation
2. [Créer un fournisseur d’authentification multifacteur](multi-factor-authentication-get-started-auth-provider.md) et affectez-le à votre répertoire ou [attribuer des licences aux utilisateurs](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licences sont disponibles pour les utilisateurs qui ont l’authentification Multifacteur Azure, Azure AD Premium ou entreprise mobilité Suite (EM).  L’authentification Multifacteur est inclus dans Azure AD Premium et l’EMS. Si vous avez suffisamment de licences, vous n’avez pas besoin de créer un fournisseur Auth.


## <a name="turn-on-two-step-verification-for-users"></a>Activer la vérification pour les utilisateurs
Pour démarrer nécessitant des deux début vérification sur pour un utilisateur, changez l’état de désactivé à activé.  Pour plus d’informations sur les États utilisateur, voir [États utilisateur dans l’authentification multifacteur Azure](multi-factor-authentication-get-started-user-states.md)

Utilisez la procédure suivante pour activer l’authentification Multifacteur pour vos utilisateurs.

### <a name="to-turn-on-multi-factor-authentication"></a>Pour activer l’authentification multifacteur

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com) en tant qu’administrateur.
2.  Sur la gauche, cliquez sur **Active Directory**.
3.  Sous répertoire, sélectionnez l’annuaire de l’utilisateur que vous souhaitez activer.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Dans la partie supérieure, cliquez sur **utilisateurs**.
5.  Dans la partie inférieure de la page, cliquez sur **Gérer les authentification multifacteur**. Un nouvel onglet de navigateur s’ouvre.
![Cliquez sur le répertoire](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Recherchez l’utilisateur que vous souhaitez activer pour la vérification. Vous devrez peut-être modifier l’affichage dans la partie supérieure. Vérifiez que l’état est **désactivé.** 
 ![Autoriser l’utilisateur](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Placez une **vérification** dans la zone en regard de son nom.
7.  Sur la droite, cliquez sur **Activer**.
![Utilisateur](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Cliquez sur **Activer l’authentification multifacteur**.
![Utilisateur](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Notez qu’état de l’utilisateur a changé de **désactivé** à **activé**.
![Permettre aux utilisateurs](./media/multi-factor-authentication-get-started-cloud/user.png)

Une fois que vous avez activé vos utilisateurs, vous devez les avertir par courrier électronique. La prochaine fois que tentez de vous connecter, ils serez invités à inscrire leur propre compte pour la vérification. Une fois qu’ils démarrent à l’aide de la vérification, ils devez également configurer les mots de passe application afin d’éviter verrouillés déconnecter applications autres que des navigateurs.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Utiliser PowerShell pour automatiser l’activation de la vérification

Pour modifier l' [état](multi-factor-authentication-whats-next.md) à l’aide [d’Azure AD PowerShell](../powershell-install-configure.md), vous pouvez utiliser les éléments suivants.  Vous pouvez modifier `$st.State` soit égale à un des états suivants :

- Activé
- Mises en œuvre
- Désactivé  

> [AZURE.IMPORTANT]  Nous déconseillons contre déplacement d’utilisateurs directement à partir de l’état de désactiver l’état appliqué. Applications basées sur le navigateur non il s’interrompt, car l’utilisateur ne dispose pas disparaît via l’inscription de l’authentification Multifacteur et obtenir un [mot de passe de l’application](multi-factor-authentication-whats-next.md#app-passwords). Si vous avez des applications non basés sur un navigateur et exiger des mots de passe de l’application, nous vous recommandons d’accéder à partir d’un état désactivé à activé. Cela permet aux utilisateurs d’enregistrer et obtenir son mot de passe d’application. Après cela, vous pouvez les déplacer sur appliqué.

À l’aide de PowerShell serait une option pour permettre aux utilisateurs d’en bloc. Actuellement, il n’existe aucune fonctionnalité d’activer en bloc dans le portail Azure et vous devez sélectionner chaque utilisateur individuellement. Cela peut s’avérer très si vous disposez de nombreux utilisateurs. En créant un PowerShell script à l’aide des opérations suivantes, vous pouvez effectuer une boucle dans une liste des utilisateurs et les activez.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Voici un exemple :

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Pour plus d’informations, voir [États utilisateur dans l’authentification multifacteur Azure](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez configuré l’authentification multifacteur Azure dans le cloud, vous pouvez configurer et configurer votre déploiement. Pour plus d’informations, voir [Configuration de l’authentification multifacteur Azure](multi-factor-authentication-whats-next.md) .
