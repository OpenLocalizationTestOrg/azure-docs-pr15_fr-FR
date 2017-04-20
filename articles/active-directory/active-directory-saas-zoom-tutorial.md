<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Zoom | Microsoft Azure" 
    description="Découvrez comment utiliser Zoom avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Didacticiel : Intégration d’Azure Active Directory avec Zoom
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Zoom.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client de Zoom
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Zoom pourront authentification unique dans l’application sur votre site de société Zoom (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md)
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Zoom
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Scénario")

##<a name="enabling-the-application-integration-for-zoom"></a>L’activation de l’intégration des applications pour Zoom
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Zoom, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **effectuer un zoom avant**.

    ![Galerie d’applications] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Zoom**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Effectuer un zoom avant] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Effectuer un zoom avant")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier pour effectuer un Zoom avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page **effectuer un zoom avant** de l’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784696.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Zoom** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784697.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Zoom URL de connexion** , entrez votre URL à l’aide du modèle suivant «*http://company.zoom.us*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Zoom** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784699.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Zoom en tant qu’administrateur.

6.  Cliquez sur l’onglet **De l’authentification unique** .

    ![Authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784700.png "Authentification unique")

7.  Cliquez sur l’onglet **Contrôle de sécurité** , puis accédez aux paramètres **De l’authentification unique** .

8.  Dans la section authentification unique, procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784701.png "Authentification unique")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Zoom** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL de la page se connecter** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Zoom** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL de la page de déconnexion** .
    3.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **certificat fournisseur d’identité**
    5.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Zoom** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784702.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Zoom, ils doivent être configurées dans Zoom.  
Dans le cas de Zoom, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **effectuer un zoom avant** en tant qu’administrateur.

2.  Cliquez sur l’onglet **Gestion des comptes** , puis cliquez sur **Gestion des utilisateurs**.

3.  Dans la section Gestion des utilisateurs, cliquez sur **Ajouter des utilisateurs**.

    ![Gestion des utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Gestion des utilisateurs")

4.  Dans la page **Ajouter des utilisateurs** , effectuez les opérations suivantes :

    ![Ajouter des utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Ajouter des utilisateurs")

    1.  En tant que **Type d’utilisateur**, sélectionnez **simple**.
    2.  Dans la zone de texte des **messages électroniques** , tapez l’adresse de messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    3.  Cliquez sur **Ajouter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Zoom utilisateur compte outils de création ou API fournies par Zoom mise en service DAS les comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Pour attribuer aux utilisateurs pour effectuer un Zoom, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page **effectuer un zoom avant **de l’intégration d’application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).