<properties
    pageTitle="Seul gestion authentification pour les applications d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Découvrez comment gérer l’authentification unique sur pour les applications d’entreprise à l’aide d’Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Aperçu : Gestion de l’authentification unique pour les applications d’entreprise dans le nouveau portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portail classique Azure](active-directory-sso-integrate-saas-apps.md)

Cet article décrit comment utiliser le [portail Azure](https://portal.azure.com) pour gérer les paramètres d’authentification unique pour les applications, notamment celles qui ont été ajoutés à partir de la [Galerie d’applications Azure Active Directory (AD Azure)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). L’expérience de gestion Azure AD pour l’authentification unique est actuellement dans la version d’évaluation et cet article décrit les nouvelles fonctionnalités, ainsi que quelques limitations temporaires qui seront en place uniquement pendant la période d’aperçu. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Recherche de vos applications dans le nouveau portail

À partir de septembre 2016, toutes les applications qui ont été configurées pour unique authentification dans un répertoire, par un administrateur d’annuaire à l’aide de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) à l’intérieur du [portail classique Azure](https://manage.windowsazure.com), peuvent maintenant consulter et gérer dans le portail Azure.

Vous trouverez ces applications dans la section **Applications d’entreprise** du portail Azure, un lien vers lequel vous pouvez trouver dans la liste des **Services plus** dans le [portail](https://portal.azure.com). Applications d’entreprise sont des applications qui ont été déployées et sont utilisées par les utilisateurs au sein de votre organisation.

![Carte d’Applications d’entreprise][1]

Sélectionnez **toutes les applications** pour afficher une liste de toutes les applications qui ont été configurés, y compris les applications qui avaient été ajoutées à partir de la galerie. Sélection d’une application du chargement de la carte de la ressource pour cette application, où les rapports peuvent être affichés pour cette application et une variété de paramètres pouvant être gérée.

Pour gérer les paramètres d’authentification unique, sélectionnez **authentification unique**.

![Carte de ressources d’application][2]


##<a name="single-sign-on-modes"></a>Modes d’authentification unique

La carte **de l’authentification unique** commence par un menu **en Mode** , qui permet du mode d’authentification unique doit être configuré. Les options disponibles incluent :

* **Authentification basée sur SAML** - cette option est disponible si l’application prend en charge complète fédéré de l’authentification unique avec Azure Active Directory à l’aide du protocole SAML 2.0.

* **Authentification par mot de passe** - cette option est disponible si Azure AD prend en charge de cette application de remplissage de formulaires de mot de passe.

* **Lié se** - auparavant connu sous le « Existante de l’authentification unique », cette option permet aux administrateurs de placer un lien vers l’application dans le Lanceur d’applications de leur utilisateur panneau Azure AD Access ou Office 365.

Pour plus d’informations sur ces modes, voir [comment authentification unique avec travail Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Authentification basée sur SAML

L’option **authentification basée sur SAML** affiche une carte est divisé en quatre sections :

###<a name="domains-and-urls"></a>Domaines et URL
Il s’agit de l’emplacement des détails sur le domaine et les URL de l’application sont ajoutés à votre répertoire Azure AD. Toutes les entrées nécessaires pour rendre l’application de travail d’authentification unique sont affichent directement dans l’écran, tandis que toutes les entrées facultatives peuvent être affichées en activant la case à cocher **Afficher les URL paramètres avancés** . La liste complète des entrées pris en charge inclut :

* **Signe d’adresse URL** où l’utilisateur accède à se connecter à cette application. Si l’application est configurée pour effectuer une authentification unique par le fournisseur de service sur, puis lorsqu’un utilisateur accède à cette URL, le fournisseur de services ne la redirection nécessaire pour Azure AD pour authentifier et connecter l’utilisateur. Si ce champ est rempli, Azure AD Utilisez cette URL pour lancer l’application à partir d’Office 365 et le panneau d’Azure AD accès. Si ce champ est omis, puis Azure AD effectue à la place fournisseur d’identité-créé par ouverture de session lorsque l’application est lancée à partir d’Office 365, le panneau de configuration Azure AD Access, ou à partir d’Azure Active Directory unique authentification URL.

* **Identificateur** - URI ce doivent identifier l’application pour l’unique ouverture de session est en cours de configuration. Il s’agit de la valeur Azure AD renvoie à application comme paramètre Audience du jeton SAML et l’application est censée valider. Cette valeur apparaît également sous forme de l’ID d’entité dans les métadonnées SAML fournies par l’application.

* **URL de réponse** - l’URL de réponse est l’endroit où l’application attend de recevoir le jeton SAML. Ceci est également appelé l’URL Assertion consommateur ACS (Service). Une fois ces entrées, cliquez sur Suivant pour passer à l’écran suivant. Cet écran fournit des informations sur ce qui doit être configuré sur le côté de l’application pour lui permettre d’accepter un jeton SAML à partir d’Azure AD.

* **État du relais** - l’état de relais est un paramètre facultatif qui peut vous aider à indiquer à l’application where rediriger l’utilisateur une fois l’authentification terminée. En règle générale la valeur est une URL valide dans l’application, mais certaines applications utilisent ce champ différemment (voir l’authentification unique de l’application sur la documentation pour plus d’informations). La possibilité de définir l’état du relais est une nouvelle fonctionnalité unique pour le nouveau portail Azure.

###<a name="user-attributes"></a>Attributs de l’utilisateur
Il s’agit dans laquelle les administrateurs peuvent afficher et modifier les attributs qui sont envoyés dans le jeton SAML qui Azure AD émet à l’application utilisateurs moment de se connecter.

Pour la première version préliminaire, l’attribut peut uniquement être modifiée pris en charge est l’attribut **d’Identification utilisateur** . La valeur de cet attribut est le champ dans Azure AD qui identifie chaque utilisateur de l’application. Par exemple, si l’application a été déployée à l’aide de l’adresse de messagerie « » en tant que le nom d’utilisateur et un identificateur unique, puis la valeur serait être définie dans le champ « user.mail » dans Azure Active Directory.

Modification des attributs supplémentaires est pris en charge dans un aperçu ultérieur.

###<a name="saml-signing-certificate"></a>Certificat de signature SAML
Cette section affiche les détails du certificat Azure Active Directory utilise pour signer les jetons SAML émis à l’application chaque fois que l’utilisateur s’authentifie. Il permet les propriétés du certificat actuel à inspecter, y compris la date d’expiration.

La possibilité de survol le certificat et gérer le certificat supplémentaire options seront pris en charge dans une version ultérieure (version préliminaire). Notez que complètes de gestion des certificats peuvent toujours être effectuée dans le [portail classique Azure](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configuration de l’application
La dernière section fournit la documentation et/ou les contrôles nécessaires pour configurer l’application elle-même utiliser Azure Active Directory comme fournisseur d’identité.

Le menu de sortie **Configurer l’Application** fournit des instructions concises, incorporées nouveau pour la configuration de l’application. Il s’agit d’une autre nouveauté unique pour le nouveau portail Azure.

> [AZURE.NOTE] Pour obtenir un exemple complet de documentation incorporé, reportez-vous à l’application Salesforce.com. Documentation présentée sous d’autres applications est ajoutée en permanence lors de la visualisation.

![Documents incorporés][3]

##<a name="password-based-sign-on"></a>Authentification par mot de passe
Si pris en charge pour l’application, sélectionnez le mode d’authentification par mot de passe, cliquez sur **Enregistrer** instantanément configurent pour effectuer l’authentification par mot de passe. Pour plus d’informations sur le déploiement d’authentification par mot de passe, voir [comment authentification unique avec travail Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Authentification par mot de passe][4]


##<a name="linked-sign-on"></a>Ouverture de session liée
Si pris en charge pour l’application, sélection du mode d’authentification unique lié vous permet d’entrer l’URL que vous souhaitez le panneau de configuration Azure AD Access ou Office 365 pour rediriger vers lorsque les utilisateurs cliquent sur cette application. Pour plus d’informations sur l’authentification unique liée (auparavant appelé « SSO existant »), voir [comment authentification unique avec travail Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Authentification liée][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
