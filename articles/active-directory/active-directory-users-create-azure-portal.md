<properties
    pageTitle="Ajouter de nouveaux utilisateurs Preview Azure Active Directory | Microsoft Azure"
    description="Explique comment ajouter de nouveaux utilisateurs ou modifier les informations utilisateur dans Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="add-new-users-to-azure-active-directory-preview"></a>Ajouter de nouveaux utilisateurs Preview Azure Active Directory

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-users-create-azure-portal.md)
- [Portail classique Azure](active-directory-create-users.md)

Cet article explique comment ajouter de nouveaux utilisateurs de votre organisation dans l’aperçu Azure Active affichait (Azure AD). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  Sur la carte **utilisateurs et groupes** , sélectionnez **tous les utilisateurs**, puis **Ajouter**.

    ![Cliquez sur la commande Ajouter](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Entrez les détails de l’utilisateur, telles que **nom** et **nom d’utilisateur**. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine par défaut initiales nom de domaine « foo.onmicrosoft.com » ou un nom de domaine vérifié, non fédéré tel que « contoso.com ».

5. Copiez ou notez sinon le mot de passe utilisateur généré afin que vous pouvez le fournir à l’utilisateur une fois ce processus terminé.

6. Si vous le souhaitez, vous pouvez ouvrir et renseignez les informations dans la carte de **profil** , la carte de **groupes** ou la carte de **rôle d’annuaire** pour l’utilisateur. Pour plus d’informations sur les rôles d’utilisateur et l’administrateur, voir [affectation de rôles administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

7.  Dans la carte de **l’utilisateur** , sélectionnez **créer**.

8. Distribuer en toute sécurité le mot de passe généré au nouvel utilisateur afin que l’utilisateur peut se connecter.

## <a name="whats-next"></a>Ensuite ?

- [Ajouter un utilisateur externe](active-directory-users-create-external-azure-portal.md)
- [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Modifier les informations relatives au travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
- [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
- [Supprimer un utilisateur dans votre annonce Azure](active-directory-users-delete-user-azure-portal.md)
- [Affecter un utilisateur à un rôle dans votre annonce Azure](active-directory-users-assign-role-azure-portal.md)
