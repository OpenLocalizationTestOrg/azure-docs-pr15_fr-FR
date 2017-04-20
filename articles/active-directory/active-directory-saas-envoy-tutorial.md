<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec envoi | Microsoft Azure" 
    description="Découvrez comment utiliser envoi avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Didacticiel : Intégration d’Azure Active Directory avec envoi
  
L’objectif de ce didacticiel consiste à afficher l’intégration d’Azure et d’envoi.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client d’envoi
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à envoi sera en mesure d’authentification unique dans l’application sur votre site de société envoi (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour l’envoi
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Scénario")
##<a name="enabling-the-application-integration-for-envoy"></a>L’activation de l’intégration des applications pour l’envoi
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour l’envoi.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Pour activer l’intégration des applications pour l’envoi, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **envoi**.

    ![Galerie d’applications] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **envoi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Envoi] (./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoi")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à l’envoi avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour l’envoi, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, Découvrez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **envoi** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à l’envoi** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776779.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Envoi URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Envoy.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à l’envoi** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\Envoy.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776781.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise envoi en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**.

    ![Envoi] (./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoi")

7.  Cliquez sur **société**.

    ![Société] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Société")

8.  Cliquez sur **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Dans la section configuration de **L’authentification SAML** , effectuez les opérations suivantes :

    ![Authentification SAML] (./media/active-directory-saas-envoy-tutorial/IC776785.png "Authentification SAML")

    >[AZURE.NOTE] La valeur pour le code d’emplacement siège social est automatiquement générée par l’application.

    1.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **empreinte** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique à l’envoi** , copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **URL SAML HTTP du fournisseur d’identité** .
    3.  Cliquez sur **Enregistrer les modifications**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776786.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à l’envoi de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à l’envoi à l’aide du panneau d’accès, envoi vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par envoi.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Pour attribuer des utilisateurs à l’envoi, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **envoi **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).