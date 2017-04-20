<properties
   pageTitle="Comment donner accès à PIM | Microsoft Azure"
   description="Découvrez comment ajouter des rôles aux utilisateurs avec l’extension Azure Active Directory dotés de privilèges gestion des identités afin qu’ils peuvent gérer PIM."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Comment donner accès à gérer la gestion des identités Azure AD dotés de privilèges

L’administrateur global qui active automatiquement Azure AD dotés de privilèges identité gestion d’une organisation obtenir attributions de rôle et l’accès aux PIM. Personne d’autre Obtient un accès en écriture par défaut, cependant, y compris les autres administrateurs globaux. Autres administrateurs globaux, les administrateurs de la sécurité et les lecteurs de sécurité ont accès en lecture seule à Azure AD PIM. Pour donner accès aux PIM, le premier utilisateur peut affecter d’autres personnes au rôle **administrateur de rôles dotés de privilèges** . Cette affectation doit être effectuée dans PIM lui-même et ne peuvent pas être modifiée via PowerShell ou autres portails.

> [AZURE.NOTE] La gestion d’Azure AD PIM nécessite l’authentification Multifacteur Azure. Dans la mesure où les comptes Microsoft ne peut pas enregistrer pour l’authentification Multifacteur Azure, un utilisateur qui se connecte avec un compte Microsoft n’est pas accessible Azure AD PIM.

Vérifiez qu’est toujours au moins deux utilisateurs dans un rôle d’administrateur rôle dotés de privilèges, au cas où un utilisateur est verrouillé ou leur propre compte est supprimé.

## <a name="give-another-user-access-to-manage-pim"></a>Autoriser un autre utilisateur à gérer PIM

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez l’application de **Gestion des identités Azure AD dotés de privilèges** du tableau de bord.
2. Sélectionnez **Gérer les rôles dotés de privilèges** > **rôle dotés de privilèges administrateur** > **Ajouter**.

    ![Ajouter des administrateurs de rôles dotés de privilèges - capture d’écran][1]

4. Sur la carte ajouter les utilisateurs gérés, l’étape 1 est déjà terminée. Sélectionnez l’étape 2, **Sélectionnez utilisateurs** et recherche de l’utilisateur que vous voulez ajouter.

    ![Sélectionnez utilisateurs - capture d’écran][2]

6. Sélectionnez l’utilisateur dans les résultats de recherche, puis cliquez sur **terminé**.
7. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné apparaît dans la liste des administrateurs de rôles dotés de privilèges.

    - Quand vous attribuez un rôle à une personne, ils sont configurés automatiquement comme éligibles pour activer le rôle. Si vous souhaitez les rendre permanentes dans le rôle, cliquez sur l’utilisateur dans la liste. Dans le menu informations utilisateur, sélectionnez **rendre permanente** .

8. Envoyer l’utilisateur un lien vers [prise en main la gestion des identités Azure AD dotés de privilèges](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Supprimer les droits d’accès d’un autre utilisateur pour la gestion PIM

Avant de supprimer une personne du rôle de privilège rôle d’administrateur, vérifiez toujours il sera toujours deux utilisateurs affectés à celle-ci.

1. Dans le tableau de bord PIM, cliquez sur le rôle **administrateur de rôles dotés de privilèges**.  La liste des utilisateurs actuellement dans ce rôle s’affichera.
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**.  Un message de confirmation s’affiche.
4. Cliquez sur **Oui** pour supprimer l’utilisateur du rôle.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
