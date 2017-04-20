<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec BAMBOU HR | Microsoft Azure" 
    description="Découvrez comment utiliser BAMBOU HR avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Didacticiel : Azure Active Directory intégration avec BAMBOU HR

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et BambooHR.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement BambooHR d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à BambooHR sera en mesure d’authentification unique dans l’application sur votre site de société BambooHR (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour BambooHR
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")
##<a name="enabling-the-application-integration-for-bamboohr"></a>L’activation de l’intégration des applications pour BambooHR

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour BambooHR.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Pour activer l’intégration des applications pour BambooHR, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **BambooHR**.

    ![Galerie d’applications] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **BambooHR**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à BambooHR avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **BambooHR** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Scénario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à BambooHR** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se BambooHR d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application BambooHR (par exemple : https://company.bamboohr.com), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en BambooHR** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise BambooHR en tant qu’administrateur.

6.  Sur la page d’accueil, effectuez les opérations suivantes :

    ![Authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Authentification unique")

    1.  Cliquez sur **applications**.
    2.  Dans le menu applications sur la gauche, cliquez sur **Authentification unique**.
    3.  Cliquez sur **SAML SSO**.

7.  Dans la section **SAML SSO** , effectuez les opérations suivantes :

    ![SAML SSO] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML SSO")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en BambooHR** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL de connexion de l’authentification unique** .
    2.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat X.509**
    4.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à BambooHR, ils doivent être configurées dans BambooHR.  
Dans le cas BambooHR, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site **BambooHR** en tant qu’administrateur.

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**.

    ![Définition] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Définition")

3.  Cliquez sur **Aperçu**.

4.  Dans le volet de navigation gauche, accédez à **sécurité \> utilisateurs**.

5.  Tapez l’adresse e-mail et le mot de passe, nom utilisateur d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.

6.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres BambooHR utilisateur compte outils de création ou API fournies par BambooHR aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Pour affecter des utilisateurs à BambooHR, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **BambooHR **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
