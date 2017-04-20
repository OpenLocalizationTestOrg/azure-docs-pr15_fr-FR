<properties
    pageTitle="Stratégies de mot de passe et les restrictions dans Azure Active Directory | Microsoft Azure"
    description="Décrit les stratégies qui s’appliquent dans Azure Active Directory, y compris les caractères autorisés, longueur et l’expiration des mots de passe"
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
    ms.date="10/04/2016"
    ms.author="curtand"/>


# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Stratégies de mot de passe et les restrictions dans Azure Active Directory

Cet article décrit les stratégies de mot de passe et la complexité associées aux comptes d’utilisateurs stockés dans votre annuaire Azure AD.

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Stratégies UserPrincipalName qui s’appliquent à tous les comptes d’utilisateur

Chaque compte d’utilisateur qui doit se connecter au système d’authentification Azure AD doit comporter une valeur d’attribut de nom principal (UPN) utilisateur unique associée à ce compte. Dans le tableau suivant les stratégies qui s’appliquent à ces deux locaux comptes d’utilisateur provenant d’Active Directory (synchronisés avec le cloud) et aux comptes d’utilisateurs exclusivement le nuage.

|   Propriété           |     Configuration requise UserPrincipalName  |
|   ----------------------- |   ----------------------- |
|  Caractères autorisés    |  <ul> <li>À A Z.</li> <li>-z </li><li>0 à 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  Caractères non autorisés  | <ul> <li>N’importe quel '@' caractère qui n’est pas séparer le nom d’utilisateur du domaine.</li> <li>Ne peut pas contenir un caractère point '.' précédant immédiatement la '@' symbole</li></ul> |
| Contraintes de longueur  |       <ul> <li>Longueur totale ne doit pas dépasser 113 caractères</li><li>64 caractères avant la ‘@’ symbole</li><li>48 caractères après la ‘@’ symbole</li></ul>

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Stratégies de mot de passe qui s’appliquent uniquement aux comptes d’utilisateurs cloud

Le tableau suivant décrit les paramètres de stratégie de mot de passe disponibles qui peuvent être appliquées aux comptes d’utilisateurs qui sont créées et gérées dans Azure Active Directory.

|  Propriété       |    Configuration requise          |
|   ----------------------- |   ----------------------- |
|  Caractères autorisés   |   <ul><li>À A Z.</li><li>-z </li><li>0 à 9</li> <li>@# $ % ^ & * - _ ! + = [] {} & #124 ; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  Caractères non autorisés   |       <ul><li>Caractères Unicode</li><li>Espaces</li><li> **Mots de passe forts uniquement**: ne peut pas contenir un caractère point '.' précédant immédiatement la '@' symbole</li></ul> |
|   Restrictions de mot de passe | <ul><li>8 caractères minimales et 16 caractères au maximum</li><li>**Mots de passe forts uniquement**: 3 nécessite déconnecter 4 des opérations suivantes :<ul><li>Caractères minuscules</li><li>Caractères en majuscules</li><li>Nombres (0 à 9)</li><li>Symboles (voir restrictions de mot de passe ci-dessus)</li></ul></li></ul> |
| Durée d’expiration du mot de passe      | <ul><li>Valeur par défaut : **90** jours </li><li>Valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy à partir du Module Azure pour annuaire Active pour Windows PowerShell.</li></ul> |
| Notification d’expiration de mot de passe |  <ul><li>Valeur par défaut : **14** jours (avant l’expiration de mot de passe)</li><li>Valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy.</li></ul> |
| Expiration du mot de passe |  <ul><li>Valeur par défaut : **false** jours (indique que l’expiration mot de passe est activée) </li><li>Valeur peut être configurée pour les comptes d’utilisateurs individuels à l’aide de l’applet de commande Set-MsolUser. </li></ul> |
|  Historique de mot de passe  | Dernier mot de passe ne peuvent pas être utilisé à nouveau. |
|  Durée de l’historique de mot de passe | Pour toujours |
|  Verrouillage du compte | Après 10 connexion échecs (mauvais mot de passe), l’utilisateur sera verrouillé pendant une minute. Davantage incorrects connexion tentatives verrouille l’utilisateur pour améliorer les durées. |


## <a name="next-steps"></a>Étapes suivantes

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Gérer vos mots de passe où que vous soyez](active-directory-passwords.md)
* [Comment fonctionne la gestion de mot de passe](active-directory-passwords-how-it-works.md)
* [Prise en main pour la gestion de mot de passe](active-directory-passwords-getting-started.md)
* [Personnaliser la gestion de mot de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion de mot de passe](active-directory-passwords-best-practices.md)
* [Comment obtenir des informations opérationnelles avec des rapports de gestion de mot de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion mot de passe](active-directory-passwords-faq.md)
* [Résoudre les problèmes de gestion de mot de passe](active-directory-passwords-troubleshoot.md)
* [Pour en savoir plus](active-directory-passwords-learn-more.md)
