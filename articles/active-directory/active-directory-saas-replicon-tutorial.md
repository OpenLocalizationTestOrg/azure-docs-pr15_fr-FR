<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec RÉPLICON | Microsoft Azure" 
    description="Découvrez comment utiliser RÉPLICON avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-replicon"></a>Didacticiel : Intégration d’Azure Active Directory avec RÉPLICON
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et RÉPLICON. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client RÉPLICON
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à RÉPLICON sera en mesure d’authentification unique dans l’application sur votre site de société RÉPLICON (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour RÉPLICON
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-replicon-tutorial/IC777798.png "Scénario")
##<a name="enabling-the-application-integration-for-replicon"></a>L’activation de l’intégration des applications pour RÉPLICON
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour RÉPLICON.

###<a name="to-enable-the-application-integration-for-replicon-perform-the-following-steps"></a>Pour activer l’intégration des applications pour RÉPLICON, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-replicon-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-replicon-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-replicon-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **RÉPLICON**.

    ![Galerie d’applications] (./media/active-directory-saas-replicon-tutorial/IC777799.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **RÉPLICON**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![RÉPLICON] (./media/active-directory-saas-replicon-tutorial/IC777800.png "RÉPLICON")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à RÉPLICON avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **RÉPLICON** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-replicon-tutorial/IC777801.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à RÉPLICON** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-replicon-tutorial/IC777802.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-replicon-tutorial/IC777803.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **Se RÉPLICON d’adresse URL** , tapez l’URL de votre client RÉPLICON (par exemple : *https://na2.replicon.com/company/saml2/sp-sso/post*).
    2.  Dans la zone de texte **URL de réponse RÉPLICON** , tapez l’URL de votre RÉPLICON **AssertionConsumerService** (par exemple : *https://global.replicon.com/ ! / saml2/société/authentification/billet*).  

        >[AZURE.NOTE] Vous pouvez obtenir l’URL à partir des métadonnées RÉPLICON à :         **https://global.replicon.com/ ! /saml2/\<YourCompanyKey\>**.

    3.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en RÉPLICON** , cliquez sur **télécharger les métadonnées**pour télécharger les métadonnées de votre et puis enregistrez les métadonnées sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-replicon-tutorial/IC777804.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise RÉPLICON en tant qu’administrateur.

6.  Pour configurer SAML 2.0, procédez comme suit :

    ![Authentification SAML activer] (./media/active-directory-saas-replicon-tutorial/IC777805.png "Authentification SAML activer")

    1.  Pour afficher la boîte de dialogue **Authentification2 EnableSAML** , ajoutez les éléments suivants à votre URL après votre clé d’entreprise :  
        **/services/SecurityService1.svc/Help/test/EnableSAMLAuthentication2**  
        La figure suivante montre le schéma de l’URL complète :  
        **https://Na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  Cliquez sur le **+** pour développer la section **v20Configuration** .
    3.  Cliquez sur le **+** pour développer la section **metaDataConfiguration** .
    4.  Cliquez sur **Choisir un fichier**, pour sélectionner votre fichier XML de métadonnées du fournisseur identité, puis cliquez sur **Envoyer**.

7.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-replicon-tutorial/IC778418.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à RÉPLICON, ils doivent être configurées dans RÉPLICON.  
Dans le cas de RÉPLICON, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise RÉPLICON en tant qu’administrateur.

2.  Accédez à **Administration \> utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-replicon-tutorial/IC777806.png "Utilisateurs")

3.  Cliquez sur **+ Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-replicon-tutorial/IC777807.png "Ajouter un utilisateur")

4.  Dans la section de **Profil utilisateur** , procédez comme suit :

    ![Profil utilisateur] (./media/active-directory-saas-replicon-tutorial/IC777808.png "Profil utilisateur")

    1.  Dans la zone de texte **Nom de connexion** , tapez l’adresse de messagerie Azure AD de l’utilisateur Azure AD que vous souhaitez mettre en service.
    2.  En tant que **Type d’authentification**, sélectionnez **l’authentification unique**.
    3.  Dans la zone de texte **service** , tapez service de l’utilisateur.
    4.  En tant que **Type d’employé**, sélectionnez **administrateur**.
    5.  Cliquez sur **Enregistrer le profil utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres RÉPLICON utilisateur compte outils de création ou API fournies par RÉPLICON aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-replicon-perform-the-following-steps"></a>Pour affecter des utilisateurs à RÉPLICON, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **RÉPLICON **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-replicon-tutorial/IC777809.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-replicon-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).