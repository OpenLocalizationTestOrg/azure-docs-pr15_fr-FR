<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une marge | Microsoft Azure" 
    description="Découvrez comment utiliser marge avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure Active Directory avec une marge
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et marge.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Une marge de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à marge sera en mesure d’authentification unique dans l’application sur votre site d’entreprise marge (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration de l’application de marge
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-slack-tutorial/IC794980.png "Scénario")

##<a name="enabling-the-application-integration-for-slack"></a>L’activation de l’intégration de l’application de marge
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour marge.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Pour activer l’intégration des applications pour marge, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-slack-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-slack-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **marge**.

    ![Galerie d’applications] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **marge**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Scénario] (./media/active-directory-saas-slack-tutorial/IC796925.png "Scénario")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à marge avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **marge** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à marge** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Marge URL de connexion** , tapez l’URL de votre client marge (par exemple : «*https://azuread.slack.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en marge** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise marge en tant qu’administrateur.

6.  Accédez à **à Microsoft Azure AD \> paramètres de l’équipe**.

    ![Paramètres d’équipe] (./media/active-directory-saas-slack-tutorial/IC794986.png "Paramètres d’équipe")

7.  Dans la section **Paramètres d’équipe** , cliquez sur l’onglet **authentification** , puis cliquez sur **Modifier les paramètres**.

    ![Paramètres d’équipe] (./media/active-directory-saas-slack-tutorial/IC794987.png "Paramètres d’équipe")

8.  Dans la boîte de dialogue **Paramètres d’authentification SAML** , effectuez les opérations suivantes :

    ![Paramètres SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Paramètres SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en marge** , copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **SAML 2.0 point de terminaison HTTP ()** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en marge** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte **Émetteur de fournisseur d’identité** .
    3.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.
    
        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat Public** .
    5.  Désactivez l’option **Autoriser les utilisateurs à modifier leur adresse de messagerie**.
    6.  Sélectionnez **Autoriser les utilisateurs pour choisir leur propre nom d’utilisateur**.
    7.  **Authentification de votre équipe doit être utilisée par**, sélectionnez **il est facultative**.
    8.  Cliquez sur **Enregistrer la Configuration**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à marge, ils doivent être configurées dans la marge.
  
Il n’existe aucun élément action pour vous permettent de configurer la configuration des utilisateurs à marge.  
Lorsqu’un utilisateur tente de se connecter dans la marge, un compte marge est créé automatiquement si nécessaire.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Pour affecter des utilisateurs à marge, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **marge **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-slack-tutorial/IC794990.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-slack-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).