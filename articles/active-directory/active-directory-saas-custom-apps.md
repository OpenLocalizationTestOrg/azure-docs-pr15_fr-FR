<properties 
    pageTitle="Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory | Microsoft Azure" 
    description="Découvrez comment connecter aux applications à Azure Active Directory à l’aide de SAML et celle de mot de passe pour l’inscription en libre service" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configuration de l’authentification unique pour les applications qui ne sont pas dans la galerie des applications Azure Active Directory

Cet article concerne une fonctionnalité qui permet aux administrateurs de configurer l’authentification unique aux applications ne figurent pas dans le Azure Active Directory Application galerie *sans écrire de code*. Cette fonctionnalité a été publiée à partir de la version d’évaluation technique sur 18 novembre 2015 et incluse dans [Azure Active Directory Premium](active-directory-editions.md). Si vous cherchez à la place pour des instructions pour les développeurs sur l’intégration des applications personnalisées à Azure AD via le code, voir [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

La galerie d’applications Azure Active Directory fournit une liste d’applications qui prennent en charge un formulaire de l’authentification unique avec Azure Active Directory, comme décrit dans [cet article](active-directory-appssoaccess-whatis.md). Une fois que vous (comme un informatique specialist ou système intégration de votre organisation) avez trouvé l’application que vous voulez vous connecter, vous pouvez commencer par, suivez les instructions détaillées présentées dans le portail de gestion Azure pour activer l’authentification unique.

Clients avec des licences [Azure Active Directory Premium](active-directory-editions.md) également partie de ces fonctionnalités supplémentaires :

* Intégration libre-service de n’importe quelle application qui prend en charge des fournisseurs d’identité SAML 2.0 (SP exécutée par le serveur ou initialisé par l’IdP)
* Intégration libre-service d’une application web qui contient une basé sur HTML-page de connexion à l’aide de [l’authentification unique de mot de passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Connexion libre-service des applications qui utilisent le protocole SCIM pour utilisateur mise à disponibilité ([ci-dessus](active-directory-scim-provisioning.md))
* Possibilité d’ajouter des liens vers n’importe quelle application dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou dans le [Panneau d’accès Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Cela peut inclure non seulement SaaS applications que vous utilisez, mais n'avez pas encore été sur-lieu à la galerie des applications Azure AD, mais les applications web tiers que votre organisation a déployé aux serveurs que contrôler, dans le cloud ou en local.

Ces fonctionnalités, également connu sous le nom *des modèles d’application intégration*, fournissent des points de connexion basée sur des normes pour les applications qui prennent en charge l’authentification basée sur les formulaires, SCIM ou SAML et incluent les options flexibles et les paramètres de compatibilité avec un grand nombre d’applications. 

##<a name="adding-an-unlisted-application"></a>Ajout d’une application non répertoriée

Pour connecter une application à l’aide d’un modèle d’intégration application, se connecter au portail de gestion Azure à l’aide de votre compte d’administrateur Azure Active Directory, puis naviguez vers le **Active Directory > [répertoire] > Applications** , sélectionnez **Ajouter**, puis sur **Ajouter une application à partir de la galerie**. 

![][1]

Dans la galerie d’application, vous pouvez ajouter une application non répertoriée à l’aide de la catégorie **personnalisée** sur la gauche, ou en cliquant sur le lien **Ajouter une application non répertoriée** figurant dans les résultats de recherche si votre application souhaitée n’a pas été trouvée. Après avoir entré un nom pour votre application, vous pouvez configurer les options d’authentification unique et son comportement. 

**Astuce**: pour obtenir les meilleurs résultats, utilisez la fonction recherche pour vérifier si l’application existe déjà dans la galerie d’applications. Si l’application est trouvée et sa description « mentions » « authentification unique », puis l’application sont déjà pris en charge pour fédéré de l’authentification unique. 

![][2]

Ajout d’une application de cette façon offre une expérience très similaire à celui qui est disponible pour les applications déjà intégrées. Pour commencer, sélectionnez **Configurer de l’authentification unique**. L’écran suivant présente les trois options suivantes pour configurer l’authentification unique, qui sont décrites dans les sections suivantes.

![][3]


##<a name="azure-ad-single-sign-on"></a>Authentification Azure AD unique

Sélectionnez cette option pour configurer l’authentification basée sur les SAML pour l’application. Cette fonctionnalité nécessite que la prise en charge application SAML 2.0 et vous devriez collecter les informations sur la façon d’utiliser les capacités SAML de l’application avant de poursuivre. Après avoir sélectionné **suivant**, vous devrez entrer des URL différentes trois correspondant aux points de terminaison SAML pour l’application. 

![][4]
 
Il s’agit des :

* **Se d’URL (exécutée par SP uniquement)** – où l’utilisateur accède à se connecter à cette application. Si l’application est configurée pour effectuer une authentification unique par le fournisseur de service sur, puis lorsqu’un utilisateur accède à cette URL, le fournisseur de services fera la redirection nécessaire pour Azure AD pour authentifier et ouvrir une session sur l’utilisateur dans. Si ce champ est rempli, Azure AD Utilisez cette URL pour lancer l’application à partir d’Office 365 et le panneau d’Azure AD accès. Si ce champ est ommited, puis Azure AD va effectuer fournisseur d’identité-créé par ouverture de session lorsque l’application est lancée à partir d’Office 365, le panneau de configuration Azure AD Access, ou à partir d’Azure Active Directory unique authentification URL (copiable à partir de l’onglet tableau de bord).

* **URL de l’émetteur** - l’émetteur URL doit identifier l’application pour les authentification unique sur est en cours de configuration. Il s’agit de la valeur Azure AD renvoie à application comme paramètre **Audience** du jeton SAML et l’application est censée valider. Cette valeur s’affiche également que l' **ID de l’entité** dans les métadonnées SAML fournies par l’application. Vérifier la documentation d’application SAML pour plus d’informations sur les éléments il est ID de l’entité ou valeur Audience. Voici un exemple de la manière dont l’URL de l’Audience apparaît dans le jeton SAML renvoyé à l’application :

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de réponse** - l’URL de réponse est l’endroit où l’application attend de recevoir le jeton SAML. Ceci est également appelé l' **URL Assertion consommateur ACS (Service)**. Consultez la documentation d’application SAML pour plus d’informations sur les nouveautés de sa réponse jeton SAML URL ou ACS.
 Une fois ces entrées, cliquez sur **suivant** pour passer à l’écran suivant. Cet écran fournit des informations sur ce qui doit être configuré sur le côté de l’application pour lui permettre d’accepter un jeton SAML à partir d’Azure AD. 

![][5]

Les valeurs qui sont requises varient en fonction de l’application, aussi consultez la documentation d’application SAML pour plus d’informations. L' **Authentification** et **déconnexion** URL les deux résoudre au même point de terminaison, qui est le point de terminaison de gestion des demandes SAML pour votre instance d’Azure AD du service. L’URL de l’émetteur est la valeur qui s’affiche comme « L’émetteur » à l’intérieur du jeton SAML émis à l’application. 

Une fois que votre application a été configurée, cliquez sur bouton **suivant** , puis sur **Terminer** pour fermer la boîte de dialogue. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Affectation d’utilisateurs et groupes à votre application SAML 

Une fois que votre application a été configurée pour utiliser un fournisseur d’identité SAML basée sur Azure AD, il est presque prêt à tester. Comme un contrôle de sécurité, Azure AD ne génère un jeton de leur permettant de vous connecter à l’application, sauf si elles ont accès à l’aide d’Azure AD. Les utilisateurs peuvent accéder directement ou via un groupe dont ils sont membres de. 

Pour affecter un utilisateur ou un groupe à votre application, cliquez sur le bouton **Affecter des utilisateurs** . Sélectionnez l’utilisateur ou le groupe que vous souhaitez affecter, puis le bouton **affecter** . 

![][6]

Affectation d’un utilisateur permettra Azure AD émettre un jeton pour l’utilisateur, ainsi qu’à l’origine d’une vignette de cette application doivent apparaître dans le panneau d’accès de l’utilisateur. Vignette d’une application s’affichent également dans le Lanceur d’applications Office 365 si l’utilisateur utilise Office 365. 

Vous pouvez télécharger un logo vignette pour l’application en utilisant le bouton **Télécharger un Logo** dans l’onglet **configurer** pour l’application. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personnaliser les affirmations émises dans le jeton SAML 

Lorsqu’un utilisateur s’authentifie à l’application, Azure AD émet un jeton SAML à l’application qui contient des informations (ou revendications) sur l’utilisateur qui identifie les. Par défaut cela inclut le nom d’utilisateur, adresse de messagerie, prénom et nom de l’utilisateur. 

Vous pouvez afficher ou modifier les revendications envoyées dans le jeton SAML à l’application sous l’onglet **attributs** . 

![][7]

Il existe deux raisons possibles pourquoi vous devrez peut-être modifier les affirmations émises dans le jeton SAML : •la application a été écrite afin d’exiger un jeu de réclamer MU différent ou des valeurs de déclaration d’application •votre a été déployée d’une manière qui nécessite la NameIdentifier revendications autre que le nom d’utilisateur (UE nom d’utilisateur principal) stockée dans Azure Active Directory. 

Pour plus d’informations sur la façon d’ajouter et modifier des réclamations au titre de ces scénarios, consultez cet [article sur la personnalisation de revendications](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Test de l’application SAML 

Après ont configuré la SAML URL et le certificat dans Azure Active Directory et dans l’application, les utilisateurs ou groupes ont été affectés à l’application dans Azure, les revendications ont été examinées et modifiées si nécessaire, puis l’utilisateur est prêt à vous connecter à l’application. 

Pour tester, simplement vous connecter au panneau d’accès Azure AD en https://myapps.microsoft.com à l’aide d’un compte d’utilisateur que vous avez affecté à l’application, puis cliquez sur la vignette pour démarrer le processus d’authentification unique de l’application. Vous pouvez également cliquer, vous pouvez passer directement à l’URL d’authentification de l’application et se connecter à partir de là. 

Pour le débogage conseils, voir cet [article comment déboguer basée sur SAML de l’authentification unique pour les applications](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Mot de passe de session unique

Sélectionnez cette option pour configurer [par mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md) pour une application web qui comporte une page de connexion à HTML. Authentification par mot de passe, également appelé mot de passe de stockage, permet de gérer l’accès des utilisateurs et les mots de passe pour les applications web qui ne prennent pas en charge la fédération d’identité. Il est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, tels qu’aux comptes d’application médias sociaux de votre organisation. 

Après avoir sélectionné **suivant**, vous devrez entrer l’URL de sur le web-page de connexion l’application. Notez que cela doit être la page qui contient les champs nom d’utilisateur et mot de passe d’entrée. Une fois saisie, Azure AD démarre un processus pour analyser la page de connexion pour un nom d’utilisateur d’entrée et un mot de passe d’entrée. Si le processus n’est pas réussi, puis il vous guide un autre processus d’installation d’une extension du navigateur (nécessite Internet Explorer, Chrome ou Firefox) qui vous permet de capturer manuellement les champs.

Une fois la page de connexion est capturée, les utilisateurs et groupes peuvent être affectées et stratégies d’informations d’identification peuvent être définies comme normal [applications de l’authentification unique de mot de passe](active-directory-appssoaccess-whatis.md).

Remarque : Vous pouvez télécharger un logo vignette pour l’application en utilisant le bouton **Télécharger un Logo** dans l’onglet **configurer** pour l’application. 

##<a name="existing-single-sign-on"></a>Existant de l’authentification unique

Sélectionnez cette option pour ajouter un lien vers une application au portail panneau Azure AD Access ou Office 365 de votre organisation. Vous pouvez utiliser ce pour ajouter des liens vers des applications web personnalisée qui utilisent actuellement Azure Active Directory Federation Services (ou un autre service de fédération) au lieu d’Azure AD pour l’authentification. Ou bien, vous pouvez ajouter des liens approfondies à des pages SharePoint ou d’autres pages web que vous voulez juste apparaissent sur panneaux d’accès de l’utilisateur de votre. 

Après avoir sélectionné **suivant**, vous devrez entrer l’URL de l’application pour créer un lien vers. Une fois terminé, les utilisateurs et groupes peuvent être affectées à l’application, ce qui provoque l’affichage de l’application dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou dans le [Panneau d’accès Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) pour ces utilisateurs.

Remarque : Vous pouvez télécharger un logo vignette pour l’application en utilisant le bouton **Télécharger un Logo** dans l’onglet **configurer** pour l’application.

## <a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [La personnalisation des affirmations émises dans le jeton SAML pour les applications déjà intégrées](active-directory-saml-claims-customization.md)
- [Résolution des problèmes en fonction des SAML de l’authentification unique](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
