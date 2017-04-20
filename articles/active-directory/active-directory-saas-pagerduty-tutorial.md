<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Pagerduty | Microsoft Azure" 
    description="Découvrez comment utiliser Pagerduty avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Didacticiel : Azure Active Directory intégration avec Pagerduty
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Pagerduty.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Pagerduty
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Pagerduty sera en mesure d’authentification unique dans l’application sur votre site de société Pagerduty (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Pagerduty
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scénario")
##<a name="enabling-the-application-integration-for-pagerduty"></a>L’activation de l’intégration des applications pour Pagerduty
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Pagerduty.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Pagerduty, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Pagerduty**.

    ![Galerie d’applications] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Pagerduty**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Pagerduty avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Pagerduty** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Pagerduty** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Pagerduty URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Pagerduty.com*», puis cliquez sur **suivant**.

    ![Url de l’application de configuration] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Url de l’application de configuration")

4.  Dans la page **configuration de l’authentification unique en Pagerduty** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Pagerduty en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **Paramètres du compte**.

    ![Paramètres du compte] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Paramètres du compte")

7.  Cliquez sur **authentification unique**.

    ![Authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Authentification unique")

8.  Dans la page s’activer unique (SSO), procédez comme suit :

    ![Activer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Activer l’authentification unique")

    1.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    2.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat X.509**
    3.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en Pagerduty** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    4.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en Pagerduty** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .
    5.  Sélectionnez **Activer l’ouverture de session unique**.
    6.  Cliquez sur **Enregistrer les modifications**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Pagerduty, ils doivent être configurées dans Pagerduty.  
Dans le cas Pagerduty, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Pagerduty** .

2.  Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

3.  Cliquez sur **Ajouter des utilisateurs**.

    ![Ajouter des utilisateurs] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Ajouter des utilisateurs")

4.  Dans la boîte de dialogue **inviter votre équipe** , tapez **prénom et votre nom** et l’adresse de **messagerie** de l’utilisateur Azure AD que vous voulez mettre en service, cliquez sur **Ajouter**, puis sur **Envoyer invite**.

    ![Inviter votre équipe] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Inviter votre équipe")

    >[AZURE.NOTE] Tous les utilisateurs ajoutés recevront une invitation pour créer un compte PagerDuty.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Pagerduty utilisateur compte outils de création ou API fournies par Pagerduty aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Pour affecter des utilisateurs à Pagerduty, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Pagerduty **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).