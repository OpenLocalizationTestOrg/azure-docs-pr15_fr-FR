<properties
    pageTitle="Azure B2C annuaire Active : Restrictions et Limitations | Microsoft Azure"
    description="Une liste des limitations et restrictions avec Azure Active Directory B2C"
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
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure B2C annuaire Active : Restrictions et Limitations

Il existe plusieurs fonctionnalités et fonctionnalités de B2C Azure Active Directory (AD Azure) qui ne sont pas pris en charge. La plupart de ces problèmes connus et limitations seront adressées à l’avenir, mais vous devez être conscientes si vous créez des applications pour les consommateurs à l’aide de Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problèmes lors de la création de clients Azure AD B2C

Si vous rencontrez des problèmes lors de la [Création d’un client Azure AD B2C](active-directory-b2c-get-started.md), voir [créer un client Azure AD ou un client Azure AD B2C--problèmes et solutions](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

Notez qu’il existe des connus problèmes lorsque vous supprimez un client B2C existant puis recréez en utilisant le même nom de domaine. Vous devez créer un client B2C avec un nom de domaine différent.

## <a name="note-about-b2c-tenant-quotas"></a>Remarque sur les quotas de client B2C

Par défaut, le nombre d’utilisateurs dans un client B2C est limité à 50 000 utilisateurs. Si vous avez besoin de déclencher le quota de votre client B2C, vous devez contacter le support technique.

## <a name="branding-issues-on-verification-email"></a>Problèmes de courrier électronique de vérification de personnalisation

Le message de vérification par défaut contient la personnalisation de Microsoft. Nous entraînera sa suppression à l’avenir. Pour l’instant, vous pouvez la supprimer en utilisant la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restrictions sur les applications

Les types d’applications suivants ne sont actuellement pas pris en charge dans Azure AD B2C. Pour obtenir une description des types d’applications pris en charge, reportez-vous à [Azure AD B2C : Types d’applications](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Applications de Page unique (JavaScript)

De nombreuses applications modernes ont une seule Page Application (SPA) frontale écrit principalement dans JavaScript et utilise souvent une structure SPA tels que AngularJS, Ember.js, Durandal, etc.. Ce flux n’est pas encore disponible dans Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Processus / côté serveur applications

Les applications qui contiennent des processus longs ou qui fonctionnent sans la présence d’un utilisateur doivent également un moyen d’accéder aux ressources sécurisés, tels que des API Web. Ces applications peuvent s’authentifier et obtenez des jetons à l’aide de l’identité l’application (plutôt qu’identité déléguée consommateurs) dans le [flux des informations d’identification des clients OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Ce flux n’est pas encore disponible dans Azure AD B2C, afin que pour l’instant, les applications peuvent obtenir des jetons qu’une fois un flux de connexion consommateur interactifs s’est produite.

### <a name="standalone-web-apis"></a>API Web autonome

Dans la B2C Azure AD, vous avez la possibilité de [créer une API Web qui est sécurisé à l’aide de jetons OAuth 2.0](active-directory-b2c-apps.md#web-apis). Toutefois, cette API Web uniquement seront en mesure de recevoir des jetons à partir d’un client partageant le même ID d’Application. Création d’une API Web accessible à partir de plusieurs clients différents n’est pas pris en charge.

### <a name="web-api-chains-on-behalf-of"></a>Web chaînes API (à la place-de)

Architectures de nombreux inclure une API Web qui doit effectuer des appels d’une autre API Web en aval, les deux sécurisé par Azure AD B2C. Ce scénario est courant dans les clients natives qui ont une API Web principale, qui à son tour appelle un service en ligne Microsoft tels que l’API Azure AD Graph.

Ce scénario API Web chaîne peut être pris en charge à l’aide de l’octroi d’informations d’identification OAuth 2.0 Jwt porteur, également appelé le flux de suite à la place de. Toutefois, le flux de suite à la place de n’est pas actuellement implémenté dans les AD B2C Azure.

## <a name="restriction-on-libraries-and-sdks"></a>Restriction sur les bibliothèques et SDK

Le jeu de bibliothèques Microsoft pris en charge fonctionnant Azure AD B2C est très limité pour l’instant. Nous avons prise en charge de .NET Framework en fonction web applications et services, ainsi que NodeJS web applications et services.  Nous avons également une bibliothèque de client preview .NET appelée MSAL qui peut être utilisé avec Azure AD B2C dans Windows et d’autres applications .NET.

Nous n’avons pas actuellement prise en charge d’autres langues ou plates-formes, y compris iOS et Android de bibliothèque.  Si vous souhaitez créer sur une autre plate-forme que ceux présentés ci-dessus, nous recommandons d’utiliser un kit de développement open source, faisant référence à notre [OAuth 2.0 et OpenID connecter protocole référence](active-directory-b2c-reference-protocols.md) comme bon vous semble.  Azure AD B2C met en œuvre OAuth & OpenID vous connecter, ce qui permet d’utiliser une bibliothèque OAuth ou OpenID connecter générique pour l’intégration.

Notre iOS et Android démarrage rapide didacticiels utilisent bibliothèques open source qui nous avons testé compatibilité avec Azure AD B2C.  Toutes nos didacticiels de démarrage rapide sont disponibles dans la section consacrée à la [mise en route](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restriction sur les protocoles

Azure AD B2C prend en charge OpenID se connecter et OAuth 2.0. Toutefois, pas toutes les fonctionnalités et les capacités de chaque protocole ont été apportées. Pour mieux comprendre l’étendue de la fonctionnalité de protocole pris en charge dans Azure AD B2C, lisez notre [OpenID se connecter et référence du protocole OAuth 2.0](active-directory-b2c-reference-protocols.md). Prise en charge des protocoles SAML et Web Fed n’est pas disponible.

## <a name="restriction-on-tokens"></a>Restriction sur les jetons

La plupart des jetons émis par Azure AD B2C sont implémentées comme JSON Web jetons ou JWTs. Toutefois, toutes les informations contenues dans JWTs (appelés « revendications ») sont assez comme il doit être ou est manquant. Voici quelques exemples « sub » et les revendications « preferred_username ».  En tant que les valeurs, un format ou un sens de changement de revendications au fil du temps, jetons pour vos stratégies existants ne seront pas affectés : vous pouvez s’appuient sur les valeurs correspondantes dans les applications de production.  Lorsque les valeurs changent, nous vous donne la possibilité de configurer ces modifications pour chacun de vos stratégies.  Pour mieux comprendre les jetons émis actuellement par le service Azure AD B2C, lisez notre [référence de jeton](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restriction sur les groupes imbriqués

Appartenance aux groupes imbriqués ne sont pas pris en charge dans Azure AD B2C clients. Nous ne prévoyez ajouter cette fonctionnalité.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restriction fonctionnalité requête différentielle sur API Azure AD Graph

La [fonctionnalité de requête différentielle sur API Azure AD graphique](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) n’est pas pris en charge dans Azure AD B2C clients. Il s’agit sur notre feuille de route à long terme.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problèmes avec la gestion des utilisateurs dans le portail classique Azure

Fonctionnalités B2C sont accessibles dans le portail Azure. Toutefois, vous pouvez utiliser le portail classique Azure pour accéder aux autres fonctionnalités de client, y compris la gestion des utilisateurs. Il existe actuellement quelques problèmes connus avec la gestion des utilisateurs (onglet **utilisateurs** ) sur le portail classique Azure :

- Pour un utilisateur du compte local (par exemple, un client qui s’inscrit avec une adresse de messagerie et mot de passe, ou un nom d’utilisateur et mot de passe), le champ **Nom d’utilisateur** ne correspond pas à la connexion à identificateur (nom d’utilisateur ou adresse de messagerie) qui a été utilisée pendant l’inscription. C’est parce que le champ affiché dans le portail classique Azure est en réalité l’utilisateur nom Principal (UPN), qui n’est pas utilisé dans les scénarios B2C. Pour afficher l’identificateur de connexion du compte local, recherchez l’objet utilisateur dans [l’Explorateur de graphique](https://graphexplorer.cloudapp.net/). Vous trouverez le même problème avec un utilisateur social compte (par exemple, un client qui s’inscrit avec Facebook, Google +, etc.), mais dans ce cas, il n’existe aucun identificateur de se connecter à parler de.

    ![Compte local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Pour un utilisateur du compte local, vous ne pourrez pas modifier les champs et enregistrer les modifications sous l’onglet **profil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problèmes avec initialisé par l’administrateur du mot de passe dans le portail classique Azure

Si vous réinitialisez le mot de passe pour un consommateur local basés sur le portail classique Azure (la commande **Réinitialiser le mot de passe** sous l’onglet **utilisateurs** ), grand ne seront pas en mesure de modifier son mot de passe dans la prochaine ouverture de session, si vous utilisez une connexion vers le haut ou connectez stratégie et sont verrouillées parti de vos applications. Pour résoudre ce problème, utilisez l' [API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) pour réinitialiser votre mot de passe du consommateur (sans expiration du mot de passe) ou utiliser une connexion de stratégie à la place de connexion vers le haut ou connectez-vous stratégie.

## <a name="issues-with-creating-a-custom-attribute"></a>Problèmes avec la création d’un attribut personnalisé

Un [attribut personnalisé ajouté sur le portail Azure](active-directory-b2c-reference-custom-attr.md) n’est pas immédiatement créé dans votre client B2C. Vous devrez utiliser l’attribut personnalisé dans au moins une de vos stratégies de celui-ci pour créés dans votre client B2C et deviennent disponibles via l’API de graphique.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problèmes avec la vérification d’un domaine sur le portail classique Azure

Vous ne pouvez pas actuellement vérifier un domaine correctement dans le [portail classique Azure](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problèmes de connexion avec la stratégie de l’authentification Multifacteur sur les navigateurs Safari

Demandes de stratégies de connexion (avec l’authentification Multifacteur activé) fail intermittente sur les navigateurs Safari avec erreurs HTTP 400 (demande incorrecte). Il s’agit d’échéance limites de taille des cookies faible de Safari. Il existe deux solutions de contournement pour ce problème :

- Utilisez la « inscription ou connexion stratégie » au lieu de la stratégie « connexion ».
- Réduisez le nombre de **revendications Application** demandée dans votre stratégie.
