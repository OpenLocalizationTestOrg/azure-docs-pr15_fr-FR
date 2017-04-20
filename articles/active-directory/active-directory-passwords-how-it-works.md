<properties
    pageTitle="Mode de fonctionnement : gestion de mot de passe Azure Active Directory | Microsoft Azure"
    description="En savoir plus sur les différents composants de gestion de mot de passe Azure AD, y compris l’endroit où les utilisateurs enregistrer, réinitialiser et modifient leur mot de passe et l’emplacement dans lequel les administrateurs configurer, créer des rapports sur et activer la gestion des mots de passe Active Directory local."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="how-password-management-works"></a>Comment fonctionne la gestion de mot de passe

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Gestion des mots de passe dans Azure Active Directory se compose de plusieurs composants logiques qui sont décrites ci-dessous.  Cliquez sur chaque lien pour en savoir plus sur ce composant.

- [**Portail de Configuration de gestion de mot de passe**](#password-management-configuration-portal) – les administrateurs peuvent contrôler les différents aspects de la façon dont les mots de passe sont gérées dans leurs clients en accédant à l’onglet de configurer leur du répertoire dans le [Portail de gestion Azure](https://manage.windowsazure.com).
- [**Portail de l’enregistrement utilisateur**](#user-registration-portal) – les utilisateurs peuvent s’inscrire pour le mot de passe via ce portail web.
- [**Portail de réinitialiser votre mot de passe utilisateur**](#user-password-reset-portal) – les utilisateurs peuvent réinitialiser leur propre mot de passe à l’aide d’un certain nombre de défis associés à une autre conformément à la stratégie de réinitialisation du mot de passe contrôlés par l’administrateur
- [**Portail de modifier votre mot de passe utilisateur**](#user-password-change-portal) – les utilisateurs peuvent modifier leur mot de passe à tout moment en entrant leur ancien mot de passe et en sélectionnant un nouveau mot de passe à l’aide de ce portail web
- [**Rapports de gestion de mot de passe**](#password-management-reports) – les administrateurs peuvent afficher et analyser les activités de réinitialisation et d’enregistrement de mot de passe dans leur client en accédant à la section « Rapports activité » de l’onglet de « Rapports » de leur répertoire dans le [Portail de gestion Azure](https://manage.windowsazure.com)
- [**Mot de passe d’écriture différée composant d’Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) – les administrateurs peuvent éventuellement activer la fonctionnalité d’écriture différée de mot de passe lors de l’installation d’Azure AD Connect pour activer la gestion de fédérés ou mot de passe synchronisé les mots de passe utilisateur à partir du cloud.

## <a name="password-management-configuration-portal"></a>Portail de Configuration de gestion de mot de passe
Vous pouvez configurer des stratégies de gestion de mot de passe pour un répertoire spécifique à l’aide du [Portail de gestion Azure](https://manage.windowsazure.com) en accédant à la section **Stratégie de réinitialiser votre mot de passe utilisateur** dans l’onglet **configuration** de l’annuaire.  À partir de cette page de configuration, vous pouvez contrôler de nombreux aspects des modalités de gestion des mots de passe de votre organisation, notamment :

- Activation et désactivation du mot de passe pour tous les utilisateurs dans un répertoire
- Définition du nombre de défis (une ou deux) d’un utilisateur à suivre pour réinitialiser son mot de passe
- Définir les types spécifiques de problèmes que vous souhaitez activer pour les utilisateurs de votre organisation dans la liste ci-dessous :
 - Téléphone mobile (un code de vérification par le biais du texte ou un appel vocal)
 - Téléphone de bureau (un appel vocal)
 - Adresse de messagerie secondaire (un code de vérification par courrier électronique)
 - Questions sur la sécurité (authentification basée sur les connaissances)
- Définition du nombre de questions un utilisateur doit s’inscrire pour pouvoir utiliser la méthode d’authentification questions sécurité (visible uniquement si les questions de sécurité sont activées)
- Définition du nombre de questions un utilisateur doit fournir lors de la réinitialisation à utiliser la méthode d’authentification questions sécurité (visible uniquement si les questions de sécurité sont activées)
- À l’aide de questions de sécurité prédéfini, localisé, l’utilisateur peut choisir à utiliser lors de l’enregistrement pour votre mot de passe réinitialisé (visible uniquement si les questions de sécurité sont activées)
- Définir les questions de sécurité personnalisée qu’un utilisateur peut choisir d’utiliser lors de l’enregistrement de votre mot de passe réinitialisé (visible uniquement si les questions de sécurité sont activées)
- Obliger les utilisateurs à s’inscrire à un mot de passe lorsqu’ils accèdent à l’application Panneau d’accès au [http://myapps.microsoft.com](http://myapps.microsoft.com).
- Nécessitant aux utilisateurs de confirmer leurs données précédemment enregistré après un nombre configurable de jours se sont écoulés (visibles uniquement si l’inscription appliquée est activée)
- Fournir un support technique personnalisée messagerie ou une URL qui est affiché pour les utilisateurs au cas où ils disposent d’un problème de réinitialisation leur mot de passe
- Activation ou désactivation de la fonctionnalité d’écriture différée de mot de passe (lorsque l’écriture différée de mot de passe a été déployée à l’aide de AAD se connecter)
- Affichage de l’état de l’agent de mot de passe d’écriture différée (lorsque l’écriture différée de mot de passe a été déployée à l’aide de AAD se connecter)
- Activation des notifications par courrier électronique aux utilisateurs lors de leur propre mot de passe réinitialisé (figurant dans la section **Notifications** du [Portail de gestion Azure](https://manage.windowsazure.com))
- Activation des notifications par courrier électronique aux administrateurs lorsque d’autres administrateurs réinitialiser leur propre mot de passe (figurant dans la section **Notifications** du [Portail de gestion Azure](https://manage.windowsazure.com)
- Marque le mot de passe réinitialisé portail et mot de passe réinitialisé des messages électroniques avec le logo et le nom de votre organisation à l’aide de la fonctionnalité de personnalisation (figurant dans la section **Propriétés de l’annuaire** du [Portail de gestion Azure](https://manage.windowsazure.com) de personnalisation de client

Pour en savoir plus sur la configuration de la gestion de mot de passe de votre organisation, voir [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portail de l’enregistrement utilisateur
Avant que les utilisateurs sont en mesure d’utiliser le mot de passe réinitialisé, leurs comptes d’utilisateurs cloud doivent mis à jour avec les données d’authentification correctes pour vous assurer qu’ils peuvent passer par le nombre de défis associés à une réinitialisation du mot de passe défini par l’administrateur approprié.  Les administrateurs peuvent également définir ces informations d’authentification part de leur utilisateur à l’aide de portails web Azure ou Office, DirSync / Azure AD Connect ou Windows PowerShell.

Toutefois, si vous préférez que vos utilisateurs enregistrer leurs propres données, nous fournissons également une page web que les utilisateurs peuvent aller pour fournir ces informations.  Cette page permet aux utilisateurs spécifier les informations d’authentification selon le mot de passe réinitialisé stratégies qui ont été activés dans leur organisation.  Une fois ces données vérifiées, il est stocké dans leur compte d’utilisateur cloud à utiliser pour la récupération de compte ultérieurement. Voici ce à quoi l’inscription portail ressemble :

  ![][001]

Pour plus d’informations, voir [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md) et [meilleures pratiques : Azure AD Password Management](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portail de réinitialisation de mot de passe utilisateur
Une fois que vous avez activé le mot de passe libre-service réinitialiser, configurer la stratégie de réinitialisation de mot de passe libre-service de votre organisation et veiller à ce que vos utilisateurs disposent les données de contact appropriées dans le répertoire, les utilisateurs de votre organisation seront en mesure de réinitialiser leur mot de passe automatiquement à partir de n’importe quelle page web qui utilise un compte professionnel ou scolaire pour se connecter (par exemple, [portal.microsoftonline.com](https://portal.microsoftonline.com)). Dans les pages telles que celles-ci, les utilisateurs verront une **ne peut pas accéder à votre compte ?** lien.

  ![][002]

Cliquez sur ce lien lancera le portail de réinitialisation du mot de passe libre-service.

  ![][003]

Pour en savoir plus sur la façon dont les utilisateurs peuvent réinitialiser leur propre mot de passe, voir [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portail de changement de mot de passe utilisateur
Si les utilisateurs souhaitent modifier leur mot de passe, ils peuvent le faire à l’aide du portail de changement de mot de passe à tout moment.  Les utilisateurs peuvent accéder au portail de changement de mot de passe via la page de profil panneau d’accès ou en cliquant sur le lien « Modifier le mot de passe » à partir d’applications Office 365.  Dans le cas lors de l’expiration de leur mot de passe, les utilisateurs seront également invités à modifier automatiquement lorsque vous vous connectez.

  ![][004]

Dans les deux cas, si votre mot de passe en écriture différée a été activée et l’utilisateur fédérée soit ou habituellement de la synchronisation de mot de passe, ces mots de passe modifiés sont réécrits à Active Directory local. Voici ce que le mot de passe modifier portail ressemble :

  ![][005]

Pour en savoir plus sur la façon dont les utilisateurs peuvent modifier leur mot de passe Active Directory local, voir [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Rapports sur la gestion de mot de passe
En accédant à l’onglet **rapports** , vous recherchez dans la section **Journaux d’activité** , vous verrez deux rapports de gestion de mot de passe : **activité de réinitialisation du mot de passe** et **mot de passe réinitialisé l’activité d’enregistrement**.  À l’aide de ces deux rapports, vous pouvez obtenir un affichage des utilisateurs inscrit et à l’aide du mot de passe de votre organisation. Voici à quoi ressemblent ces rapports dans le [Portail de gestion Azure](https://manage.windowsazure.com):

  ![][006]

Pour plus d’informations, voir [obtenir des informations : rapports sur la gestion de mot de passe Azure AD](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Composant d’écriture différée de mot de passe d’Azure AD Connect
Si les mots de passe des utilisateurs de votre organisation issus de votre environnement local (soit via la fédération ou la synchronisation de mot de passe), vous pouvez installer la dernière version d’Azure AD Connect pour activer la mise à jour de ces mots de passe directement à partir du cloud.  Cela signifie que lorsque vos utilisateurs oublient ou want modifier leur mot de passe AD, qu’il peut faire donc directement à partir du web.  Voici où trouver d’écriture différée de mot de passe dans l’Assistant installation Azure AD Connect :

  ![][007]

Pour plus d’informations sur Azure AD Connect, voir [prise en main : Azure AD Connect](active-directory-aadconnect.md). Pour plus d’informations sur le mot de passe d’écriture différée, voir [mise en route : Azure AD Password Management](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers votre mot de passe réinitialisé documentation
Voici des liens vers toutes les pages de documentation de réinitialisation du mot de passe Azure AD :

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Prise en main**](active-directory-passwords-getting-started.md) - obtenir des informations pour vous permettre aux utilisateurs de réinitialiser et modifier leur mot de passe cloud ou en local
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’apparence et la convivialité et le comportement du service aux besoins de votre organisation
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) - Apprenez à déployer rapidement et efficacement gérer les mots de passe de votre organisation
* [**Obtenir des informations**](active-directory-passwords-get-insights.md) - en savoir plus sur notre fonctions intégrées de reporting
* [**Forum aux questions**](active-directory-passwords-faq.md) - obtenir des réponses aux questions fréquemment posées
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - accédez profondeur dans les détails techniques de fonctionnement du service



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
