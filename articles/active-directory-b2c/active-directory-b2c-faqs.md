<properties
    pageTitle="B2C Azure Active Directory : Forum aux questions | Microsoft Azure"
    description="Forum aux questions sur Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>B2C Azure Active Directory : Forum aux questions

Cette page fournit des réponses à des questions fréquemment posées sur la B2C Azure Active Directory (AD Azure). Conserver la vérification revenir les mises à jour.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Puis-je utiliser des fonctionnalités Azure AD B2C dans mon client Azure AD existant, en fonction des employés ?

Actuellement Azure AD B2C fonctionnalités ne peut pas être activées dans votre client Azure Active Directory existant. Nous vous recommandons de créer un client distinct pour utiliser les fonctionnalités Azure AD B2C pour gérer vos consommateurs.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Puis-je utiliser Azure AD B2C pour fournir une connexion sociale (Facebook et Google +) dans Office 365 ?

Azure AD B2C ne peuvent pas être utilisés avec Microsoft Office 365. En règle générale, il ne peut pas être utilisé pour fournir une authentification pour toutes les applications SaaS (Office 365, Salesforce, journée de travail, etc.). Il fournit la gestion des identités et des accès uniquement pour les applications web pour les consommateurs et mobiles et s’applique pas aux scénarios employé ou un partenaire.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Quels sont les comptes locaux dans Azure AD B2C ? Comment sont-ils différents comptes professionnel ou scolaire dans Azure AD ?

Dans un client Azure AD, chaque utilisateur du client (sauf les utilisateurs disposant de comptes Microsoft existants) se connecte à l’aide d’une adresse de messagerie de l’écran `<xyz>@<tenant domain>`, où `<tenant domain>` est un des domaines vérifiés dans le client ou l’initiale `<...>.onmicrosoft.com` domaine. Ce type de compte est un compte professionnel ou scolaire.

Dans un client Azure AD B2C, la plupart des applications que l’utilisateur à se connecter avec n’importe quelle adresse de messagerie arbitraire (par exemple, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Ce type de compte est un compte local. Aujourd'hui, nous prennent également en charge noms d’utilisateur arbitraire (chaînes de texte brut uniquement) en tant que comptes locaux (par exemple, Jean, bob, sarah ou jim). Vous pouvez choisir une de ces deux types de compte local dans le service d’Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Les fournisseurs d’identité social vous prennent en charge maintenant ? Quels sont ceux que vous prévoyez prendre en charge à l’avenir ?

Nous pris en charge que Facebook, Google +, LinkedIn et Amazon. Nous allons ajouter prise en charge pour les autres fournisseurs d’identité social populaires en fonction de la demande du client.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Puis-je configurer des étendues pour plus d’informations sur les consommateurs collectez les différents fournisseurs d’identité mise en réseau ?

Non, mais cette fonctionnalité est activée notre feuille de route. Les étendues par défaut utilisés pour notre jeu de fournisseurs d’identité social pris en charge sont :

- Facebook : messagerie
- Google + : messagerie
- Compte Microsoft : profil de messagerie openid
- Amazon : profil
- LinkedIn : r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Mon application doit-il être exécuté sur Azure pour qu’il fonctionne avec Azure AD B2C ?

Non, vous pouvez héberger votre application en tout lieu (dans le cloud ou en local). Tout ce qu’il doit interagir avec Azure AD B2C est la fonctionnalité permettant d’envoyer et recevoir les requêtes HTTP sur les points de terminaison accessible au public.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>J’ai plusieurs clients Azure AD B2C. Comment puis-je gérer les sur le portail Azure ?

Chaque client Azure AD B2C possède son propre carte de fonctionnalités B2C sur le portail Azure. Voir [Azure AD B2C : enregistrer votre application](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) pour découvrir comment vous pouvez accéder à la carte de fonctionnalités B2C d’un client spécifique dans le portail Azure. Basculement entre des répertoires Azure AD B2C sur le portail Azure permet de solidariser pas vos fonctionnalités B2C carte ouvrir sur la plupart des navigateurs.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Comment personnaliser les messages électroniques de vérification (le contenu et la » à partir de : « champ) envoyé par Azure AD B2C ?

Vous pouvez utiliser la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md) pour personnaliser le contenu des messages électroniques de vérification. Plus précisément, ces deux éléments du courrier électronique peuvent être personnalisées :

- **Bannière Logo**: indiqué dans le coin inférieur droit.
- **Couleur d’arrière-plan**: apparaît en haut.

    ![Capture d’écran d’un message de vérification personnalisée](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La signature de courrier électronique contient nom locataire B2C que vous avez fourni lorsque vous avez créé tout d’abord le client B2C. Vous pouvez modifier le nom à l’aide de ces instructions :

- Se connecter au [portail classique Azure](https://manage.windowsazure.com/) l’en tant que l’administrateur d’abonnement.
- Accédez à votre client B2C.
- Cliquez sur l’onglet **configurer** .
- Modifier le champ **nom** sous la section **Propriétés de l’annuaire** .
- Cliquez sur **Enregistrer** dans la partie inférieure de la page.

Il n’existe actuellement aucun moyen de modifier la « de : « champ sur le message électronique. Si vous êtes intéressé dans cette fonctionnalité et entièrement personnaliser le corps du message de vérification, voter pour la fonctionnalité sur [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Comment puis-je migrer mes noms d’utilisateur existants, les mots de passe et des profils de ma base de données à Azure AD B2C ?

Vous pouvez utiliser l’API Azure AD Graph pour rédiger votre outil de migration. Voir l' [exemple de graphique d’API](active-directory-b2c-devquickstarts-graph-dotnet.md) pour plus d’informations. Nous proposons différentes options de migration et outils-de-l’emploi à l’avenir.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quelle stratégie de mot de passe est utilisé pour les comptes locaux dans Azure AD B2C ?

La stratégie de mot de passe Azure AD B2C pour les comptes locaux est basée sur la stratégie pour Azure AD. Azure AD B2C d’inscription, inscription ou connexion et votre mot de passe réinitialisé stratégies utilise la puissance de mot de passe « fort » et n’expire jamais les mots de passe. Lisez la [stratégie de mot de passe Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) pour plus d’informations.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Puis-je utiliser Azure AD Connect pour migrer les identités qui sont stockées sur mon locale d’Active Directory pour Azure AD B2C ?

Non, Azure AD Connect n’est pas conçu pour fonctionner avec Azure AD B2C. Nous proposons différentes options de migration et outils-de-l’emploi à l’avenir.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C fonctionne avec des systèmes tels que Microsoft Dynamics CRM ?

Pas actuellement. Intégration de ces systèmes est notre feuille de route.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Ne prend Azure AD B2C avec SharePoint en local 2016 ou une version antérieure ?

Pas actuellement. Azure AD B2C n’est pas prise en charge des jetons SAML 1.1 portails et applications de commerce électronique basées sur vous avez besoin de SharePoint en local. Notez que Azure AD B2C n’est pas conçu pour le SharePoint externes partenaire scénario de partage ; voir [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) à la place.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dois-je utiliser Azure AD B2C ou B2B pour gérer les identités externes ?

Lisez cet article sur les [identités externes](../active-directory/active-directory-b2b-compare-external-identities.md) pour en savoir plus sur l’application les fonctionnalités appropriées à vos scénarios identité externes.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Les rapports et les fonctionnalités d’audit Azure AD B2C propose-t-il ? Ils sont les mêmes qu’Azure AD Premium ?

Non, Azure AD B2C ne prend pas en charge le même ensemble de rapports que Azure AD Premium. Azure AD B2C doit publier reporting de base et d’audit API plus rapidement.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Puis-je localiser l’interface utilisateur de pages pris en charge par Azure AD B2C ? Quelles langues sont prises en charge ?

Pour l’instant, Azure AD B2C est optimisé pour l’anglais uniquement. Nous prévoyons à déployer les fonctionnalités de localisation dès que possible.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Puis-je utiliser mon propre URL sur Mes pages d’abonnement et connexion pris en charge par Azure AD B2C ? Par exemple, puis-je modifier l’URL à partir de login.microsoftonline.com à login.contoso.com ?

Pas actuellement. Cette fonctionnalité est notre feuille de route. Notez également que la vérification de votre domaine dans l’onglet **domaines** de votre client dans le portail classique Azure se comportent pas ainsi.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Comment supprimer mon client Azure AD B2C ?

Suivez ces étapes pour supprimer votre client Azure AD B2C :

- Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
- Accédez aux cartes de **toutes les stratégies** , les **fournisseurs d’identité** et les **Applications**et supprimez toutes les entrées dans chacun d’eux.
- Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) en tant que l’administrateur d’abonnement. (Cela est le même travail ou compte scolaire ou le même compte Microsoft que vous avez utilisé pour vous inscrire Azure).
- Accédez à l’extension Active Directory sur la gauche, puis cliquez sur votre client B2C.
- Cliquez sur l’onglet **utilisateurs** .
- Sélectionnez chaque utilisateur à son tour (exclure l’utilisateur que vous êtes connecté en tant que, par exemple, l’administrateur d’abonnement). Cliquez sur **Supprimer** dans la partie inférieure de la page et cliquez sur **Oui** lorsque vous y êtes invité.
- Cliquez sur l’onglet **Applications** .
- Sélectionnez **Applications propriétaire de mon entreprise** dans le champ de liste déroulante **Afficher** , cliquez sur la coche.
- Vous verrez une application appelée **b2c-extensions-app** présentées ci-après. Cliquez sur **Supprimer** dans la partie inférieure de la page et cliquez sur **Oui** lorsque vous y êtes invité.
- Naviguez jusqu'à l’extension Active Directory à nouveau et sélectionnez votre client B2C.
- Cliquez sur **Supprimer** dans la partie inférieure de la page. Suivez les instructions à l’écran pour terminer le processus.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Puis-je obtenir Azure AD B2C dans le cadre d’entreprise mobilité Suite ?

Non, Azure AD B2C est un paiement service Azure et ne fait pas partie d’entreprise mobilité Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Comment puis-je signaler un problème avec Azure AD B2C ?

Voir [fichier prend en charge les demandes d’Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="more-information"></a>Plus d’informations

Vous pouvez également passer en revue [les contraintes, restrictions et limitations du service](active-directory-b2c-limitations.md)en cours.
