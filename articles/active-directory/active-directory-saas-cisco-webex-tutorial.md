<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Cisco Webex | Microsoft Azure" 
    description="Découvrez comment utiliser Cisco Webex avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Didacticiel : Azure Active Directory intégration avec Cisco Webex

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Cisco Webex.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Cisco Webex

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Cisco Webex seront en mesure d’authentification unique dans l’application sur votre site de société Cisco Webex (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Cisco Webex
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scénario")
##<a name="enabling-the-application-integration-for-cisco-webex"></a>L’activation de l’intégration des applications pour Cisco Webex

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Cisco Webex.

###<a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Cisco Webex, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Cisco Webex**.

    ![Galerie d’applications] (./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Cisco Webex**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Cisco Webex] (./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Cisco Webex avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Cisco Webex** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Cisco Webex** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **à l’aide d’URL** , tapez l’URL de votre client Cisco Webex (par exemple : *http://contoso.webex.com*).
    2.  Dans la zone de texte **URL de réponse Cisco Webex** , tapez votre **Cisco Webex AssertionConsumerService URL** (par exemple : *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Dans la page **configuration de l’authentification unique en Cisco Webex** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Cisco Webex en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **Administration du Site**.

    ![Administration du site] (./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administration du site")

7.  Dans la section **Gérer le Site** , cliquez sur **Configuration de l’authentification unique**.

    ![Configuration de l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuration de l’authentification unique")

8.  Dans la section Configuration de l’authentification unique Web fédéré, effectuez les opérations suivantes :

    ![Configuration de l’authentification unique de fédéré] (./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Configuration de l’authentification unique de fédéré")

    1.  Dans la liste **Protocole de fédération** , sélectionnez **SAML 2.0**.
    2.  Créer un fichier **codé en Base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé base 64 dans le bloc-notes, puis copiez le contenu de celle-ci.
    4.  Cliquez sur **Importer les métadonnées SAML**, puis collez votre certificat codé base 64.
    5.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Cisco Webex** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur de SAML (IdP ID)** .
    6.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Cisco Webex** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion de Service client l’authentification unique** .
    7.  Dans la liste **NameID Format** , sélectionnez **adresse de messagerie**.
    8.  Dans la zone de texte **AuthnContextClassRef** , tapez **Urn : oasis : noms : tc : SAML:2.0:ac:classes:Password**.
    9.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Cisco Webex** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion du Service client l’authentification unique** .
    10. Cliquez sur **mettre à jour**.

9.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Cisco Webex** , sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Cisco Webex, ils doivent être configurées dans Cisco Webex.  
Dans le cas de Cisco Webex, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Cisco Webex** .

2.  Accédez à **Gérer les utilisateurs \> Ajouter utilisateur**.

    ![Ajouter des utilisateurs] (./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Ajouter des utilisateurs")

3.  Dans la section Ajouter un utilisateur, procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Ajouter un utilisateur")

    1.  **Type de compte**, sélectionnez **hôte**.
    2.  Tapez les informations d’un utilisateur existant Azure AD dans les zones de texte suivante : **prénom, deuxième prénom**, **nom d’utilisateur**, **E-mail**, **mot de passe**, **Confirmer le mot de passe**.
    3.  Cliquez sur **Ajouter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Cisco Webex utilisateur compte outils de création ou API fournies par Cisco Webex aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Pour affecter des utilisateurs à Cisco Webex, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Cisco Webex **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
