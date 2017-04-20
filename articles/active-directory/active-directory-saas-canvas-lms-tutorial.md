<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory zone LMS | Microsoft Azure" 
    description="Découvrez comment utiliser toile LMS avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Didacticiel : Intégration d’Azure Active Directory zone LMS

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et zone de dessin.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client de la zone de dessin

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à la zone de dessin seront en mesure d’authentification unique dans l’application sur votre site de société toile (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour la zone de dessin
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scénario")
##<a name="enabling-the-application-integration-for-canvas"></a>L’activation de l’intégration des applications pour la zone de dessin

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour la zone de dessin.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Pour activer l’intégration des applications pour la zone de dessin, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **zone de dessin**.

    ![Galerie d’applications] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **zone de dessin**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Zone de dessin")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier sur zone de dessin avec leur propre compte dans Azure Active Directory à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour la zone de dessin, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, Découvrez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **zone de dessin** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la zone de dessin** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone **Zone URL de connexion** , entrez votre URL à l’aide du modèle suivant `https://<tenant-name>.instructure.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en zone de dessin** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise de zone de dessin en tant qu’administrateur.

6.  Accédez à **cours \> les comptes gérés \> Microsoft**.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zone de dessin")

7.  Dans le volet de navigation gauche, sélectionnez **l’authentification**, puis cliquez sur **Ajouter nouveau SAML Config**.

    ![Authentification] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentification")

8.  Dans la page intégration actuelle, effectuez les opérations suivantes :

    ![Intégration actuelle] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Intégration actuelle")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en zone de dessin** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité IdP** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en zone de dessin** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion sur** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en zone de dessin** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL absence de connexion** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en zone de dessin** , copiez la valeur de **Modifier votre mot de passe l’URL** et collez-le dans la zone de texte **Lien de mot de passe de modification** .
    5.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Dans la liste des **Attributs de connexion** , sélectionnez **NameID**.
    7.  Dans la liste **Format de l’identificateur** , sélectionnez **emailAddress**.
    8.  Cliquez sur **Enregistrer les paramètres d’authentification**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à la zone de dessin, ils doivent être configurées dans la zone de dessin.  
Dans le cas de zone de dessin, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client de la **zone de dessin** .

2.  Accédez à **cours \> les comptes gérés \> Microsoft**.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zone de dessin")

3.  Cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Utilisateurs")

4.  Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Utilisateurs")

5.  Dans la zone Ajouter une page de la boîte de dialogue Nouvel utilisateur, procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Ajouter un utilisateur")

    1.  Dans la zone de texte **Nom complet** , tapez le nom d’utilisateur.
    2.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie de l’utilisateur.
    3.  Dans la zone de texte de **connexion** , tapez l’adresse de messagerie de l’utilisateur Azure AD.
    4.  Sélectionnez **l’utilisateur sur la création de ce compte de messagerie**.
    5.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres zone de dessin utilisateur compte outils de création ou API fournies par la zone de dessin aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Pour attribuer des utilisateurs à la zone de dessin, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **zone de dessin **, cliquez sur **attribuer aux utilisateurs**.

    ![Affectation d’utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Affectation d’utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
