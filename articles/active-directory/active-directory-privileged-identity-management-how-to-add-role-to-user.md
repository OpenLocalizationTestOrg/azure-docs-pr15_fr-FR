<properties
   pageTitle="Comment ajouter ou supprimer un rôle d’utilisateur | Microsoft Azure"
   description="Découvrez comment ajouter des rôles avec les identités dotés de privilèges avec l’application Azure Active Directory dotés de privilèges gestion des identités."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD dotés de privilèges gestion des identités : Comment ajouter ou supprimer un rôle d’utilisateur

Avec Azure Active Directory (AD), un administrateur global (ou administrateur de la société) peut mettre à jour les utilisateurs qui sont **définitivement** attribuées aux rôles dans Azure Active Directory. Pour cela, avec les applets de commande PowerShell comme `Add-MsolRoleMember` et `Remove-MsolRoleMember`. Ou qu’ils peuvent utiliser le portail classique Azure comme décrit dans [attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

L’application de gestion des identités Azure AD dotés de privilèges permet aux administrateurs de rôle dotés de privilèges vérifier les attributions de rôle permanent, également. En outre, rôle dotés de privilèges administrateurs peuvent apporter aux utilisateurs **éligible** pour le rôle d’administrateur. Un administrateur éligible peut activer le rôle lorsqu’ils ont besoin, et puis leurs autorisations expirent une fois qu’ils ont terminé.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gérer les rôles avec PIM dans le portail Azure

Dans votre organisation, vous pouvez affecter des utilisateurs à différents rôles administratifs dans Azure AD, applications et Office 365 et d’autres services Microsoft.  Vous trouverez plus d’informations sur les rôles disponibles à [rôles dans Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Pour ajouter ou supprimer un utilisateur dans un rôle à l’aide de la gestion des identités dotés de privilèges, ouvrez le tableau de bord PIM. Cliquez ensuite sur le bouton **des utilisateurs dans le rôle d’administrateur** , ou sélectionnez un rôle spécifique (par exemple, l’administrateur Global) dans le tableau rôles.

> [AZURE.NOTE] Si vous n’avez pas encore activée PIM dans le portail Azure, accédez à la [prise en main Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) pour plus d’informations.

Si vous voulez donner un autre utilisateur accès à PIM lui-même, les rôles PIM, l’utilisateur doit disposer sont décrites dans [la section accorder l’accès PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Ajouter un utilisateur à un rôle

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez la vignette de **la gestion des identités Azure AD dotés de privilèges** du tableau de bord.
2. Sélectionnez **Gérer les rôles dotés de privilèges**.
3. Dans la table de **Synthèse de rôle** , sélectionnez le rôle que vous voulez gérer.
4. Dans la carte de rôle, sélectionnez **Ajouter**.
5. Cliquez sur **Sélectionner des utilisateurs** et recherche de l’utilisateur sur la carte **Sélectionnez utilisateurs** .  
6. Sélectionnez l’utilisateur dans la liste des résultats, puis cliquez sur **terminé**.
4. Cliquez sur **OK** pour enregistrer votre sélection. L’utilisateur que vous avez sélectionné apparaît dans la liste comme éligibles pour le rôle.

> [AZURE.NOTE]
>Nouveaux utilisateurs dans un rôle ne sont pas éligibles pour le rôle par défaut. Si vous voulez conserver le rôle, cliquez sur l’utilisateur dans la liste. Informations de l’utilisateur seront affichent dans une nouvelle carte. Dans le menu informations utilisateur, sélectionnez **rendre permanente** .  
>Si un utilisateur ne peut pas enregistrer pour Azure multifacteur l’authentification Multifacteur ou utilise un compte Microsoft (généralement @outlook.com), vous avez besoin afin de les rendre permanentes dans tous les rôles de leurs. Les administrateurs éligibles sont invités à inscrire pour l’authentification Multifacteur lors de l’activation.

À présent que l’utilisateur est éligible pour un rôle, faites-le leur savoir qu’ils peuvent l’activer conformément aux instructions de la rubrique [comment activer ou désactiver un rôle](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Supprimer un utilisateur d’un rôle

Vous pouvez supprimer des utilisateurs d’attributions de rôle éligibles, mais assurez-vous qu’il existe toujours au moins un utilisateur qui est un administrateur global permanent.

Suivez ces étapes pour supprimer un utilisateur spécifique d’un rôle :

1. Accédez au rôle dans la liste rôle en sélectionnant un rôle dans le tableau de bord Azure AD PIM ou en cliquant sur le bouton **des utilisateurs dans les rôles d’administrateur** .
2. Cliquez sur l’utilisateur dans la liste des utilisateurs.
3. Cliquez sur **Supprimer**. Un message vous demande de confirmer.
4. Cliquez sur **Oui** pour supprimer le rôle de l’utilisateur.

Si vous ne savez pas quels les utilisateurs doivent toujours leurs attributions de rôle, vous pouvez [Démarrer une révision access pour le rôle](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
