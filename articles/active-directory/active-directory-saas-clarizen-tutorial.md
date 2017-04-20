<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Clarizen | Microsoft Azure" 
    description="Découvrez comment utiliser Clarizen avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Didacticiel : Intégration d’Azure Active Directory avec Clarizen

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Clarizen.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Clarizen d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Clarizen sera en mesure d’authentification unique dans l’application sur votre site de société Clarizen (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Clarizen
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scénario")
##<a name="enabling-the-application-integration-for-clarizen"></a>L’activation de l’intégration des applications pour Clarizen

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Clarizen.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Clarizen, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Clarizen**.

    ![Galerie d’applications] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Clarizen**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Clarizen avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Clarizen** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Clarizen** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurer l’authentification unique")

3.  Dans la page **configuration de l’authentification unique en Clarizen** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise **Clarizen** en tant qu’administrateur (par exemple : *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Cliquez sur votre nom d’utilisateur, puis cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Paramètres")

6.  Cliquez sur l’onglet **Paramètres globaux** et puis, en regard **Authentification fédérés**, cliquez sur **Modifier**.

    ![Paramètres globaux] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Paramètres globaux")

7.  Dans la boîte de dialogue **Authentification fédéré** , effectuez les opérations suivantes :

    ![Authentification de fédéré] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Authentification de fédéré")

    1.  Cliquez sur **Télécharger** pour télécharger votre certificat téléchargé.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Clarizen** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL de connexion** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configurer unique déconnexion en Clarizen** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL Sign-out** .
    4.  Sélectionnez **utiliser billet**.
    5.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Clarizen, ils doivent être configurées dans Clarizen.  
Dans le cas Clarizen, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Clarizen** en tant qu’administrateur.

2.  Cliquez sur **personnes**.

    ![Personnes] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Personnes")

3.  Cliquez sur **inviter un utilisateur**.

    ![Inviter des utilisateurs] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Inviter des utilisateurs")

4.  Dans la page de dialogue inviter des personnes, effectuez les opérations suivantes :

    ![Inviter des personnes] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Inviter des personnes")

    1.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **inviter**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un message électronique et suivre un lien pour confirmer leur propre compte avant d’être active.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Pour affecter des utilisateurs à Clarizen, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Clarizen **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
