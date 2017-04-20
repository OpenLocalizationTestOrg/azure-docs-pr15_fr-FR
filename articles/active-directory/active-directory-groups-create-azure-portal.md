<properties
    pageTitle="Créer un groupe en mode Aperçu avant Azure Active Directory | Microsoft Azure"
    description="Comment créer un groupe dans Azure Active Directory et ajouter des utilisateurs (membres) au groupe"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Créer un groupe en mode Aperçu avant Azure Active Directory

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-groups-create-azure-portal.md)
- [Portail classique Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Cet article explique comment créer et remplir un nouveau groupe dans l’aperçu Azure Active Directory (AD Azure). [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Utiliser un groupe pour effectuer les tâches de gestion telles que l’affectation des licences ou autorisations à un nombre d’utilisateurs ou les périphériques en même temps.

## <a name="how-do-i-create-a-group"></a>Comment créer un groupe ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2. Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

  ![Gestion des utilisateurs de l’ouverture](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Dans la carte **utilisateurs et groupes** , sélectionnez **tous les groupes**.

  ![Ouvrir la carte de groupes](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Dans la carte **utilisateurs et groupes - tous les groupes** , sélectionnez la commande **Ajouter** .

  ![Cliquez sur la commande Ajouter](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. Sur la carte de **groupe** , ajoutez un nom et une description pour le groupe.

6. Pour sélectionner les membres à ajouter au groupe, sélectionnez **attribuées** dans la zone **type d’abonnement** , puis **membres**. Pour plus d’informations sur la façon de gérer les membres d’un groupe dynamiquement, voir [attributs à l’aide pour créer des règles avancées pour l’appartenance aux groupes](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Sélection des membres à ajouter](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Sur la carte de **membres** , sélectionnez une ou plusieurs utilisateurs ou appareils à ajouter au groupe, puis sélectionnez le bouton **Sélectionner** dans la partie inférieure de la cuillère les ajouter au groupe. La zone **utilisateur** filtre l’affichage selon correspondant à votre entrée à une partie d’un nom d’utilisateur ou un appareil. Aucun des caractères génériques ne sont acceptées dans cette zone.

6. Lorsque vous avez terminé d’ajouter des membres au groupe, sélectionnez **créer** dans la carte de **groupe** .    

  ![Créer la confirmation de groupe](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Informations supplémentaires

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Voir groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les appartenances d’un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
