<properties
    pageTitle="Gérer les groupes de votre groupe est membre dans Aperçu Azure Active Directory | Microsoft Azure"
    description="Groupes peuvent contenir d’autres groupes dans Azure Active Directory. Voici comment procéder pour gérer les appartenances."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Gérer les groupes de que votre groupe est membre dans Aperçu Azure Active Directory

Groupes peuvent contenir d’autres groupes en mode Aperçu avant Azure Active Directory. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Voici comment procéder pour gérer les appartenances.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Comment trouver les groupes de que mon groupe est membre ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

  ![Gestion des utilisateurs de l’ouverture](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Dans la carte **utilisateurs et groupes** , sélectionnez **tous les groupes**.

  ![Ouvrir la carte de groupes](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Sur la carte **utilisateurs et groupes - tous les groupes** , sélectionnez un groupe.

5. Sur la * *Group - *nom de* ** carte, sélectionnez **regrouper appartenances **.

  ![Ouvrir la carte de membres du groupe](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Pour ajouter votre groupe en tant que membre d’un autre groupe, sur la carte de **groupe - appartenances à un groupe** , sélectionnez la commande **Ajouter** .

7. Sélectionnez un groupe à partir de la carte de **Sélection d’un groupe** , puis le bouton **Sélectionner** dans la partie inférieure de la cuillère. Vous pouvez ajouter votre groupe pour qu’un seul groupe à la fois. La zone **utilisateur** filtre l’affichage selon correspondant à votre entrée à une partie d’un nom d’utilisateur ou un appareil. Aucun des caractères génériques ne sont acceptées dans cette zone.

  ![Ajouter une appartenance au groupe](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Pour supprimer votre groupe en tant que membre d’un autre groupe, sur la carte de **groupe - appartenances à un groupe** , sélectionnez un groupe.

9. Sur la carte de ***nom de groupe*** , sélectionnez la commande **Supprimer** et confirmez votre choix à l’invite.

  ![supprimer la commande d’appartenance](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Après avoir modifié les appartenances pour votre groupe, cliquez sur **Enregistrer**.


## <a name="additional-information"></a>Informations supplémentaires

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Voir groupes existants](active-directory-groups-view-azure-portal.md)
* [Créer un nouveau groupe et ajouter des membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
