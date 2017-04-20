<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ScreenSteps | Microsoft Azure" 
    description="Découvrez comment utiliser ScreenSteps avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Didacticiel : Intégration d’Azure Active Directory avec ScreenSteps
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et ScreenSteps.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client ScreenSteps
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à ScreenSteps sera en mesure d’authentification unique dans l’application sur votre site de société ScreenSteps (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour ScreenSteps
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scénario")
##<a name="enabling-the-application-integration-for-screensteps"></a>L’activation de l’intégration des applications pour ScreenSteps
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Pour activer l’intégration des applications pour ScreenSteps, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **ScreenSteps**.

    ![Galerie d’applications] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **ScreenSteps**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à ScreenSteps avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **ScreenSteps** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ScreenSteps** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **ScreenSteps URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. ScreenSteps.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en ScreenSteps** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise ScreenSteps en tant qu’administrateur.

6.  Cliquez sur **Gestion des comptes**.

    ![Gestion des comptes] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestion des comptes")

7.  Cliquez sur **l’authentification à distance**.

    ![Authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Authentification à distance")

8.  Cliquez sur **créer d’authentification extrémité**.

    ![Authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Authentification à distance")

9.  Dans la section **Création d’un point de terminaison d’authentification** , procédez comme suit :

    ![Créer un point de terminaison d’authentification] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Créer un point de terminaison d’authentification")

    1.  Dans la zone de texte **titre** , tapez un titre.
    2.  Dans la liste **Mode** , sélectionnez **SAML**.
    3.  Cliquez sur **créer**.

10. Dans la section **Point de terminaison d’authentification distant** , procédez comme suit :

    ![Point de terminaison de l’authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Point de terminaison de l’authentification à distance")

    1.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique en ScreenSteps** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion à distance** .
    2.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique en ScreenSteps** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL vous déconnecter** .
    3.  Cliquez sur **Choisir un fichier**, puis téléchargez le certificat téléchargé.
    4.  Cliquez sur **mettre à jour**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à **ScreenSteps**, ils doivent être configurées dans **ScreenSteps**.  
Dans le cas **ScreenSteps**, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à ScreenSteps, effectuez les opérations suivantes :

1.  Connectez-vous à votre client **ScreenSteps** .

2.  Cliquez sur **Gestion des comptes**.

    ![Gestion des comptes] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestion des comptes")

3.  Cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utilisateurs")

4.  Cliquez sur **créer un utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Tous les utilisateurs")

5.  Dans la liste **Rôle d’utilisateur** , sélectionnez un rôle pour l’utilisateur.

6.  Dans la section rôle d’utilisateur, tapez «**prénom**, **nom**, **messagerie**, **connexion**, **mot de passe** et **Confirmation de mot de passe**» d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.

    ![Nouvel utilisateur] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nouvel utilisateur")

7.  Dans la section groupes, sélectionnez le **Groupe d’authentification (SAML)**, puis cliquez sur **Créer un utilisateur**.

    ![Groupes] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groupes")

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres ScreenSteps utilisateur compte outils de création ou API fournies par ScreenSteps aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Pour affecter des utilisateurs à ScreenSteps, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **ScreenSteps **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Attribuer aux utilisateurs")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).