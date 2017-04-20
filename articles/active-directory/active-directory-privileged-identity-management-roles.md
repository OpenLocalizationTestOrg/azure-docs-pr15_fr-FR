<properties
   pageTitle="Rôles dans PIM | Microsoft Azure"
   description="Découvrez quels rôles sont utilisés pour les identités dotés de privilèges avec l’extension de la gestion des identités dotés de privilèges Azure."
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

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Rôles dans Azure AD privilégié gestion des identités

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Vous pouvez attribuer aux utilisateurs de votre organisation à différents rôles administratifs dans Azure Active Directory. Ces attributions de rôle contrôlent quelles tâches, telles que l’ajout ou suppression d’utilisateurs ou modification des paramètres de service, les utilisateurs sont autorisés à effectuer sur Azure AD, Office 365 et autres Microsoft Online Services et applications connectées.  

Un administrateur global peut mettre à jour les utilisateurs qui sont **définitivement** attribuées aux rôles dans Azure Active Directory, à l’aide des applets de commande PowerShell tel que `Add-MsolRoleMember` et `Remove-MsolRoleMember`, ou via le portail classique comme décrit dans [attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

Azure AD privilégié identité gestion gère les stratégies pour l’accès pour les utilisateurs dotés de privilèges dans Azure Active Directory. PIM affecte les utilisateurs à un ou plusieurs rôles dans Azure Active Directory, et vous pouvez affecter une personne à être définitivement dans le rôle, ou éligible pour le rôle. Lorsqu’un utilisateur est définitivement affecté à un rôle ou Active une attribution de rôle éligibles, puis qu’ils peuvent gérer Azure Active Directory, Office 365 et autres applications avec les autorisations attribuées aux leurs rôles.

Il n’existe aucune différence dans le niveau d’accès donné à une personne disposant permanent par rapport à une attribution de rôle éligible. La seule différence est que certaines personnes ne doivent par access tout le temps. Ils sont effectués éligibles pour le rôle et peuvent activer et désactiver chaque fois qu’ils souhaitent.

## <a name="roles-managed-in-pim"></a>Rôles gérés dans PIM

Gestion des identités dotés de privilèges vous permet d’attribuer des rôles d’administrateur courantes, y compris :


- **Administrateur global** (également appelé administrateur de la société) a accès à toutes les fonctionnalités d’administration. Vous pouvez avoir plusieurs administrateur global de votre organisation. La personne qui s’inscrit à acheter Office 365 automatiquement devient un administrateur général.
- **Administrateur de rôles dotés de privilèges** gère Azure AD PIM et met à jour les attributions de rôle à d’autres utilisateurs.  
- **Administrateur de facturation** : effectue des achats, gère les abonnements, tickets de support et surveille l’état du service.
- **Mot de passe administrateur** réinitialise les mots de passe, gère les demandes de service et surveille l’état du service. Les administrateurs de mot de passe sont limités à la réinitialisation des mots de passe pour les utilisateurs.
- **Administrateur de service** gère les demandes de service et moniteurs du service.

  > [AZURE.NOTE] Si vous utilisez Office 365, puis avant d’attribuer le rôle d’administrateur de service à un utilisateur, tout d’abord affecter l’utilisateur des autorisations d’administration à un service, tel que Exchange Online.

- **Administrateur de gestion des utilisateurs** réinitialise les mots de passe, surveille l’état des services et gère les comptes d’utilisateurs, les groupes d’utilisateurs et les demandes de service. L’administrateur de gestion des utilisateurs ne peuvent pas supprimer un administrateur général, créer des autres rôles d’administrateur ou réinitialiser les mots de passe pour facturation, généraux et les administrateurs de service.
- **Administrateur Exchange** a l’accès administratif à Exchange Online via le Exchange admin center (centre d’administration Exchange) et peut effectuer presque n’importe quelle tâche dans Exchange Online.
- **Administrateur SharePoint** a l’accès administratif à SharePoint Online via le centre d’administration SharePoint Online et peut effectuer presque n’importe quelle tâche dans SharePoint Online.
- **Skype pour l’administrateur d’entreprise** a l’accès administratif à Skype entreprise via le Skype centre d’administration Business et peut effectuer presque n’importe quelle tâche dans Skype entreprise Online.

Lisez les articles suivants pour plus d’informations sur les [rôles d’administrateur affectation dans Azure AD](active-directory-assign-admin-roles.md) et [attribution de rôles d’administrateur dans Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


À partir de PIM, vous pouvez [affecter ces rôles à un utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md) afin que l’utilisateur peut [Activer le rôle lorsque cela est nécessaire](active-directory-privileged-identity-management-how-to-activate-role.md).

Si vous souhaitez autoriser un autre utilisateur à gérer dans PIM lui-même, les rôles PIM nécessitant l’utilisateur dispose sont décrites dans [comment accorder l’accès PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Rôles non gérés dans PIM

Rôles dans Exchange Online ou SharePoint Online, à l’exception de ceux présentés ci-dessus, ne sont pas représentés dans Azure Active Directory et donc ne sont pas visibles dans PIM. Pour plus d’informations sur la modification des attributions de rôle permissions dans ces services Office 365, voir [autorisations dans Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Abonnements Azure et groupes de ressources sont également pas représentés dans Azure Active Directory. Pour gérer les abonnements Azure, voir [comment ajouter ou modifier des rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md) et pour plus d’informations sur RBAC Azure [Azure_Role-Based le contrôle d’accès](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Rôles d’utilisateur et se connecter
Certaines applications et de services de Microsoft, attribution d’un utilisateur à un rôle peut ne pas être suffisant pour permettre à cet utilisateur d’être un administrateur.

Accéder au portail classique Azure nécessite que l’utilisateur être un administrateur de service ou administrateur Co-création sur un abonnement Azure, même si l’utilisateur n’a pas besoin gérer les abonnements Azure.  Par exemple, pour gérer les paramètres de configuration d’Azure AD dans le portail classique, un utilisateur doit être un administrateur global dans Azure AD et un administrateur de co-création abonnement sur un abonnement Azure.  Pour savoir comment ajouter des utilisateurs aux abonnements Azure, Découvrez [comment ajouter ou modifier des rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md).

Accès aux Services en ligne Microsoft peut nécessiter l’utilisateur également être attribué une licence avant de pouvoir ouvrir le portail du service ou effectuer des tâches administratives.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Attribuer une licence à un utilisateur dans Active Directory Azure

1. Se connecter à [portal classique Azure] (http://manage.windowsazure.com) avec un compte d’administrateur général ou un compte d’administrateur Co-création.
2. Sélectionner **Tous les éléments** dans le menu principal.
3. Sélectionnez le répertoire que vous souhaitez utiliser et qui dispose des licences associés.
4. Sélectionnez **licences**. La liste de licences disponibles s’affiche.
5. Sélectionnez le plan de licence qui contient les licences que vous souhaitez distribuer.
6. Sélectionnez **attribuer aux utilisateurs**.
7. Sélectionnez l’utilisateur que vous voulez attribuer une licence.
8. Cliquez sur le bouton **affecter** .  L’utilisateur peut désormais se connecter à Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
