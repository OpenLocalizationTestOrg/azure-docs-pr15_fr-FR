<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec cycle de vie de contrôle de code source | Microsoft Azure" 
    description="Découvrez comment utiliser cycle de vie de contrôle de code source avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Didacticiel : Intégration d’Azure Active Directory avec cycle de vie de contrôle de code source
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et cycle de vie de contrôle de code source.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un contrôle de code source du cycle de vie de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez attribué un cycle de vie de contrôle de code source seront en mesure d’authentification unique dans l’application sur votre site de société du cycle de vie contrôle de code source (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration de l’application du cycle de vie de contrôle de code source
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scénario")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>L’activation de l’intégration de l’application du cycle de vie de contrôle de code source
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration de l’application du cycle de vie de contrôle de code source.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>Pour activer l’intégration de l’application du cycle de vie de contrôle de code source, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez le **Cycle de vie de contrôle de code source**.

    ![Galerie d’applications] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Cycle de vie de contrôle de code source**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Cycle de vie de contrôle de code source] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Cycle de vie de contrôle de code source")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à cycle de vie de contrôle de code source avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration application du **Cycle de vie de contrôle de code source** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la politique de contrôle de code source** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application de cycle de vie de contrôle de code source en utilisant le modèle suivant «*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en cycle de vie de contrôle de code source** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à l’équipe de Support du cycle de vie de contrôle de code source.

    >[AZURE.NOTE]Authentification unique doit être activée par l’équipe de support technique de contrôle de code source.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD ouvrir une session cycle de vie de contrôle de code source, ils doivent être configurées cycle de vie de contrôle de code source.
  
Il n’existe aucun élément action pour vous permettent de configurer la configuration des utilisateurs au cycle de vie de contrôle de code source.  
Lorsqu’un utilisateur affecté tente d’ouvrir une session cycle de vie de contrôle de code source, un compte de contrôle de code source du cycle de vie est créé automatiquement si nécessaire.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres cycle de vie de contrôle de code source utilisateur compte outils de création ou API fournies par cycle de vie de contrôle de code source aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Pour attribuer aux utilisateurs au cycle de vie de contrôle de code source, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration application du **Cycle de vie de contrôle de code source **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).