<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec InsideView | Microsoft Azure" 
    description="Découvrez comment utiliser InsideView avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Didacticiel : Intégration d’Azure Active Directory avec InsideView
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et InsideView.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client InsideView
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à InsideView sera en mesure d’authentification unique dans l’application sur votre site de société InsideView (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour InsideView
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Scénario")
##<a name="enabling-the-application-integration-for-insideview"></a>L’activation de l’intégration des applications pour InsideView
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour InsideView.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Pour activer l’intégration des applications pour InsideView, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **InsideView**.

    ![Galerie d’applications] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **InsideView**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à InsideView avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **InsideView** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à InsideView** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **URL de réponse InsideView** , tapez l’URL de votre InsideView l’authentification unique (par exemple : `https://my.insideview.com/iv/<STS Name>/login.iv`), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en InsideView** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise InsideView en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **administrateur**, **SingleSignOn paramètres**, puis sur **Ajouter SAML**.

    ![Authentification unique SAML paramètres] (./media/active-directory-saas-insideview-tutorial/IC794135.png "Authentification unique SAML paramètres")

7.  Dans la section **Ajouter un nouveau SAML** , effectuez les opérations suivantes :

    ![Ajouter un nouveau SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Ajouter un nouveau SAML")

    1.  Dans la zone de texte **Nom STS** , tapez un nom pour votre configuration.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en InsideView** , copiez la valeur de **Point de terminaison initié Service Provider (SP)** et collez-le dans la zone de texte de **Point de terminaison Unsolicated SamlP/Web-Fed** .
    3.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **STS du certificat**
    5.  Dans la zone de texte **Crm de mappage d’Id d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  Dans la zone de texte **Crm mappage de messagerie** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Dans la zone de texte **Crm prénom mappage** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  Dans la zone de texte **nom Crm mappage** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à InsideView, ils doivent être configurées dans InsideView.  
Dans le cas InsideView, mise en service est une tâche manuelle.
  
Pour obtenir des utilisateurs ou des contacts créés dans InsideView, contactez votre responsable de réussite client ou envoyer un message électronique à**support@insideview.com**

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres InsideView utilisateur compte outils de création ou API fournies par InsideView mise en service des comptes d’utilisateurs Azure AD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Pour affecter des utilisateurs à InsideView, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **InsideView **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).