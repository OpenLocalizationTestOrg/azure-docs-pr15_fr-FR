<properties
    pageTitle="Gérer les mots de passe dans Azure Active Directory | Microsoft Azure"
    description="Comment gérer les mots de passe dans Azure Active Directory."
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
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="manage-passwords-in-azure-active-directory"></a>Gérer les mots de passe dans Azure Active Directory

Si vous êtes un administrateur, vous pouvez réinitialiser votre mot de passe d’un utilisateur dans Azure Active Directory (AD Azure) dans le portail classique Azure. Cliquez sur le nom de votre annuaire et dans la page utilisateurs, cliquez sur le nom de l’utilisateur, dans la partie inférieure du portail de sur **Réinitialiser le mot de passe**.

Le reste de cette rubrique couvre l’ensemble des fonctionnalités de gestion du mot de passe qui Azure AD prend en charge, notamment :

- Modifier **le mot de passe libre-service** permet aux utilisateurs finaux ou les administrateurs pour modifier leur mot de passe qui a expiré ou non expiré sans appeler un administrateur ou un support technique pour la prise en charge.
- Réinitialisation du **mot de passe libre-service** permet aux utilisateurs finaux ou les administrateurs réinitialiser les mots de passe automatiquement sans appeler un administrateur ou un support technique pour la prise en charge. Réinitialisation du mot de passe libre-service requiert Azure AD Premium ou Basic. Pour plus d’informations, voir [éditions Azure Active Directory](active-directory-editions.md).
- **Réinitialiser le mot de passe initialisé par l’administrateur** permet à un administrateur de mot de passe d’un utilisateur final ou d’un autre administrateur à partir du portail classique Azure.
- **Rapports d’activité de gestion de mot de passe** donner aux administrateurs vue d’ensemble de mot de passe réinitialiser et inscription de l’activité se produisant dans leur organisation.
- **Mot de passe en écriture différée** permet de gérer les mots de passe en local à partir du cloud afin que tous les scénarios ci-dessus peuvent être effectuée par, ou de la part de, fédérés ou mot de passe des utilisateurs synchronisés. L’écriture différée de mot de passe requiert Azure AD Premium. Pour plus d’informations, voir [mise en route d’Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]
> Azure AD Premium est disponible pour les clients chinois à l’aide de l’instance du monde entier d’Azure AD. Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure géré par 21Vianet en Chine. Pour plus d’informations, nous contacter lors du [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

Utilisez les liens suivants pour accéder à la documentation qui en que vous intéressent :

- [Vue d’ensemble : gestion des mots de passe dans Azure Active Directory](active-directory-passwords-how-it-works.md)
- [Libre-service du mot de passe dans Azure AD : comment activer, configurer et tester la réinitialisation du mot de passe libre-service](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Libre-service du mot de passe dans Azure AD : comment personnaliser votre mot de passe réinitialisé à vos besoins](active-directory-passwords-customize.md)
- [Libre-service du mot de passe dans Azure AD : déploiement et gestion des meilleures pratiques](active-directory-passwords-best-practices.md)
- [Rapports sur la gestion de mot de passe dans Azure AD : comment afficher les activités de gestion de mot de passe de votre client](active-directory-passwords-get-insights.md)
- [Mot de passe en écriture différée : comment configurer Azure AD pour gérer les mots de passe sur site](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Résolution des problèmes de gestion des mots de passe Azure AD](active-directory-passwords-troubleshoot.md)
- [Forum aux questions sur la gestion des mots de passe Azure AD](active-directory-passwords-faq.md)


## <a name="whats-next"></a>Ensuite ?

- [Administration Azure AD](active-directory-administer.md)
- [Créer ou modifier des utilisateurs dans Azure Active Directory](active-directory-create-users.md)
- [Gérer des groupes dans Azure AD](active-directory-manage-groups.md)
