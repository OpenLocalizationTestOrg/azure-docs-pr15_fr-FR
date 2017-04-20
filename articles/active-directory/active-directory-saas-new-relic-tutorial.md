<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Relic nouveau | Microsoft Azure" 
    description="Découvrez comment utiliser Relic nouvelle avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Didacticiel : Intégration d’Azure Active Directory avec Relic nouveau
  
L’objectif de ce didacticiel consiste à afficher la configuration de l’authentification unique entre Azure Active Directory et Relic nouveau.
  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un nouveau Relic de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affecté à nouveau Relic sera en mesure de l’authentification unique à l’aide du panneau d’accès AAD.

1.  L’activation de l’intégration des applications pour Relic nouveau
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scénario")
##<a name="enabling-the-application-integration-for-new-relic"></a>L’activation de l’intégration des applications pour Relic nouveau
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour Relic nouveau.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Relic nouveau, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez le **Nouveau Relic**.

    ![Galerie d’applications] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Relic nouveau**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Nouveau Relic] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Nouveau Relic")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier à nouveau Relic avec leur propre compte dans Azure Active Directory, à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Relic nouvelle** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à nouveau Relic** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Nouveau se Relic d’URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Relic nouveau, puis cliquez sur **suivant**. 

    L’URL de l’application est l’URL de votre client Relic nouveau (par exemple : *https://rpm.newrelic.com*) :

    ![Configurer des URL de l’application] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à nouveau Relic** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, s’à votre site d’entreprise **Relic nouvelle** en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **Paramètres du compte**.

    ![Paramètres du compte] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Paramètres du compte")

7.  Cliquez sur l’onglet **sécurité et l’authentification** , puis cliquez sur l’onglet **authentification unique** .

    ![Authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "Authentification unique")

8.  Dans la page de dialogue SAML, effectuez les opérations suivantes :

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Cliquez sur **Choisir un fichier** pour télécharger votre certificat Azure Active Directory téléchargé.
    2.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique à nouveau Relic** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion à distance** .
    3.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique à nouveau Relic** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL d’accueil de déconnexion** .
    4.  Cliquez sur **Enregistrer mes modifications**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure Active Directory pour vous connecter à nouveau Relic, ils doivent être configurées dans Relic nouveau.  
Dans le cas de nouveau Relic, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à nouveau Relic, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **Relic nouvelle** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **Paramètres du compte**.

    ![Paramètres du compte] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Paramètres du compte")

3.  Dans le volet de **compte** sur le côté gauche, cliquez sur **synthèse**, puis cliquez sur **Ajouter un utilisateur**.

    ![Paramètres du compte] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Paramètres du compte")

4.  Dans la boîte de dialogue **utilisateurs actifs** , procédez comme suit :

    ![Utilisateurs actifs] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Utilisateurs actifs")

    1.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service.
    2.  En tant que **rôle** sélectionnez **utilisateur**.
    3.  Cliquez sur **Ajouter cet utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Relic nouvel utilisateur compte outils de création ou API fournies par Relic nouveau aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Pour affecter des utilisateurs à nouveau Relic, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Relic nouvelle** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




