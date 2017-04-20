<properties
   pageTitle="L’Assistant sécurité Azure AD dotés de privilèges gestion des identités"
   description="La première fois que vous utilisez l’extension Azure Active Directory dotés de privilèges gestion des identités, vous verrez afficher avec l’Assistant sécurité. Cet article décrit la procédure d’utilisation de l’Assistant."
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>L’Assistant sécurité Azure AD dotés de privilèges gestion des identités

Si vous êtes le premier participant à exécuter Azure dotés de privilèges identité PIM (Management) pour votre organisation, vous verrez afficher avec l’Assistant. L’Assistant permet d’identifier les risques de sécurité des identités dotés de privilèges et comment utiliser GIP pour limiter ces risques. Vous ne devez apporter des modifications aux affectations de rôle existantes dans l’Assistant, si vous préférez le faire ultérieurement.

## <a name="what-to-expect"></a>À quoi s’attendre

Avant le démarrage de votre organisation à l’aide de PIM, toutes les attributions de rôle sont permanentes : les utilisateurs sont toujours dans ces rôles même s’ils ne sont pas actuellement pas leurs privilèges.  La première étape de l’Assistant affiche une liste des rôles avec privilèges élevés et combien d’utilisateurs se trouvent dans ces rôles. Vous pouvez extraire à un rôle particulier pour en savoir plus sur les utilisateurs si une ou plusieurs d'entre eux ne connaissez pas.

La deuxième étape de l’Assistant vous donne la possibilité de modifier les attributions de rôle d’administrateur.  

> [AZURE.WARNING]Il est important que vous disposez au moins un administrateur global et plusieurs rôle dotés de privilèges administrateur avec un compte professionnel (et non un compte Microsoft). S’il n'existe qu’un seul rôle dotés de privilèges administrateur, l’organisation ne seront pas en mesure de gérer PIM si ce compte est supprimé.
> En outre, conserver les attributions de rôle permanent si un utilisateur a un compte Microsoft (un compte qu’ils utilisent pour se connecter aux services Microsoft tels que Skype et Outlook.com). Si vous envisagez d’exiger l’authentification Multifacteur pour l’activation de ce rôle, cet utilisateur sera verrouillé.


Une fois que vous avez apporté des modifications, l’Assistant n’est plus apparaît. La prochaine fois que vous ou un autre rôle dotés de privilèges administrateur utiliser GIP, vous verrez le tableau de bord PIM.  

- Si vous voulez ajouter ou supprimer des utilisateurs de rôles ou modifier les affectations de permanent éligibles, pour en savoir plus en [comment ajouter ou supprimer des rôles d’un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Si vous souhaitez autoriser plusieurs utilisateurs à accéder à gérer PIM, découvrez comment [donner accès à gérer dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
