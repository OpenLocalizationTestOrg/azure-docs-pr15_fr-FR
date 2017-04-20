<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel Igloo | Microsoft Azure" 
    description="Découvrez comment utiliser les logiciels Igloo avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Didacticiel : Intégration d’Azure Active Directory avec un logiciel de Igloo
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Igloo logiciel.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Une abonnement activé [Logiciel Igloo](http://www.igloosoftware.com/) d’authentification unique
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez attribué un logiciel Igloo sera en mesure d’authentification unique dans l’application sur votre site de société Igloo logiciel (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Igloo logiciel
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scénario")
##<a name="enabling-the-application-integration-for-igloo-software"></a>L’activation de l’intégration des applications pour Igloo logiciel
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour Igloo logiciel.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Igloo logiciel, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Igloo logiciel**.

    ![Galerie d’applications] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Igloo logiciel**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier au logiciel Igloo avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 à votre client de bureau Central.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Logiciel Igloo** application, cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Igloo logiciel** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD unique authentification] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD unique authentification")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Igloo logiciel URL de connexion** , entrez votre URL à l’aide du modèle suivant «*https://company.igloocommunities.com/?signin*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique chez Igloo Software** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Igloo logiciel en tant qu’administrateur.

6.  Accédez au **Panneau de configuration**.

    ![Le panneau de configuration] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Le panneau de configuration")

7.  Dans l’onglet **adhésion** , cliquez sur **Paramètres de connexion**.

    ![Se connecter paramètres] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Se connecter paramètres")

8.  Dans la section Configuration SAML, cliquez sur **Configurer l’authentification de SAML**.

    ![Configuration SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuration SAML")

9.  Dans la section **Configuration générale** , effectuez les opérations suivantes :

    ![Configuration générale] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuration générale")

    1.  Dans la zone de texte **Nom de la connexion** , tapez un nom personnalisé pour votre configuration.
    2.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique chez Igloo Software** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion IdP** .
    3.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique chez Igloo Software** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion IdP** .
    4.  En tant **réponse déconnexion et le Type de demande HTTP**, sélectionnez **Publier**.
    5.  Créer un fichier texte à partir du certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Supprimer la première ligne et la dernière ligne de la version de fichier texte de votre certificat, copier le texte du certificat restant et collez-le dans la zone de texte **Certificat Public** .

10. Dans **Configuration d’authentification et réponse**, procédez comme suit :

    ![Configuration d’authentification et réponse] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuration d’authentification et réponse")

    1.  En tant que **Fournisseur d’identité**, sélectionnez **Microsoft ADFS**.
    2.  **Identificateur de Type**, sélectionnez **Adresse de messagerie**.
    3.  Dans la zone de texte **Attribut de messagerie** , tapez **emailaddress**.
    4.  Dans la zone de texte **Prénom attribut** , tapez le **prénom**.
    5.  Dans la zone de texte **Dernier attribut de nom** , tapez le **nom de famille**.

11. Effectuez les opérations suivantes pour terminer la configuration :

    ![Création de l’utilisateur sur se connecter] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Création de l’utilisateur sur se connecter")

    1.  **Création de l’utilisateur sur se connecter**, sélectionnez **créer un nouvel utilisateur dans votre site lorsqu’ils se connecter**.
    2.  En tant que **signer dans paramètres**, sélectionnez **utiliser SAML bouton sur écran « Se connecter »**.
    3.  Cliquez sur **Enregistrer**.

12. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer la configuration des utilisateurs au logiciel Igloo.  
Lorsqu’un utilisateur affecté tente de vous connecter à logiciel Igloo en utilisant le panneau d’accès, logiciel Igloo vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par un logiciel Igloo.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Pour attribuer aux utilisateurs au logiciel Igloo, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Logiciel Igloo **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).