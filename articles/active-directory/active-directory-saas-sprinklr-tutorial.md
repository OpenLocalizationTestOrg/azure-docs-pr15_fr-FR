<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Sprinklr | Microsoft Azure" 
    description="Découvrez comment utiliser Sprinklr avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Didacticiel : Intégration d’Azure Active Directory avec Sprinklr
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Sprinklr.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Sprinklr
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Sprinklr sera en mesure d’authentification unique dans l’application sur votre site de société Sprinklr (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Sprinklr
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scénario")

##<a name="enabling-the-application-integration-for-sprinklr"></a>L’activation de l’intégration des applications pour Sprinklr
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Sprinklr.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Sprinklr, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Sprinklr**.

    ![Galerie d’applications] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Sprinklr**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Sprinklr avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Sprinklr** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Sprinklr** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Sprinklr URL de connexion** , tapez votre URL à l’aide du modèle suivant «*https://\<nom de client\>. sprinklr.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Sprinklr** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Sprinklr en tant qu’administrateur.

6.  Accédez à **Administration \> paramètres**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  Accédez à **partenaire gérer \> authentification unique** sur dans le volet gauche.

    ![Gérer les partenaires] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gérer les partenaires")

8.  Cliquez sur **+ authentification unique modules complémentaires**.

    ![Connexions uniques] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Connexions uniques")

9.  Dans la page de **l’Authentification unique** , procédez comme suit :

    ![Connexions uniques] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Connexions uniques")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : *WAADSSOTest*).
    2.  Sélectionnez **activé**.
    3.  Sélectionnez **Utiliser un nouveau certificat l’authentification unique**.
    4.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat fournisseur d’identité** ,
    6.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Sprinklr** , copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte **Id de l’entité** .
    7.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Sprinklr** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL Login du fournisseur d’identité** .
    8.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Sprinklr** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    9.  En tant que **Type d’ID utilisateur SAML**, sélectionnez **Assertion contient utilisateur « nom d’utilisateur de s sprinklr.com**.
    10. En tant qu' **Emplacement de l’ID utilisateur SAML**, sélectionnez **nom d’utilisateur est dans l’élément identificateur de nom de l’instruction objet**.
    11. Fermez **Enregistrer**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour les utilisateurs AAD peut se connecter, ils doivent être configurées pour l’accès à l’intérieur de l’application Sprinklr.  
Cette section décrit comment créer des comptes d’utilisateur AAD à l’intérieur de Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur dans Sprinklr, effectuez les opérations suivantes :

1.  Connectez-vous à votre site d’entreprise Sprinklr en tant qu’administrateur.

2.  Accédez à **Administration \> paramètres**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  Accédez à **Manage Client \> utilisateurs** dans le volet gauche.

    ![Paramètres] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Paramètres")

4.  Cliquez sur **Ajouter un utilisateur**.

    ![Paramètres] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Paramètres")

5.  Dans la boîte de dialogue **Modifier l’utilisateur** , procédez comme suit :

    ![Modifier l’utilisateur] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifier l’utilisateur")

    1.  Dans la **messagerie**, le **prénom** et le **Nom** des zones de texte, le type les informations d’un compte d’utilisateur Azure AD vous souhaitez mise en service.
    2.  Sélectionnez le **mot de passe désactivée**.
    3.  Sélectionnez une **langue**.
    4.  Sélectionnez un **Type d’utilisateur**.
    5.  Cliquez sur **mettre à jour**.

    >[AZURE.IMPORTANT] **Mot de passe désactivé** doit être activée pour permettre à un utilisateur pour se connecter via un fournisseur d’identité.

6.  Accédez au **rôle**et puis effectuez les opérations suivantes :

    ![Rôles de partenaire] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Rôles de partenaire")

    1.  Dans la liste **globale** , sélectionnez **tous les\_autorisations**.
    2.  Cliquez sur **mettre à jour**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Sprinklr utilisateur compte outils de création ou API fournies par Sprinklr mise en service des comptes d’utilisateurs Azure AD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Pour affecter des utilisateurs à Sprinklr, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Sprinklr **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).