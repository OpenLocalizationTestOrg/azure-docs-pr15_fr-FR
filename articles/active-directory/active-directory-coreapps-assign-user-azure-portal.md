<properties
    pageTitle="Affecter un utilisateur ou un groupe à une application d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Comment sélectionner une application d’entreprise pour un utilisateur ou groupe lui associer dans Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Affecter un utilisateur ou un groupe à une application d’entreprise dans l’aperçu Azure Active Directory

Il est facile affecter un utilisateur ou un groupe à vos applications d’entreprise dans l’aperçu Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans l’aperçu en cours, vous devez être administrateur global pour l’annuaire.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Comment attribuer des accès des utilisateurs à une application d’entreprise ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2. Sélectionnez **plusieurs services**, entrez Azure Active Directory dans la zone de texte, puis **entrée**.

3. Sur le *nom du répertoire *Azure Active Directory - ** ** carte (autrement dit, la carte Azure AD pour le répertoire vous gérez), sélectionnez **Enterprise applications **.

    ![Applications d’entreprise ouverture](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Dans la carte **des applications d’entreprise** , sélectionnez **toutes les applications**. Une liste des applications que vous pouvez gérer s’affiche.

5. Dans la carte **des applications d’entreprise - toutes les applications** , sélectionnez une application.

6. Sur la carte ***appname*** (autrement dit, la carte avec le nom de l’application dans le titre sélectionné), sélectionnez **utilisateurs et groupes**.

    ![Sélection de la commande toutes les applications](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. Sur ***appname*** **-utilisateur & affectation de groupe** carte, sélectionnez la commande **Ajouter** .

8. Dans la carte **Ajouter un devoir** , sélectionnez **utilisateurs et groupes**.

    ![Affecter un utilisateur ou un groupe à l’application](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. Dans la carte **utilisateurs et groupes** , sélectionnez une ou plusieurs utilisateurs ou groupes dans la liste, puis sélectionnez le bouton **Sélectionner** dans la partie inférieure de la carte.

10. Sur la carte **Ajouter un devoir** , sélectionnez le **rôle**. Puis, dans la carte **Sélectionnez rôle** , sélectionnez un rôle à appliquer aux utilisateurs sélectionnés ou aux groupes, puis sélectionnez le bouton **OK** en bas de la carte.

11. Dans la carte **Ajouter un devoir** , sélectionnez le bouton **affecter** dans la partie inférieure de la carte. Les utilisateurs affectés ou les groupes ont les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## <a name="next-steps"></a>Étapes suivantes

- [Afficher tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Supprimer un utilisateur ou groupe d’une affectation d’une application d’entreprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Désactiver les compléments d’authentification utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
