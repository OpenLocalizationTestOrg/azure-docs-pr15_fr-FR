<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SumoLogic | Microsoft Azure" 
    description="Découvrez comment utiliser SumoLogic avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Didacticiel : Azure Active Directory intégration avec SumoLogic
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et SumoLogic.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client SumoLogic
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à SumoLogicwill être en mesure de même signe dans l’application auprès de votre SumoLogic société site (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour SumoLogic
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scénario")

##<a name="enabling-the-application-integration-for-sumologic"></a>L’activation de l’intégration des applications pour SumoLogic
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour SumoLogic.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Pour activer l’intégration des applications pour SumoLogic, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **sumologic**.

    ![Galerie d’applications] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **SumoLogic**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à SumoLogic avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 dans votre SumoLogictenant.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **SumoLogic** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SumoLogic** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **SumoLogic URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. SumoLogic.com*», puis cliquez sur **suivant**.

    ![Configurer aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurer aoo URL")

4.  Dans la page **configuration de l’authentification unique en SumoLogic** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise SumoLogic en tant qu’administrateur.

6.  Accédez à **gérer \> sécurité**.

    ![Gérer] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gérer")

7.  Cliquez sur **SAML**.

    ![Paramètres de sécurité globaux] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Paramètres de sécurité globaux")

8.  Dans la liste **Sélectionner une configuration ou créer un nouveau** , sélectionnez **Azure AD**, puis cliquez sur **configurer**.

    ![Configurer SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurer SAML 2.0")

9.  Dans la boîte de dialogue **configurer SAML 2.0** , procédez comme suit :

    ![Configurer SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurer SAML 2.0")

    1.  Dans la zone de texte **Nom de la Configuration** , tapez **Azure AD**.
    2.  Sélectionnez **le Mode débogage**.
    3.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en SumoLogic** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    4.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en SumoLogic** , copiez la valeur de **l’URL de la demande d’authentification** et puis collez-les dans la zone de texte **URL de la requête seul** .
    5.  Créer un fichier **codé en Base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et collez l’intégralité du certificat dans la zone de texte **Certificat X.509** .
    7.  En tant qu' **Attribut de messagerie**, sélectionnez **utiliser SAML sujet**.
    8.  Sélectionnez **SP initié Configuration de connexion**.
    9.  Dans la zone de texte **Chemin d’accès de connexion** , tapez **Azure**.
    10. Cliquez sur **Enregistrer**.

10. Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en SumoLogic** , sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à SumoLogic, ils doivent être configurées pour SumoLogic.  
Dans le cas SumoLogic, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **SumoLogic** .

2.  Accédez à **gérer \> utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utilisateurs")

3.  Cliquez sur **Ajouter**.

    ![Utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utilisateurs")

4.  Dans la boîte de dialogue **Nouvel utilisateur** , procédez comme suit :

    ![Nouvel utilisateur] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nouvel utilisateur")

    1.  Tapez les informations associées du compte Azure AD que vous souhaitez mettre en service dans les zones de texte **prénom**, **Nom** et **courrier électronique** .
    2.  Sélectionnez un rôle.
    3.  Sous **statut**, sélectionnez **actif**.
    4.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SumoLogic utilisateur compte outils de création ou API fournies par SumoLogic aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Pour affecter des utilisateurs à SumoLogic, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **SumoLogic** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).