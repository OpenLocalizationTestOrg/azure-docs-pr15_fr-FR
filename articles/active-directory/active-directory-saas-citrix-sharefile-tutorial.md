<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Citrix ShareFile | Microsoft Azure" 
    description="Découvrez comment utiliser Citrix ShareFile avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Didacticiel : Azure Active Directory intégration avec Citrix ShareFile

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Citrix ShareFile.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Citrix ShareFile

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Citrix ShareFile seront en mesure d’authentification unique dans l’application sur votre site de société Citrix ShareFile (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Citrix ShareFile
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scénario")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>L’activation de l’intégration des applications pour Citrix ShareFile

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Citrix ShareFile.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Citrix ShareFile, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Citrix ShareFile**.

    ![Galerie d’applications] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Citrix ShareFile**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Citrix ShareFile avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Citrix ShareFile** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Citrix ShareFile** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone **Citrix ShareFile sur URL de connexion** , entrez votre URL à l’aide du modèle suivant `https://<tenant-name>.shareFile.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Citrix ShareFile** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Authentification ConfigureSingle] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Authentification ConfigureSingle")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise **Citrix ShareFile** en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **administrateur**.

7.  Dans le volet de navigation gauche, sélectionnez **Configurer de l’authentification unique**.

    ![Administration des comptes] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administration des comptes")

8.  Sur la **de l’authentification unique / SAML 2.0 Configuration** page de dialogue sous **Paramètres de base**, procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Authentification unique")

    1.  Cliquez sur **Activer SAML**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Citrix ShareFile** , copiez la valeur **d’ID de l’entité** et collez-le dans la **votre émetteur IDP / ID de l’entité** zone de texte.
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Citrix ShareFile** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    4.  Dans le portail classique Azure, sur **la configuration de l’authentification unique en Citrix ShareFile** page de dialogue, copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .
    5.  Cliquez sur **Modifier** en regard du champ **Certificat X.509** , puis téléchargez le certificat que vous avez téléchargé à partir du portail classique Azure AD.
        ![Paramètres de base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Paramètres de base")

9.  Cliquez sur **Enregistrer** dans le portail de gestion Citrix ShareFile.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Citrix ShareFile, ils doivent être mis en service dans Citrix ShareFile.  
Dans le cas de Citrix ShareFile, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Citrix ShareFile** .

2.  Cliquez sur **Gérer les utilisateurs \> gérer les utilisateurs accueil \> + créer employé**.

    ![Créer des employés] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Créer des employés")

3.  Entrez la **messagerie**, le **prénom** et **nom** d’une annonce Azure valide compte que vous souhaitez mettre en service.

    ![Informations de base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informations de base")

4.  Cliquez sur **Ajouter un utilisateur**.

    >[AZURE.NOTE] Le titulaire du compte DAS recevra un message électronique et suivre un lien pour confirmer leur propre compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Citrix ShareFile utilisateur compte outils de création ou API fournies par Citrix ShareFile aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Pour affecter des utilisateurs à Citrix ShareFile, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Citrix ShareFile **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
