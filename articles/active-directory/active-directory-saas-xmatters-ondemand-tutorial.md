<properties 
    pageTitle="Didacticiel : Azure Active Directory l’intégration avec xMatters à la demande | Microsoft Azure"
    description="Découvrez comment utiliser xMatters à la demande avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Didacticiel : Azure Active Directory l’intégration avec xMatters à la demande
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et xMatters à la demande. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client à la demande xMatters
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à xMatters à la demande seront en mesure d’authentification unique dans l’application auprès de votre site d’entreprise à la demande xMatters (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour xMatters à la demande
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scénario")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>L’activation de l’intégration des applications pour xMatters à la demande
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour xMatters à la demande.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Pour activer l’intégration des applications pour xMatters à la demande, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **xMatters à la demande**.

    ![Galerie d’applications] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **XMatters à la demande**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![xMatters à la demande] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters à la demande")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à XMatters OnDemand avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **À la demande XMatters** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à XMatters à la demande** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurer des URL de l’application")

    un. Dans la zone de texte **XMatters à la demande URL de connexion** , tapez l’URL de votre selon le modèle suivant :`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Cliquez sur **suivant**.


4.  Dans la page **configuration de l’authentification unique en XMatters à la demande** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement en tant que **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Vous devez transférer le certificat à l’équipe de support xMatters. Le certificat doit être téléchargé par l’équipe de support xMatters avant que vous pouvez finaliser la configuration de l’authentification unique.

    ![Ouverture de session configurer unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Ouverture de session configurer unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise XMatters OnDemand en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **administrateur**, puis cliquez sur **Détails de la société** dans la barre de navigation sur le côté gauche.

    ![Administrateur] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Administrateur")

7.  Dans la page **Configuration de SAML** , effectuez les opérations suivantes :

    ![Configuration SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuration SAML")

    1.  Sélectionnez **Activer SAML**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en XMatters à la demande** , copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte **ID de fournisseur d’identité** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en XMatters à la demande** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **Se sur URL unique** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en XMatters à la demande** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL déconnexion unique** .
    5.  Dans la page Détails de la société, dans la partie supérieure, cliquez sur **Enregistrer les modifications**.
        ![Détails de la société] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Détails de la société")

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Ouverture de session configurer unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Ouverture de session configurer unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à XMatters OnDemand, ils doivent être mis en service dans XMatters OnDemand.  
Dans le cas XMatters OnDemand, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **À la demande XMatters** .

2.  Cliquez sur l’onglet **utilisateurs** .

3.  Cliquez sur **Ajouter un utilisateur**.

    ![Utilisateurs] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utilisateurs")

4.  Sélectionnez **actif**.

5.  Dans la section **Ajouter un utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Ajouter un utilisateur")

    1.  Entrez le **nom d’utilisateur**, le **prénom**, le **prénom**, le **Site** d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres XMatters OnDemand utilisateur compte outils de création ou API fournies par XMatters OnDemand aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Pour affecter des utilisateurs à XMatters OnDemand, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **À la demande XMatters **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).