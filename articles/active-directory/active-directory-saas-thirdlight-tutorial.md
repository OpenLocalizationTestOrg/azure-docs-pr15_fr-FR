<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Thirdlight | Microsoft Azure" 
    description="Découvrez comment utiliser Thirdlight avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Didacticiel : Azure Active Directory intégration avec Thirdlight
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Thirdlight.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Thirdlight d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Thirdlight sera en mesure d’authentification unique dans l’application sur votre site de société Thirdlight (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Thirdlight
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scénario")

##<a name="enabling-the-application-integration-for-thirdlight"></a>L’activation de l’intégration des applications pour Thirdlight
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Thirdlight.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Thirdlight, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Thirdlight**.

    ![Galerie d’applications] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Thirdlight**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Thirdlight avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Thirdlight, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Thirdlight** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Thirdlight** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Thirdlight URL de connexion** , tapez votre URL utilisée par vos utilisateurs pour se connecter à votre application Thirdlight (par exemple : «*http://azuresso2.thirdlight.com/*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Thirdlight** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Thirdlight en tant qu’administrateur.

6.  Accédez à **Configuration \> Administration système**, puis cliquez sur **SAML2**.

    ![Administration système] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administration système")

7.  Dans la section configuration SAML2, effectuez les opérations suivantes :

    ![SAML SSO] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML SSO")

    1.  Sélectionnez **Activer SAML2 SSO**.
    2.  En tant que **Source pour les métadonnées IdP**, sélectionnez **Charge IdP métadonnées à partir de XML**.
    3.  Ouvrir le fichier de métadonnées téléchargé, copiez le contenu et collez-le dans la zone de texte **IdP métadonnées XML** .
    4.  Cliquez sur **SAML2 enregistrer les paramètres**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Thirdlight, ils doivent être configurées dans Thirdlight.  
Dans le cas Thirdlight, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Thirdlight** en tant qu’administrateur.

2.  Accédez à l’onglet **utilisateurs** .

3.  Sélectionnez **utilisateurs et groupes**.

4.  Cliquez sur le bouton **Ajouter un nouvel utilisateur** .

5.  Mise en service, entrez **le nom d’utilisateur, nom ou Description, message électronique, choisissez un paramètre prédéfini ou de nouveaux membres du groupe** d’un compte DAS valide souhaité.

6.  Cliquez sur **créer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Thirdlight utilisateur compte outils de création ou API fournies par Thirdlight aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Pour affecter des utilisateurs à Thirdlight, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Thirdlight **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).