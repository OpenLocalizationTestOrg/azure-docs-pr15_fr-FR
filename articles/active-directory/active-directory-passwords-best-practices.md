<properties
    pageTitle="Meilleures pratiques : Azure AD Password Management | Microsoft Azure"
    description="Meilleures pratiques de déploiement et l’utilisation, exemple de documentation de l’utilisateur final et guides de formation pour la gestion de mot de passe dans Azure Active Directory."
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

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Déploiement de la gestion de mot de passe et la formation des utilisateurs à l’utiliser

> [AZURE.IMPORTANT] **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

Après que l’activation du mot de passe réinitialisé, l’étape suivante, que vous devez emmener consiste à rendre les utilisateurs au moyen du service de votre organisation. Pour ce faire, vous devez pour vous assurer que vos utilisateurs sont configurés pour utiliser le service correctement et également que vos utilisateurs disposent de la formation dont elles ont besoin pour réussir dans la gestion de leur propre mot de passe. Cet article explique vous les concepts suivants :

* [**Comment obtenir vos utilisateurs configurés pour la gestion de mot de passe**](#how-to-get-users-configured-for-password-reset)
  * [Création d’un compte configuré pour réinitialiser votre mot de passe](#what-makes-an-account-configured)
  * [Méthodes que vous pouvez pour renseigner les données d’authentification](#ways-to-populate-authentication-data)
* [**Les meilleures façons à déployer du mot de passe pour votre organisation**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Déploiement des e-mails + exemple messagerie électronique](#email-based-rollout)
  * [Créer votre propre portail de gestion de mot de passe personnalisé pour vos utilisateurs](#creating-your-own-password-portal)
  * [Comment utiliser l’inscription appliquée pour obliger les utilisateurs à s’inscrire à se connecter](#using-enforced-registration)
  * [Comment télécharger des données d’authentification pour les comptes d’utilisateurs](#uploading-data-yourself)
* [**Exemple d’utilisateur et de supports de cours de formation prise en charge (bientôt disponible)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Comment rendre les utilisateurs configurés pour réinitialiser votre mot de passe
Cette section décrit vous différentes méthodes par lequel vous pouvez vous assurer que chaque utilisateur de votre organisation peut utiliser libre-service du mot de passe efficace au cas où ils oublient son mot de passe.

### <a name="what-makes-an-account-configured"></a>Création d’un compte configuré
Un utilisateur peut utiliser réinitialisation du mot de passe, **toutes les** conditions suivantes doivent être remplies :

1.  Réinitialisation du mot de passe doit être activée dans l’annuaire.  Apprenez à activer le mot de passe en lisant l’article [permettre aux utilisateurs de réinitialiser les mots de passe AD Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [permettre aux utilisateurs de réinitialiser ou modifier leur mot de passe AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
2.  L’utilisateur doit être une licence.
 - Pour les utilisateurs du cloud, l’utilisateur doit avoir **une licence Office 365 payant**, ou une **Base AAD** ou **Premium AAD licence** attribuée.
 - Pour local utilisateurs (fédéré ou hachage synchronisé), l’utilisateur **doit disposer d’une licence AAD Premium affectée**.
3.  L’utilisateur doit disposer de l' **ensemble minimal de données d’authentification définies** conformément à la stratégie de réinitialisation du mot de passe actuel.
 - Données d’authentification sont considéré comme définie si le champ correspondant dans le répertoire contient des données correcte.
 - Un jeu de données d’authentification est défini à **au moins un** des options d’authentification activés si une stratégie d’une grille est configurée, ou au **moins deux** des options d’authentification activés si une stratégie deux porte est configurée.
4.  Si l’utilisateur utilise un compte local, puis [D’écriture différée de mot de passe](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) doit être activé et activé

### <a name="ways-to-populate-authentication-data"></a>Manières de remplir des données d’authentification
Vous disposez de plusieurs options sur la spécification des données pour les utilisateurs de votre organisation à utiliser pour réinitialiser votre mot de passe.

- Modifier des utilisateurs dans le [Portail de gestion Azure](https://manage.windowsazure.com) ou le [Portail d’administration d’Office 365](https://portal.microsoftonline.com)
- Utiliser la synchronisation Azure Active Directory pour synchroniser les propriétés de l’utilisateur dans Active Directory Azure à partir d’un domaine Active Directory local
- Utiliser Windows PowerShell pour modifier les propriétés de l’utilisateur en [suivant les étapes décrites ici](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Permettre aux utilisateurs d’enregistrer leurs propres données en les guider au portail d’inscription à [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Obliger les utilisateurs à s’inscrire à un mot de passe lorsqu’ils se connecter à leur compte Azure AD en définissant la [**obliger les utilisateurs à enregistrer lorsque vous vous connectez ?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) option de configuration sur **Oui**.

Les utilisateurs doivent s’inscrire pas de mot de passe pour le système fonctionne.  Par exemple, si vous avez mobile existant ou des numéros de téléphone de bureau dans votre annuaire local, vous pouvez les synchroniser dans Azure Active Directory et nous allons utiliser leur mot de passe réinitialisé automatiquement.

Vous pouvez également lire des informations supplémentaires sur la [manière dont les données sont utilisées par mot de passe réinitialisé](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) et la [comment vous pouvez remplir les champs individuels authentification avec PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>Quelle est la meilleure façon de déploiement de mot de passe pour les utilisateurs ?
Voici les étapes de déploiement général pour réinitialiser votre mot de passe :

1.  Activer le mot de passe dans votre annuaire en accédant à l’onglet **configurer** dans le [Portail de gestion Azure](https://manage.windowsazure.com) et si vous sélectionnez **Oui** pour que l’option **Les utilisateurs activés pour réinitialiser votre mot de passe** .
2.  Attribuer des licences appropriés à chaque utilisateur auquel vous voulez proposer du mot de passe dans l’en accédant à l’onglet **licences** dans le [Portail de gestion Azure](https://manage.windowsazure.com).
3.  Vous pouvez également restreindre du mot de passe à un groupe d’utilisateurs à déployer la fonctionnalité lentement au fil du temps en définissant le bouton bascule **Limiter l’accès au mot de passe réinitialisé** sur **Oui** et sélection d’un groupe de sécurité pour activer la réinitialisation de mot de passe (Remarque : si ces utilisateurs doivent avoir toutes les licences qui leur sont affectées).
4.  Invitez vos utilisateurs à utiliser le mot de passe en soit leur envoyant un message électronique demandant à enregistrer, l’activation de mise en œuvre d’enregistrement dans le panneau d’accès ou en le téléchargeant les données d’authentification appropriée pour les utilisateurs via le [Portail de gestion Azure](https://manage.windowsazure.com), PowerShell ou DirSync.  Obtenir plus d’informations sur ce problème sont fournis ci-dessous.
5.  Dans le temps, passez en revue les utilisateurs de votre inscription en accédant à l’onglet Rapports et la consultation du rapport [**d’Activité de l’inscription de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) .
6.  Une fois qu’un bon nombre d’utilisateurs ont enregistré, visionnez-les utiliser du mot de passe en accédant à l’onglet Rapports et la consultation du rapport [**d’Activité de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-activity) .

Il existe plusieurs méthodes pour informer les utilisateurs qu’ils peuvent s’inscrire et utiliser du mot de passe de votre organisation.  Ils sont détaillées ci-dessous.

### <a name="email-based-rollout"></a>Déploiement des e-mails
L’approche la plus simple pour informer vos utilisateurs à s’inscrire ou utilisez le mot de passe réinitialisé est peut-être en leur envoyant un message électronique demandant les à le faire.  Voici un modèle que vous pouvez utiliser pour effectuer cette action.  N’hésitez pas à remplacer les couleurs / logos avec celles de vos propres choisir de le personnaliser selon vos besoins.

  ![][001]

Vous pouvez [Télécharger le modèle de courrier électronique ici](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Créez votre propre portail de mot de passe
Une stratégie qui fonctionne bien pour les clients plus importants déploiement de fonctionnalités de gestion de mot de passe consiste à créer un seul « portail de mot de passe « que vos utilisateurs peuvent utiliser pour gérer tous les éléments liés à leur mot de passe dans un emplacement unique.  

Nombre de nos clients plus grand choix pour créer une racine des entrées DNS, comme https://passwords.contoso.com avec des liens vers le mot de passe Azure AD réinitialiser portail, le portail de l’enregistrement de réinitialisation du mot de passe, modifier le mot de passe de pages.  Ainsi, dans les communications par courrier électronique ou un prospectus que vous envoyez, vous pouvez inclure un simple, facile à retenir, URL que les utilisateurs peuvent aller lorsqu’ils ont une seconde pour commencer à utiliser le service.

Pour commencer ici, nous avez créé une page simple qui utilise les dernière version injoignable interface utilisateur Création modèles et travaillera sur tous les navigateurs et les appareils mobiles.

  ![][007]

Vous pouvez [Télécharger le modèle de site Web ici](https://github.com/kenhoff/password-reset-page).  Nous vous recommandons de personnaliser le logo et les couleurs pour qu’il soit nécessaire de votre organisation.

### <a name="using-enforced-registration"></a>À l’aide de l’inscription appliquée
Si vous souhaitez que vos utilisateurs à s’inscrire eux-mêmes réinitialiser un mot de passe, vous pouvez également forcer à enregistrer lorsqu’ils se connecter pour le panneau d’accès au [http://myapps.microsoft.com](http://myapps.microsoft.com).  Vous pouvez activer cette option à partir de l’onglet **configuration** de votre annuaire en activant l’option **Demander aux utilisateurs de Registre lorsque vous vous connectez le panneau d’accès** .  

Vous pouvez également définir ou non qu’ils seront invités à réenregistrer après une période donnée en modifiant l’option **nombre de jours avant que les utilisateurs doivent confirmer leurs données de contact** à une valeur non nulle. Pour plus d’informations, voir [Personnaliser le comportement de gestion de mot de passe utilisateur](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Une fois que vous activez cette option, lorsque les utilisateurs se connectent au panneau d’accès, ils verront une fenêtre indépendante les informant que leur administrateur a elles sont nécessaires pour vérifier ses informations de contact. Il peut l’utiliser pour réinitialiser leur mot de passe si elles jamais perdent l’accès à leur propre compte.

  ![][003]

En cliquant sur **Vérifier maintenant** fait apparaître leur **mot de passe réinitialisé portail de l’enregistrement** auprès de [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) et a besoin d’enregistrer.  Inscription via cette méthode peut être désactivée en cliquant sur le bouton **Annuler** ou en fermant la fenêtre, mais les utilisateurs reçoivent un rappel à chaque fois qu’ils se connecter si elles ne s’enregistrent pas.

  ![][004]

### <a name="uploading-data-yourself"></a>Téléchargement de données
Si vous souhaitez télécharger des données d’authentification vous-même, les utilisateurs doivent s’inscrire pas de mot de passe avant de pouvoir réinitialiser les mots de passe.  Dans la mesure où les utilisateurs ont les données d’authentification définies dans leur propre compte qui remplit le mot de passe réinitialisé stratégie que vous avez définie, puis les utilisateurs seront en mesure de réinitialiser les mots de passe.

Pour savoir quelles propriétés pouvant être définies par le biais AAD se connecter ou Windows PowerShell, consultez [quelles données sont utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Vous pouvez télécharger les données d’authentification via le [Portail de gestion Azure](https://manage.windowsazure.com) en suivant les étapes ci-dessous :

1.  Naviguez jusqu'à votre répertoire dans l' **extension Active Directory** dans le [Portail de gestion Azure](https://manage.windowsazure.com).
2.  Cliquez sur l’onglet **utilisateurs** .
3.  Sélectionnez l’utilisateur que qui vous intéresse dans la liste.
4.  Dans le premier onglet, vous trouverez **Messagerie de secours**, qui peut être utilisé en tant que propriété pour activer la réinitialisation de mot de passe.

    ![][005]

5.  Cliquez sur l’onglet **Informations de travail** .
6.  Dans cette page, vous trouverez **Téléphone de bureau**, **téléphone Mobile**, le **Téléphone de l’authentification**et **L’authentification**.  Ces propriétés peuvent également être définies pour permettre à un utilisateur réinitialiser son mot de passe.

    ![][006]

Découvrez [quelles données sont utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) pour voir comment chacune de ces propriétés peut être utilisé.

Voir [comment accéder à votre mot de passe rétablir les données pour vos utilisateurs à partir de PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) pour voir comment vous pouvez lire et définir ces données avec PowerShell.

## <a name="sample-training-materials"></a>Supports de cours de formation exemple
Nous travaillons sur supports de formation exemple que vous pouvez utiliser pour votre service informatique et vos utilisateurs pour être opérationnel rapidement comment déployer et utiliser la réinitialisation du mot de passe.  Restez en ligne !


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Des liens vers votre mot de passe réinitialisé documentation
Voici des liens vers toutes les pages de documentation de réinitialisation du mot de passe Azure AD :

* **Vous êtes ici parce que vous rencontrez des problèmes en vous connectant ?** Si c’est le cas, [Voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Mode de fonctionnement**](active-directory-passwords-how-it-works.md) - en savoir plus sur les différents composants de service et de six chacun a
* [**Prise en main**](active-directory-passwords-getting-started.md) - obtenir des informations pour vous permettre aux utilisateurs de réinitialiser et modifier leur mot de passe cloud ou en local
* [**Personnaliser**](active-directory-passwords-customize.md) - Apprenez à personnaliser l’apparence et la convivialité et le comportement du service aux besoins de votre organisation
* [**Obtenir des informations**](active-directory-passwords-get-insights.md) - en savoir plus sur notre fonctions intégrées de reporting
* [**Forum aux questions**](active-directory-passwords-faq.md) - obtenir des réponses aux questions fréquemment posées
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) - Apprenez à rapidement résoudre les problèmes avec le service
* [**En savoir plus**](active-directory-passwords-learn-more.md) - accédez profondeur dans les détails techniques de fonctionnement du service



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
