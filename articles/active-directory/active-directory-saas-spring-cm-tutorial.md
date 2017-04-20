<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ressort CM | Microsoft Azure" 
    description="Découvrez comment utiliser ressort CM avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Didacticiel : Intégration d’Azure Active Directory avec ressort CM
  
L’objectif de ce didacticiel consiste à afficher la configuration de l’authentification unique entre Azure Active Directory et SpringCM.
  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement SpringCM d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affecté à SpringCM sera en mesure de l’authentification unique à l’aide du panneau d’accès AAD.

1.  L’activation de l’intégration des applications pour SpringCM
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scénario")

##<a name="enabling-the-application-integration-for-springcm"></a>L’activation de l’intégration des applications pour SpringCM
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour SpringCM.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Pour activer l’intégration des applications pour SpringCM, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **SpringCM**.

    ![Galerie d’applications] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **SpringCM**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier à SpringCM avec leur propre compte dans Azure Active Directory, à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **SpringCM** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SpringCM** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se SpringCM d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application SpringCM, puis cliquez sur **suivant**. 

    L’URL de l’application est l’URL de votre client SpringCM (par exemple : *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*) :

    ![Configurer des URL de l’application] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en SpringCM** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous au site de votre entreprise **SpringCM** en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **Aller à**, cliquez sur **Préférences**et puis, dans la section **Préférences du compte** , cliquez sur **L’authentification unique SAML**.

    ![Authentification unique SAML] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "Authentification unique SAML")

7.  Dans la section Configuration de fournisseur d’identité, effectuez les opérations suivantes :

    ![Configuration de fournisseur d’identité] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuration de fournisseur d’identité")

    1.  Pour télécharger votre certificat Azure Active Directory téléchargé, cliquez sur **Sélectionner un certificat émetteur** ou **Changer l’émetteur de certificat**.
    2.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique en SpringCM** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    3.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique en SpringCM** , copiez la valeur **Singel authentification URL du Service** et collez-le dans la zone de texte de **Point de terminaison initié Service Provider (SP)** .
    4.  En tant **SAML activé**, sélectionnez **Activer**.
    5.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurer la fonctionnalité d’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure Active Directory pour vous connecter à SpringCM, ils doivent être configurées dans SpringCM.  
Dans le cas SpringCM, mise en service est une tâche manuelle.

>[AZURE.NOTE] Pour plus d’informations, voir [créer et modifier un utilisateur SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à SpringCM, effectuez les opérations suivantes :

1.  Connectez-vous à votre site d’entreprise **SpringCM** en tant qu’administrateur.

2.  Cliquez sur **Atteindre**, puis cliquez sur **Carnet d’adresses**.

    ![Création d’utilisateur] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Création d’utilisateur")

3.  Cliquez sur **créer un utilisateur**.

4.  Sélectionnez un **rôle d’utilisateur**.

5.  Sélectionnez **Envoyer des messages électroniques de l’Activation**.

6.  Tapez le prénom, le nom et l’adresse de messagerie d’un compte d’utilisateur Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.

7.  Ajouter l’utilisateur à un **groupe de sécurité**.

8.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SpringCM utilisateur compte outils de création ou API fournies par SpringCM aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Pour affecter des utilisateurs à SpringCM, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **SpringCM** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




