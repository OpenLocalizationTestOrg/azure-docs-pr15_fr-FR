<properties
    pageTitle="Azure B2C annuaire Active : Infrastructure de stratégie Extensible | Microsoft Azure"
    description="Une rubrique dans le cadre de stratégie extensible de Azure Active Directory B2C et sur la création de différents types de stratégie"
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

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure B2C annuaire Active : Infrastructure de stratégie Extensible

## <a name="the-basics"></a>Concepts de base

Le cadre de stratégie extensible de B2C Azure Active Directory (AD Azure) est la puissance core du service. Stratégies de description complète d’expériences d’identité consommateur comme d’abonnement, connexion ou profil modification. Par exemple, une stratégie d’inscription permet de contrôler les comportements en configurant les paramètres suivants :

- Types de comptes (comptes social tel que Facebook ou comptes locaux comme adresse de messagerie) que les utilisateurs peuvent utiliser pour vous inscrire pour l’application.
- Attributs (par exemple, prénom, code postal et pointure) à collecter du consommateur pendant l’inscription.
- Utilisation de l’authentification multifacteur.
- L’et apparence de toutes les pages d’inscription.
- Informations (qui se manifeste en tant que revendications dans un jeton) que l’application reçoit lorsque la stratégie exécutée se termine.

Vous pouvez créer plusieurs stratégies de différents types de votre client et les utiliser dans vos applications selon vos besoins. Stratégies peuvent être réutilisées entre les applications. Cela permet aux développeurs de définir et modifier des expériences d’identité consommateur avec très peu ou aucune modification apportée à leur code.

Stratégies sont disponibles pour une utilisation via une interface simple pour les développeurs. Votre application déclenche une stratégie en utilisant une demande d’authentification HTTP standard (passage d’un paramètre de stratégie dans la demande de) et reçoit un jeton personnalisé en tant que réponse. Par exemple, la seule différence entre les demandes d’appel d’une stratégie d’abonnement et celles appel d’une stratégie de connexion sont le nom de stratégie utilisé dans le paramètre de chaîne de requête « p » :

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Pour plus d’informations sur l’infrastructure de stratégie, consultez le [billet de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Créer une stratégie d’abonnement

Pour activer l’inscription de votre application, vous avez besoin créer une stratégie d’inscription. Cette stratégie décrit les expériences consommateurs passera pendant l’inscription et le contenu de jetons que l’application recevra sur sportives réussies.

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Le **nom** détermine le nom de la stratégie d’inscription utilisé par votre application. Par exemple, entrez « SiUp ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Envoyer par courrier électronique d’inscription ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité social, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs d’abonnement**. Ici vous choisissez les attributs que vous souhaitez collecter du consommateur pendant l’inscription. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes d’Application**. Vous sélectionnez ici revendications que vous voulez retourner dans les jetons envoyés à votre application après une expérience d’inscription réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur d’identité », « Code Postal », « Utilisateur est les nouveautés » et « ID d’objet de l’utilisateur ».
8. Cliquez sur **créer**. Notez que la stratégie venez de créer apparaît sous la forme «**B2C_1_SiUp**» (la **B2C\_1\_ ** fragment est automatiquement ajouté) dans la carte de **stratégies d’inscription** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiUp**».
10. Sélectionnez « Contoso B2C application » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la **URL de réponse / rediriger URI** menu déroulant.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience client d’inscription pour votre application.

    > [AZURE.NOTE]
    Elle occupe en minute pour la création de stratégies et mises à jour prenne effet.

## <a name="create-a-sign-in-policy"></a>Créer une stratégie de connexion

Pour activer la connexion à votre application, vous devrez créer une stratégie de connexion. Cette stratégie décrit les expériences consommateurs va parcourir lors de la connexion et le contenu de jetons qui recevra l’application sur les compléments d’authentification réussies.

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de connexion**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Le **nom** détermine le nom de la stratégie de connexion utilisé par votre application. Par exemple, entrez « SiIn ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Local compte connexion ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité social, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **demandes d’Application**. Vous sélectionnez ici revendications que vous voulez retourner dans les jetons envoyés à votre application après une expérience de connexion réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur d’identité », « Code Postal » et « ID d’objet de l’utilisateur ». Cliquez sur **OK**.
7. Cliquez sur **créer**. Notez que la stratégie venez de créer apparaît sous la forme «**B2C_1_SiIn**» (la **B2C\_1\_ ** fragment est automatiquement ajouté) dans la carte de **stratégies de connexion** .
8. Ouvrez la stratégie en cliquant sur «**B2C_1_SiIn**».
9. Sélectionnez « Contoso B2C application » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la **URL de réponse / rediriger URI** menu déroulant.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience grand public de la connexion à votre application.

    > [AZURE.NOTE]
    Elle occupe en minute pour la création de stratégies et mises à jour prenne effet.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Créer une stratégie d’abonnement ou se connecter

Cette stratégie gère les deux expériences grand public d’abonnement et se connecter avec une configuration unique. Les consommateurs sont dirigées vers le bas la bonne voie (inscription ou connexion) en fonction du contexte. Il décrit également le contenu des jetons que l’application recevront à leur sportives réussies ou signe-ins.  Un exemple de code pour la stratégie d’abonnement ou se connecter est [disponible ici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’abonnement ou se connecter**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Le **nom** détermine le nom de la stratégie d’inscription utilisé par votre application. Par exemple, entrez « SiUpIn ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Envoyer par courrier électronique d’inscription ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité social, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs d’abonnement**. Ici vous choisissez les attributs que vous souhaitez collecter du consommateur pendant l’inscription. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes d’Application**. Vous sélectionnez ici revendications que vous voulez retourner dans les jetons envoyés à votre application après une expérience d’abonnement ou connexion réussie. Par exemple, sélectionnez « Nom complet », « Fournisseur d’identité », « Code Postal », « Utilisateur est les nouveautés » et « ID d’objet de l’utilisateur ».
8. Cliquez sur **créer**. Notez que la stratégie venez de créer apparaît sous la forme «**B2C_1_SiUpIn**» (la **B2C\_1\_ ** fragment est automatiquement ajouté) dans la carte de **stratégies d’abonnement ou se connecter** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiUpIn**».
10. Sélectionnez « Contoso B2C application » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la **URL de réponse / rediriger URI** menu déroulant.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience d’abonnement ou connexion à des consommateurs comme configuré.

    > [AZURE.NOTE]
    Elle occupe en minute pour la création de stratégies et mises à jour prenne effet.

## <a name="create-a-profile-editing-policy"></a>Créer un stratégie de modification du profil

Pour permettre à votre application de modification du profil, vous devrez créer un stratégie de modification du profil. Cette stratégie décrit les expériences consommateurs passera lors de la modification du profil et le contenu de jetons que l’application recevrez lors de réussite.

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de modification du profil**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Le **nom** détermine le nom de la stratégie utilisée par votre application de modification du profil. Par exemple, entrez « LAMELLE ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Adresse de messagerie ». Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d’identité social, si déjà configuré. Cliquez sur **OK**.
6. Cliquez sur **attributs de profil**. Ici vous choisissez les attributs que le consommateur peut afficher et modifier. Par exemple, sélectionnez « Pays », « Nom complet » et « Code Postal ». Cliquez sur **OK**.
7. Cliquez sur **demandes d’Application**. Vous sélectionnez ici revendications que vous voulez retourner dans les jetons envoyés à votre application après une expérience de modification du profil réussie. Par exemple, sélectionnez « Nom complet » et « Code Postal ».
8. Cliquez sur **créer**. Notez que la stratégie venez de créer apparaît sous la forme «**B2C_1_SiPe**» (la **B2C\_1\_ ** fragment est automatiquement ajouté) dans la carte de **modification des stratégies de profil** .
9. Ouvrez la stratégie en cliquant sur «**B2C_1_SiPe**».
10. Sélectionnez « Contoso B2C application » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la **URL de réponse / rediriger URI** menu déroulant.
11. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience du consommateur dans votre application de modification du profil.

    > [AZURE.NOTE]
    Elle occupe en minute pour la création de stratégies et mises à jour prenne effet.
    
## <a name="create-a-password-reset-policy"></a>Créer une stratégie de réinitialiser votre mot de passe

Pour activer permissions du mot de passe sur votre application, vous devrez créer une stratégie de réinitialiser votre mot de passe. Notez que le mot de passe à l’échelle de client réinitialisé option spécifiée [ici](active-directory-b2c-reference-sspr.md) est toujours applicable pour les stratégies de connexion. Cette stratégie décrit les expériences les consommateurs passera lors de la réinitialisation du mot de passe et le contenu de jetons que l’application recevrez lors de réussite.

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de réinitialisation du mot de passe**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Le **nom** détermine que le mot de passe réinitialisé nom de la stratégie utilisée par votre application. Par exemple, entrez « SSPR ».
5. Cliquez sur **fournisseurs d’identité** et sélectionnez « Réinitialiser le mot de passe à l’aide des adresse de messagerie ». Cliquez sur **OK**.
6. Cliquez sur **demandes d’Application**. Ici, vous choisissez revendications inclure dans les jetons envoyés à votre application après la réinitialisation d’un mot de passe réussie expérience. Par exemple, sélectionnez « ID d’objet de l’utilisateur ».
7. Cliquez sur **créer**. Notez que la stratégie venez de créer apparaît sous la forme «**B2C_1_SSPR**» (la **B2C\_1\_ ** fragment est automatiquement ajouté) dans la carte de **stratégies de réinitialisation du mot de passe** .
8. Ouvrez la stratégie en cliquant sur «**B2C_1_SSPR**».
9. Sélectionnez « Contoso B2C application » dans le menu déroulant **Applications** et `https://localhost:44321/` dans la **URL de réponse / rediriger URI** menu déroulant.
10. Cliquez sur **Exécuter maintenant**. Un nouvel onglet de navigateur s’ouvre et vous pouvez exécuter via l’expérience de grand public de réinitialiser votre mot de passe dans votre application.

    > [AZURE.NOTE]
    Elle occupe en minute pour la création de stratégies et mises à jour prenne effet.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Jeton, session et configuration de l’authentification unique](active-directory-b2c-token-session-sso.md).
