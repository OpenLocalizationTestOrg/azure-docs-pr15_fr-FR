<properties
    pageTitle="Didacticiel : Azure Active Directory intégrant ServiceNow et ServiceNow Express | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et ServiceNow et ServiceNow Express."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Didacticiel : Azure Active Directory l’intégration avec ServiceNow et ServiceNow Express.


Dans ce didacticiel, vous allez apprendre à intégrer ServiceNow et ServiceNow Express avec Azure Active Directory (AD Azure).

Intégration ServiceNow et ServiceNow Express avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à ServiceNow et ServiceNow Express
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté sur ServiceNow et ServiceNow Express (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec ServiceNow et ServiceNow Express, vous devez les éléments suivants :

- Un abonnement Azure AD
- Pour ServiceNow, une instance ou le client de ServiceNow, version Calgary ou une version ultérieure
- Pour ServiceNow Express, une instance de ServiceNow Express, version Helsinki ou une version ultérieure
- ServiceNow client doit avoir le [Plusieurs fournisseur unique se sur plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) activé. Pour ce faire en envoyant une demande de service en <https://hi.service-now.com/> 


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test. Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de ServiceNow à partir de la galerie
2. Configuration et test Azure AD authentification unique pour ServiceNow ou ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Ajout de ServiceNow à partir de la galerie
Pour configurer l’intégration des ServiceNow ou ServiceNow Express dans Azure AD, vous devez ajouter ServiceNow à partir de la galerie à votre liste d’applications SaaS gérées. 

**Pour ajouter ServiceNow à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **ServiceNow**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. Dans le volet résultats, sélectionnez **ServiceNow**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et test Azure AD SSO avec ServiceNow ou ServiceNow Express basé sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans ServiceNow est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans ServiceNow doit être établie.
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans ServiceNow. Pour configurer et tester Azure AD SSO avec ServiceNow, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO pour ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Configuration Azure AD SSO pour ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
3. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un ServiceNow tester l’utilisateur](#creating-a-servicenow-test-user)** - d’avoir un homologue de Britta Simon dans ServiceNow est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
6. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

> [AZURE.NOTE] Si vous voulez configurer ServiceNow omettez l’étape 2. De même, si vous voulez configurer ServiceNow Express omettez l’étape 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuration d’authentification Azure AD unique pour ServiceNow

1.  Dans le portail classique Azure AD, dans la page de l’intégration **ServiceNow** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ServiceNow** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer des URL de l’application")

    un. dans la zone de texte **Se ServiceNow d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs pour vous connecter à votre application ServiceNow suivant le modèle : `https://<instance-name>.service-now.com`.

    b. Dans la zone de texte **identificateur** , tapez l’URL de votre utilisé par vos utilisateurs pour vous connecter à votre application ServiceNow suivant le modèle : `https://<instance-name>.service-now.com`.

    c. Cliquez sur **suivant**

4.  Pour être Azure AD configure automatiquement ServiceNow pour l’authentification basée sur les SAML, entrez votre nom de l’instance ServiceNow, administrateur nom d’utilisateur et mot de passe administrateur dans le formulaire de **configuration automatique de l’authentification unique** , cliquez sur *configurer*. Notez que le nom d’utilisateur d’administration fourni doit avoir le rôle **security_admin** affecté dans ServiceNow pour que cela fonctionne. Dans le cas contraire, pour configurer manuellement ServiceNow pour utiliser un fournisseur d’identité SAML Azure AD, cliquez sur **configurer manuellement l’application pour l’authentification unique**, puis cliquez sur **suivant** et effectuez les étapes suivantes.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer des URL de l’application")



5.  Dans la page **configuration de l’authentification unique en ServiceNow** , cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "De configurer l’authentification unique")

1. Authentification à votre application ServiceNow en tant qu’administrateur.
2. Activer le plug-in _intégration - plusieurs fournisseur authentification programme d’installation unique_ en suivant les étapes suivantes :

    un. Dans le volet de navigation sur le côté gauche, reportez-vous à la section **Définition du système** , puis sur **plug-ins**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Plug-in activer")
    
    b. Rechercher _l’intégration - plusieurs fournisseur authentification programme d’installation unique_.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Plug-in activer")

    c. Sélectionnez le plug-in. Côtés droit, cliquez sur et sélectionnez **Activer/mise à niveau**.
    
    d. Cliquez sur le bouton **Activer** .

2. Dans le volet de navigation sur le côté gauche, cliquez sur **Propriétés**.  

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurer des URL de l’application")


3. Dans la boîte de dialogue **Propriétés de l’authentification unique fournisseur plusieurs** , procédez comme suit :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurer des URL de l’application")

    un. En tant **Activer plusieurs fournisseur l’authentification unique**, sélectionnez **Oui**.

    b. En tant **Activer journalisation vous utilisez le fournisseur multiple l’intégration de l’authentification unique**, sélectionnez **Oui**.

    c. Dans la zone de texte **du champ de l’utilisateur table...** , tapez **nom_utilisateur**.

    d. Cliquez sur **Enregistrer**.



1. Dans le volet de navigation sur le côté gauche, cliquez sur **certificats x509**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Certificats X.509** , cliquez sur **Nouveau**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Certificats X.509** , effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "De configurer l’authentification unique")

    un. Cliquez sur **Nouveau**.

    b. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **TestSAML2.0**).

    c. Sélectionnez **actif**.

    d. En tant que **Format**, sélectionnez **PEM**.

    e. En tant que **Type**, sélectionnez **Gestion de la confidentialité magasin de certificats**.
    
    f. Ouvrez votre certificat en base 64 codé dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat PEM** .

    g. Cliquez sur **mettre à jour**.


1. Dans le volet de navigation sur le côté gauche, cliquez sur **Fournisseurs d’identité**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "De configurer l’authentification unique")

1. Dans la boîte de dialogue **Fournisseurs d’identité** , cliquez sur **Nouveau**:

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Fournisseurs d’identité** , cliquez sur **SAML2 clic1 ?**:

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue Propriétés de clic1 SAML2, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "De configurer l’authentification unique")


    un. dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **SAML 2.0**).

    b. Dans la zone de texte **Champ utilisateur** , type **messagerie** ou **user_id**, selon laquelle le champ est utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow. 
    
    > [AZURE.NOTE] Vous pouvez configue Azure AD pour émettre l’identifiant Azure AD (nom d’utilisateur principal) ou l’adresse de messagerie comme l’identificateur unique dans le jeton SAML en accédant à la **ServiceNow > attributs > authentification unique** section du portail classique Azure et de mappage du champ souhaité pour l’attribut **nameidentifier** . La valeur stockée dans Azure Active Directory (par exemple, nom d’utilisateur principal) pour l’attribut sélectionné doit correspondre à la valeur stockée dans ServiceNow pour le champ saisi (par exemple, user_id)

    c. Dans le portail classique Azure AD, copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte **URL du fournisseur d’identité** .

    d. Dans le portail classique Azure AD, copiez la valeur de **l’URL de la demande d’authentification** et collez-le dans la zone de texte **AuthnRequest fournisseur d’identité** .

    e. Dans le portail classique Azure AD, copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **SingleLogoutRequest fournisseur d’identité** .

    f. Dans la zone de texte **ServiceNow page d’accueil** , tapez l’URL de votre page d’accueil d’instance ServiceNow.

    > [AZURE.NOTE] La page d’accueil de ServiceNow instance est une concaténation de votre **URL de client ServieNow** et **/navpage.do** (par exemple :`https://fabrikam.service-now.com/navpage.do`).
 

    g. Dans la **ID de l’entité / émetteur** zone de texte, tapez l’URL de votre client ServiceNow.

    h. Dans la zone de texte **URL d’Audience** , tapez l’URL de votre client ServiceNow. 

    J’ai. Dans la zone de texte **Protocole de liaison pour SingleLogoutRequest de IDP** , tapez **urn : oasis : noms : tc : SAML:2.0:bindings:HTTP-rediriger**.

    j. Dans la zone de texte NameID stratégie, tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : hauteur**.

    k. Désactivez l’option **créer un AuthnContextClass**.

    l. Dans la **Méthode AuthnContextClassRef**, tapez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Cela est nécessaire uniquement si vous êtes la seule organisation cloud. Si vous utilisez local ADFS ou l’authentification Multifacteur pour l’authentification, vous ne pouvez pas configure cette valeur. 

    m. Dans la zone de texte **Horloge incliner** , tapez **60**.

    n. En tant que **Seul signe sur Script**, sélectionnez **MultiSSO_SAML2_Update1**.

    o. En tant que **x509 certificat**, sélectionnez le certificat que vous avez créé à l’étape précédente.

    p. Cliquez sur **Envoyer**. 



6. Dans le portail classique Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "De configurer l’authentification unique")

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.
 
    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "De configurer l’authentification unique")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuration d’authentification Azure AD unique pour ServiceNow Express

1.  Dans le portail classique Azure AD, dans la page de l’intégration **ServiceNow** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ServiceNow** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer des URL de l’application")

    un. dans la zone de texte **Se ServiceNow d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs pour vous connecter à votre application ServiceNow suivant le modèle : `https://<instance-name>.service-now.com`.

    b. Dans la zone **URL de l’émetteur** , entrez votre URL utilisée par vos utilisateurs pour vous connecter à votre application ServiceNow suivant le modèle `https://<instance-name>.service-now.com`.

    c. Cliquez sur **suivant**

4.  Cliquez sur **configurer manuellement l’application pour l’authentification unique**, puis cliquez sur **suivant** et effectuez les étapes suivantes.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer des URL de l’application")

5.  Dans la page **configuration de l’authentification unique en ServiceNow** , cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat localement sur votre ordinateur, puis sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "De configurer l’authentification unique")

6. Authentification à votre application ServiceNow Express en tant qu’administrateur.

7. Dans le volet de navigation sur le côté gauche, cliquez sur **Authentification unique**.  

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurer des URL de l’application")


8. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur l’icône de configuration dans le coin supérieur droit et définir les propriétés suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurer des URL de l’application")

    un. Activer ou désactiver **Activer plusieurs fournisseur SSO** vers la droite.

    b. Activer ou désactiver **Activer l’enregistrement pour l’intégration de l’authentification unique fournisseur plusieurs de débogage** vers la droite.

    c. Dans la zone de texte **du champ de l’utilisateur table...** , tapez **nom_utilisateur**.


9. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur **Ajouter un nouveau certificat**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "De configurer l’authentification unique")



10. Dans la boîte de dialogue **Certificats X.509** , effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "De configurer l’authentification unique")

    un. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **TestSAML2.0**).

    b. Sélectionnez **actif**.

    c. En tant que **Format**, sélectionnez **PEM**.

    d. En tant que **Type**, sélectionnez **Gestion de la confidentialité magasin de certificats**.

    e. Créer un fichier en base 64 codé à partir de votre certificat téléchargé.
   
    > [AZURE.NOTE] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Ouvrez votre certificat en base 64 codé dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat PEM** .

    g. Cliquez sur **mettre à jour**.


11. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur **Ajouter un nouveau IdP**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "De configurer l’authentification unique")


12. Dans la boîte de dialogue **Ajouter nouveau fournisseur d’identité** , sous **Configurer fournisseur d’identité**, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "De configurer l’authentification unique")


    un. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **SAML 2.0**).

    b. Dans le portail classique Azure AD, copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte **URL du fournisseur d’identité** .

    c. Dans le portail classique Azure AD, copiez la valeur de **l’URL de la demande d’authentification** et collez-le dans la zone de texte **AuthnRequest fournisseur d’identité** .

    d. Dans le portail classique Azure AD, copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **SingleLogoutRequest fournisseur d’identité** .

    e. En tant que **Certificat d’identité du fournisseur**, sélectionnez le certificat que vous avez créé à l’étape précédente.


13. Cliquez sur **Paramètres avancés**, puis sous **Autres propriétés de fournisseur d’identité**, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "De configurer l’authentification unique")

    un. Dans la zone de texte **Protocole de liaison pour SingleLogoutRequest de IDP** , tapez **urn : oasis : noms : tc : SAML:2.0:bindings:HTTP-rediriger**.

    b. Dans la zone de texte **NameID stratégie** , tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : hauteur**.    

    c. Dans la **Méthode AuthnContextClassRef**, tapez **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Désactivez l’option **créer un AuthnContextClass**.

14. Sous **Propriétés de fournisseur de Service supplémentaires**, procédez comme suit :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "De configurer l’authentification unique")

    un. Dans la zone de texte **ServiceNow page d’accueil** , tapez l’URL de votre page d’accueil d’instance ServiceNow.

    > [AZURE.NOTE] La page d’accueil de ServiceNow instance est une concaténation de votre **URL de client ServieNow** et **/navpage.do** (par exemple : `https://fabrikam.service-now.com/navpage.do`).

    b. Dans la **ID de l’entité / émetteur** zone de texte, tapez l’URL de votre client ServiceNow.

    c. Dans la zone de texte **URI d’Audience** , tapez l’URL de votre client ServiceNow. 

    d. Dans la zone de texte **Horloge incliner** , tapez **60**.

    e. Dans la zone de texte **Champ utilisateur** , type **messagerie** ou **user_id**, selon laquelle le champ est utilisé pour identifier les utilisateurs dans votre déploiement ServiceNow.
    
    > [AZURE.NOTE] Vous pouvez configue Azure AD pour émettre l’identifiant Azure AD (nom d’utilisateur principal) ou l’adresse de messagerie comme l’identificateur unique dans le jeton SAML en accédant à la **ServiceNow > attributs > authentification unique** section du portail classique Azure et de mappage du champ souhaité pour l’attribut **nameidentifier** . La valeur stockée dans Azure Active Directory (par exemple, nom d’utilisateur principal) pour l’attribut sélectionné doit correspondre à la valeur stockée dans ServiceNow pour le champ saisi (par exemple, user_id)

    f. Cliquez sur **Enregistrer**. 


15. Dans le portail classique Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "De configurer l’authentification unique")

16. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.
 
    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
L’objectif de cette section est hiérarchique l’activation d’utilisateur mise en service des comptes d’utilisateurs Active Directory ServiceNow.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1. Dans le portail classique Azure Management, dans la page de l’intégration **ServiceNow** application, cliquez sur **configurer approvisionnement de l’utilisateur**. 

    ![Configuration des utilisateurs] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Configuration des utilisateurs")


2. Dans la page **Entrez vos informations d’identification ServiceNow pour activer la mise en service automatique de l’utilisateur** , fournissent des paramètres de configuration suivants : configurer la mise en service des utilisateurs 

     un. Dans la zone de texte **Nom de l’Instance ServiceNow** , tapez le nom de l’instance ServiceNow.

     b. Dans la zone de texte **Nom d’utilisateur ServiceNow** , tapez le nom du compte d’administrateur ServiceNow.

     c. Dans la zone de texte **Mot de passe administrateur ServiceNow** , tapez le mot de passe pour ce compte.

     d. Cliquez sur **Valider** pour vérifier votre configuration.

     e. Cliquez sur le bouton **suivant** pour ouvrir la page **étapes suivantes** .

     f. Si vous voulez configurer tous les utilisateurs de cette application, sélectionnez «**configurer automatiquement tous les comptes d’utilisateurs dans l’annuaire pour cette application**». 

    ![Étapes suivantes] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Étapes suivantes")

     g. Dans la page **étapes suivantes** , cliquez sur **terminé** pour enregistrer votre configuration.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-a-servicenow-test-user"></a>Création d’un utilisateur de test ServiceNow

Dans cette section, vous créez un utilisateur appelé Britta Simon dans ServiceNow. Dans cette section, vous créez un utilisateur appelé Britta Simon dans ServiceNow. Si vous ne savez pas comment ajouter un utilisateur dans votre compte ServiceNow ou ServiceNow Express, contactez l’équipe de support technique ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux ServiceNow.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à ServiceNow, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **ServiceNow**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette ServiceNow dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application ServiceNow.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
