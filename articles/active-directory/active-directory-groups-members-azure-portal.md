<properties
    pageTitle="Gérer les membres d’un groupe en mode Aperçu avant Azure Active Directory | Microsoft Azure"
    description="Comment les utilisateurs et périphériques qui font partie d’un groupe dans Azure Active Directory"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gérer les membres d’un groupe en mode Aperçu avant Azure Active Directory

Cet article explique comment gérer les membres d’un groupe en mode Aperçu avant Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Comment trouver les membres et les gérer ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

  ![Gestion des utilisateurs de l’ouverture](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Dans la carte **utilisateurs et groupes** , sélectionnez **tous les groupes**.

  ![Ouvrir la carte de groupes](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Sur la carte **utilisateurs et groupes - tous les groupes** , sélectionnez un groupe.

5. Sur le *nom de groupe *groupe - ** ** carte, sélectionnez **membres **.

  ![Ouvrir la carte de membres](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Pour ajouter des membres au groupe, sur la carte de **groupe - membres** , sélectionnez **Ajouter des membres**.

  ![Ajouter la commande membres](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Sur la carte de **membres** , sélectionnez une ou plusieurs utilisateurs ou appareils à ajouter au groupe, puis sélectionnez le bouton **Sélectionner** dans la partie inférieure de la cuillère les ajouter au groupe. La zone **utilisateur** filtre l’affichage selon correspondant à votre entrée à une partie d’un nom d’utilisateur ou un appareil. Aucun des caractères génériques ne sont acceptées dans cette zone.

8. Pour supprimer des membres du groupe, sur la carte de **groupe - membres** , sélectionnez un membre.

9. Sur la carte de ***nom de membre*** , sélectionnez la commande **Supprimer** et confirmez votre choix à l’invite.

  ![supprimer la commande membres](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Lorsque vous avez terminé de modifier les membres du groupe, cliquez sur **Enregistrer**.


## <a name="additional-information"></a>Informations supplémentaires

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Voir groupes existants](active-directory-groups-view-azure-portal.md)
* [Créer un nouveau groupe et ajouter des membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les appartenances d’un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
