<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AppDynamics | Microsoft Azure" 
    description="Découvrez comment utiliser AppDynamics avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Didacticiel : Intégration d’Azure Active Directory avec AppDynamics

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et AppDynamics. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement AppDynamics d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à AppDynamics sera en mesure d’authentification unique dans l’application sur votre site de société AppDynamics (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour AppDynamics
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scénario")
##<a name="enabling-the-application-integration-for-appdynamics"></a>L’activation de l’intégration des applications pour AppDynamics

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour AppDynamics.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Pour activer l’intégration des applications pour AppDynamics, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **AppDynamics**.

    ![Galerie d’applications] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **AppDynamics**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à AppDynamics avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **AppDynamics** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à AppDynamics** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se AppDynamics d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur AppDynamics («*https://companyname.saas.appdynamics.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en AppDynamics** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise AppDynamics en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**, puis cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  Cliquez sur l’onglet **Fournisseur d’authentification** .

    ![Fournisseur d’authentification] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Fournisseur d’authentification")

8.  Dans la section **Fournisseur d’authentification** , procédez comme suit :

    ![Configuration SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuration SAML")

    1.  En tant que **Fournisseur d’authentification**, sélectionnez **SAML**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en AppDynamics** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en AppDynamics** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .
    4.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte de **certificat**
    6.  Cliquez sur **Enregistrer**.
        ![Enregistrer] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Enregistrer")

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à AppDynamics, ils doivent être configurées dans AppDynamics.  
Dans le cas AppDynamics, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise AppDynamics en tant qu’administrateur.

2.  Accédez à **utilisateurs**, puis cliquez sur **+** pour ouvrir la boîte de dialogue **Créer un utilisateur** .

    ![Utilisateurs] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Utilisateurs")

3.  Dans la section **Créer un utilisateur** , procédez comme suit :

    ![Création d’utilisateur] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Création d’utilisateur")

    1.  Tapez le **nom d’utilisateur**, **nom**, **messagerie**, **Nouveau mot de passe**, **Répétez le nouveau mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AppDynamics utilisateur compte outils de création ou API fournies par AppDynamics mise en service des comptes d’utilisateurs Azure AD.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Pour affecter des utilisateurs à AppDynamics, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **AppDynamics **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
