<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec IdeaScale | Microsoft Azure" 
    description="Découvrez comment utiliser IdeaScale avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Didacticiel : Intégration d’Azure Active Directory avec IdeaScale
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et IdeaScale.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement IdeaScale d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à IdeaScale sera en mesure d’authentification unique dans l’application à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour IdeaScale
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scénario")
##<a name="enabling-the-application-integration-for-ideascale"></a>L’activation de l’intégration des applications pour IdeaScale
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour IdeaScale.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Pour activer l’intégration des applications pour IdeaScale, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **IdeaScale**.

    ![Galerie d’applications] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **IdeaScale**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à IdeaScale avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour IdeaScale, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **IdeaScale** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à IdeaScale** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se IdeaScale d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application IdeaScale (par exemple : «*https://company.IdeaScale.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en IdeaScale** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise IdeaScale en tant qu’administrateur.

6.  Accédez à **paramètres de la Communauté**.

    ![Paramètres de la Communauté] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Paramètres de la Communauté")

7.  Accédez à **sécurité \> unique les paramètres de connexion**.

    ![Paramètres de la fonctionnalité d’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Paramètres de la fonctionnalité d’authentification unique")

8.  En tant que **Type SSO**, sélectionnez **SAML 2.0**.

    ![Type d’entrée unique] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Type d’entrée unique")

9.  Dans la boîte de dialogue **Paramètres de connexion unique** , procédez comme suit :

    ![Paramètres de la fonctionnalité d’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Paramètres de la fonctionnalité d’authentification unique")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en IdeaScale** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité IdP SAML** .
    2.  Copier le contenu de votre fichier de métadonnées téléchargé et collez-le dans la zone de texte **SAML IdP métadonnées** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en IdeaScale** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de succès de déconnexion** .
    4.  Cliquez sur **Enregistrer les modifications**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à IdeaScale, ils doivent être configurées dans IdeaScale.  
Dans le cas IdeaScale, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **IdeaScale** en tant qu’administrateur.

2.  Accédez à **paramètres de la Communauté**.

    ![Paramètres de la Communauté] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Paramètres de la Communauté")

3.  Accédez à **paramètres de base \> la gestion des membres**.

4.  Cliquez sur **Ajouter un membre**.

    ![Gestion des membres] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gestion des membres")

5.  Dans la section Ajouter un nouveau membre, procédez comme suit :

    ![Ajouter un nouveau membre] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Ajouter un nouveau membre")

    1.  Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer les modifications**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un message électronique avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres IdeaScale utilisateur compte outils de création ou API fournies par IdeaScale aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Pour affecter des utilisateurs à IdeaScale, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **IdeaScale **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).