<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Suite Adaptive | Microsoft Azure"
    description="Découvrez comment utiliser Adaptive Suite avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Didacticiel : Intégration d’Azure Active Directory avec Adaptive Suite

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et gamme Adaptive.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Adaptive Suite

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à la Suite Adaptive sera en mesure d’authentification unique dans l’application sur votre site de société Suite Adaptive (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications gamme Adaptive
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scénario")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>L’activation de l’intégration des applications gamme Adaptive

L’objectif de cette section est hiérarchique comment activer l’intégration de l’application gamme Adaptive.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Pour activer l’intégration des applications gamme Adaptive, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Suite Adaptive**.

    ![Galerie d’applications] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Adaptive Suite**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Suite Adaptive] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Suite Adaptive")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Adaptive Suite avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique gamme Adaptive, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Gamme Adaptive** application, cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Adaptive Suite** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , dans la zone de texte **URL de réponse** , tapez votre URL à l’aide du modèle suivant «*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE =*», puis cliquez sur **suivant**.

    >[AZURE.NOTE] Vous pouvez obtenir cette valeur à partir de la page des **Paramètres de l’authentification unique SAML** la Suite Adaptive.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique en Adaptive Suite** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Suite Adaptive en tant qu’administrateur.

6.  Accédez à **administrateur**.

    ![Administrateur] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrateur")

7.  Dans la section **utilisateurs et les rôles** , cliquez sur **Gérer les paramètres de l’authentification unique SAML**.

    ![Gérer les paramètres de l’authentification unique SAML] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gérer les paramètres de l’authentification unique SAML")

8.  Dans la page **Paramètres de l’authentification unique SAML** , effectuez les opérations suivantes :

    ![Paramètres de l’authentification unique SAML] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Paramètres de l’authentification unique SAML")

    1.  Dans la zone de texte **nom du fournisseur identité** , tapez un nom pour votre configuration.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Suite Adaptive** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité fournisseur d’identité** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en gamme Adaptive** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis collez-les dans la zone de texte **URL de l’authentification unique fournisseur d’identité** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Suite Adaptive** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis collez-les dans la zone de texte **URL déconnexion personnalisé** .
    5.  Pour télécharger votre certificat téléchargé, cliquez sur **Choisir un fichier**.
    6.  En tant qu' **identifiant utilisateur SAML**, sélectionnez **Adaptive Insights nom d’utilisateur**.
    7.  En tant qu' **emplacement de l’id utilisateur SAML**, sélectionnez **id d’utilisateur dans NameID d’objet**.
    8.  En tant que **format SAML NameID**, sélectionnez **adresse de messagerie**.
    9.  En tant que **SAML activer**, sélectionnez **Autoriser l’authentification unique SAML et direct Insights Adaptive login**.
    10. Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Adaptive Suite, ils doivent être configurées dans la Suite Adaptive.  
Dans le cas d’Adaptive Suite, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **Suite Adaptive** en tant qu’administrateur.

2.  Accédez à **administrateur**.

    ![Administrateur] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrateur")

3.  Dans la section **utilisateurs et les rôles** , cliquez sur **Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Ajouter un utilisateur")

4.  Dans la section **Nouvel utilisateur** , procédez comme suit :

    ![Soumettre] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Soumettre")

    1.  Tapez le **nom**, **connexion**, **messagerie**et **mot de passe** d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Sélectionnez un **rôle**.
    3.  Cliquez sur **Envoyer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Suite Adaptive utilisateur compte outils de création ou API fournies par Adaptive Suite aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Pour attribuer aux utilisateurs dans la Suite Adaptive, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Suite Adaptive **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
