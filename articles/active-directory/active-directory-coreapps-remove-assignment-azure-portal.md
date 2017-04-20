<properties
    pageTitle="Supprimer un utilisateur ou groupe d’une affectation d’une application d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Comment faire pour supprimer l’affectation de l’accès d’un utilisateur ou un groupe à partir d’une application d’entreprise dans Azure Active Directory"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Supprimer un utilisateur ou une affectation de groupe d’une application d’entreprise dans l’aperçu Azure Active Directory

Il est facile de supprimer un utilisateur ou un groupe d’accès assignée à un de vos applications d’entreprise dans l’aperçu Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu en cours, vous devez être administrateur global pour l’annuaire.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Comment supprimer un utilisateur ou une affectation de groupe ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2. Sélectionnez **plusieurs services**, entrez **Azure Active Directory** dans la zone de texte, puis **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** carte (autrement dit, la carte Azure AD pour le répertoire vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. Dans la carte **des applications d’entreprise** , sélectionnez **toutes les applications**. Une liste des applications que vous pouvez gérer s’affiche.

5. Dans la carte **des applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Sur la carte ***appname*** (autrement dit, la carte avec le nom de l’application dans le titre sélectionné), sélectionnez **utilisateurs et groupes**.

    ![Sélectionner des utilisateurs ou groupes](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. Sur ***appname*** **-utilisateur & affectation de groupe** carte, sélectionnez une des autres utilisateurs ou groupes, puis sélectionnez la commande **Supprimer** . Confirmez à l’invite.

    ![Sélection de la commande Supprimer](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Étapes suivantes

- [Afficher tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Désactiver les compléments d’authentification utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
