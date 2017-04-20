<properties
    pageTitle="Désactiver les compléments d’authentification utilisateur pour une application d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Comment désactiver une application d’entreprise afin qu’aucun utilisateur ne peut se connecter à celui-ci dans Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Désactiver les compléments d’authentification utilisateur pour une application d’entreprise dans l’aperçu Azure Active Directory

Il est facile de désactiver une application d’entreprise afin qu’aucun utilisateur ne peut se connecter à celle-ci en mode Aperçu avant Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu en cours, vous devez être administrateur global pour l’annuaire.

## <a name="how-do-i-disable-user-sign-ins"></a>Comment désactiver les compléments d’authentification utilisateur ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2. Sélectionnez **plusieurs services**, entrez **Azure Active Directory** dans la zone de texte, puis **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** carte (autrement dit, la carte Azure AD pour le répertoire vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Dans la carte **des applications d’entreprise** , sélectionnez **toutes les applications**. Vous consultez une liste des applications que vous pouvez gérer.

5. Dans la carte **des applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Dans la carte ***appname*** (autrement dit, la carte avec le nom de l’application dans le titre sélectionné), sélectionnez **Propriétés**.

    ![Sélection de la commande toutes les applications](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Sur ***appname*** **-Propriétés** carte, sélectionnez **non** pour **activées pour les utilisateurs se connectent à ?**.

8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes

- [Afficher tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Supprimer un utilisateur ou groupe d’une affectation d’une application d’entreprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
