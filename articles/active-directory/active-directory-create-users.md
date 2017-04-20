<properties
    pageTitle="Ajouter de nouveaux utilisateurs à Azure Active Directory | Microsoft Azure"
    description="Explique comment ajouter de nouveaux utilisateurs ou modifier les informations utilisateur dans Azure Active Directory."
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
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Ajouter de nouveaux utilisateurs ou les utilisateurs possédant un compte Microsoft Azure Active Directory

Ajouter des utilisateurs pour remplir dans l’annuaire. Cet article explique comment ajouter de nouveaux utilisateurs de votre organisation et comment ajouter des utilisateurs possédant un compte Microsoft. Pour plus d’informations sur l’ajout d’utilisateurs à partir d’autres annuaires dans Azure Active Directory ou en ajoutant des utilisateurs à partir de partenaires, voir [Ajouter des utilisateurs à partir d’autres répertoires ou partenaires dans Azure Active Directory](active-directory-create-users-external.md). Utilisateurs ajoutés n’ont pas les autorisations d’administrateur par défaut, mais vous pouvez attribuer des rôles leur à tout moment.

## <a name="add-a-user"></a>Ajouter un utilisateur

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com) avec un compte qui est un administrateur global pour l’annuaire.
2. Sélectionnez **Active Directory**, puis le nom de l’annuaire de votre organisation.
3. Sélectionnez l’onglet **utilisateurs** et puis, dans la barre de commandes, sélectionnez **Ajouter un utilisateur**.
4. Dans la page **dites-nous sur cet utilisateur** , sous **Type d’utilisateur**, sélectionnez des options :

    - **Nouvel utilisateur de votre organisation** – ajoute un nouveau compte d’utilisateur dans votre annuaire.
    - **Utilisateur avec un compte Microsoft existant** – ajoute un compte client Microsoft existant à votre annuaire (par exemple, un compte Outlook)

5. Selon le **Type d’utilisateur**, entrez un nom d’utilisateur (pour un nouvel utilisateur) ou une adresse de messagerie (pour un utilisateur avec un compte Microsoft).
6. Dans la page de **profil** utilisateur, fournissent un nom et prénom, un nom convivial et un rôle d’utilisateur dans la liste des **rôles** . Pour plus d’informations sur les rôles d’utilisateur et l’administrateur, voir [affectation de rôles administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md). Spécifier si vous souhaitez **Activer l’authentification multifacteur** pour l’utilisateur.
7. Dans la page **obtenir le mot de passe temporaire** , sélectionnez **créer**.

> [AZURE.IMPORTANT] Si votre organisation utilise plusieurs domaines, vous devez savoir sur les problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Pour ajouter des comptes d’utilisateurs avec le nom d’utilisateur principal (UPN) sur plusieurs domaines, **première** ajouter, par exemple, geoffgrisso@contoso.onmicrosoft.com, **suivie** geoffgrisso@contoso.com.
> - **Ne pas** ajouter geoffgrisso@contoso.com avant d’ajouter geoffgrisso@contoso.onmicrosoft.com. Cet ordre est important et peut être difficile à annuler.

## <a name="change-user-information"></a>Modifiez les informations utilisateur

Vous pouvez modifier n’importe quel attribut utilisateur à l’exception de l’ID d’objet.

1. Ouvrez votre répertoire.
2. Sélectionnez l’onglet **utilisateurs** , puis le nom d’affichage de l’utilisateur à modifier.
3. Effectuez vos modifications, puis cliquez sur **Enregistrer**.

Si l’utilisateur que vous souhaitez modifier est synchronisé avec votre service d’annuaire Active Directory local, vous ne pouvez pas modifier les informations utilisateur à l’aide de cette procédure. Pour modifier l’utilisateur, utilisez vos outils de gestion Active Directory local.

## <a name="guest-user-management-and-limitations"></a>Limitations et gestion des utilisateurs invités

Comptes invités sont des utilisateurs à partir d’autres annuaires qui ont été invitées à votre annuaire pour accéder aux documents SharePoint, applications ou autres ressources Azure. Un compte invité dans l’annuaire de l’attribut UserType sous-jacent a la valeur « Invités ». Les utilisateurs standard (en particulier, les membres de votre répertoire) ont l’attribut UserType « Membre ».

Invités ont un ensemble limité de droits dans l’annuaire. Ces droits limitent la possibilité pour les invités à découvrir les informations relatives aux autres utilisateurs dans l’annuaire. Toutefois, les utilisateurs invités peuvent toujours interagir avec les utilisateurs et groupes associés aux ressources qu'elles travaillent. Les utilisateurs invités peuvent :

- Voir d’autres utilisateurs et groupes associés à un abonnement Azure à laquelle elles sont affectées
- Afficher les membres de groupes à laquelle elles appartiennent
- Rechercher d’autres utilisateurs dans le répertoire, s’ils ne connaissent l’adresse de messagerie complète de l’utilisateur
- Voir uniquement un ensemble limité des attributs des utilisateurs qu’ils ont des--limitées pour afficher le nom, adresse de messagerie, nom d’utilisateur principal (UPN) et photo miniature
- Obtenir une liste de domaines vérifiés dans l’annuaire
- Consentement pour les applications, leur accordant le même niveau d’accès des membres dans votre annuaire

## <a name="set-guest-user-access-policies"></a>Définir les stratégies d’accès utilisateur invité

Sous l’onglet **configurer** d’un répertoire comprend des options pour contrôler l’accès pour les utilisateurs invités. Ces options peuvent être modifiées par un administrateur global répertoire uniquement dans Azure portal classique. Il n’existe actuellement aucune méthode PowerShell ou API.

Pour ouvrir l’onglet **configurer** dans le portail classique Azure, sélectionnez **Active Directory**, puis le nom de l’annuaire.

![Onglet Configurer dans Azure Active Directory][1]

Vous pouvez modifier les options pour contrôler l’accès pour les utilisateurs invités.

![options de contrôle d’accès pour les utilisateurs invités][2]


## <a name="whats-next"></a>Ensuite ?

- [Ajouter des utilisateurs à partir d’autres répertoires ou partenaires dans Azure Active Directory](active-directory-create-users-external.md)
- [Administration Azure AD](active-directory-administer.md)
- [Gérer les mots de passe dans Azure Active Directory](active-directory-manage-passwords.md)
- [Gérer des groupes dans Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
