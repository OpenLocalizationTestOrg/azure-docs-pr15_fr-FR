<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Zendesk | Microsoft Azure" 
    description="Découvrez comment utiliser Zendesk avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Didacticiel : Intégration d’Azure Active Directory avec Zendesk
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Zendesk.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Zendesk
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Zendesk sera en mesure d’authentification unique dans l’application sur votre site de société Zendesk (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Zendesk
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scénario")

##<a name="enabling-the-application-integration-for-zendesk"></a>L’activation de l’intégration des applications pour Zendesk
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Zendesk.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Zendesk, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Zendesk**.

    ![Galerie d’applications] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Zendesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Zendesk avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Zendesk, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, dans la page de l’intégration **Zendesk** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "Authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Zendesk** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurer des URL de l’application")
  
    un. Dans la zone de texte **Zendesk URL de connexion** , tapez l’URL de votre selon le modèle suivant :`https://<tenant-name>.zendesk.com`

    b. Cliquez sur **suivant**.



4.  Dans la page **configuration de l’authentification unique en Zendesk** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre compiter.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Zendesk en tant qu’administrateur.

6.  Cliquez sur **administrateur**.

7.  Dans le volet de navigation gauche, cliquez sur **paramètres**, puis cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sécurité")

8.  Dans la page de **sécurité** , cliquez sur l’onglet **Admin et Agents** .

9.  Sélectionnez **authentification unique (SSO) et SAML**, puis sélectionnez **SAML**.

10. Dans le portail Azure AD, dans la page **configuration de l’authentification unique en Zendesk** , copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **URL de l’authentification unique SAML** .

11. Dans le portail Azure AD, dans la page **configuration de l’authentification unique en Zendesk** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion à distance** .

    ![Authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "Authentification unique")

12. Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat** .

    >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

13. Cliquez sur **Enregistrer**.

14. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à **Zendesk**, ils doivent être configurées dans **Zendesk**.  
Dans le cas **Zendesk**, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à Zendesk, effectuez les opérations suivantes :

1.  Connectez-vous à votre client **Zendesk** .

2.  Sélectionnez l’onglet **Liste de clients** .

3.  Sélectionnez l’onglet **utilisateur** , puis cliquez sur **Ajouter**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Ajouter un utilisateur")

4.  Tapez l’adresse de messagerie d’un compte Azure AD existant que vous souhaitez mettre en service, puis cliquez sur **Enregistrer**.

    ![Nouvel utilisateur] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nouvel utilisateur")

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Zendesk utilisateur compte outils de création ou API fournies par Zendesk aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zendesk, effectuez les opérations suivantes :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page de l’intégration **Zendesk **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
