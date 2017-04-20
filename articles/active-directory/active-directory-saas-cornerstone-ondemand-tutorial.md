<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec un jalon à la demande | Microsoft Azure" 
    description="Découvrez comment utiliser à la demande un jalon avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Didacticiel : Intégration d’Azure Active Directory avec un jalon à la demande

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et un jalon à la demande.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client à la demande un jalon

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à un jalon à la demande seront en mesure d’authentification unique dans l’application au site de votre société à la demande un jalon (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour un jalon à la demande
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scénario")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>L’activation de l’intégration des applications pour un jalon à la demande

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour un jalon à la demande.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Pour activer l’intégration des applications pour un jalon à la demande, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **un jalon à la demande**.

    ![Galerie d’applications] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Un jalon à la demande**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![À la demande un jalon] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "À la demande un jalon")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à un jalon à la demande avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **À la demande un jalon** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à un jalon à la demande** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD unique authentification] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD unique authentification")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Un jalon à la demande URL de connexion** , entrez votre URL à l’aide du modèle suivant «*http://company.csod.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à un jalon à la demande** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurer l’authentification unique")

5.  Envoyer que les éléments suivants à la demande d’un jalon équipe de support technique :

    1.  Le certificat téléchargé
    2.  La valeur de **l’URL de connexion à distance**
    3.  La valeur **d’URL de déconnexion à distance** .

    >[AZURE.NOTE] Authentification unique doit être configurée par l’équipe de support à la demande un jalon.
Vous obtenez une notification à partir de l’équipe de support lorsque la configuration est terminée.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à un jalon à la demande, ils doivent être configurées dans un jalon à la demande.  
Dans le cas d’un jalon à la demande, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Envoyez les informations (par exemple : nom, Emial) sur l’utilisateur Azure AD que vous souhaitez mettre en service à la demande d’un jalon l’équipe de support.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres un jalon à la demande utilisateur compte outils de création ou API fournies par un jalon à la demande aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Pour affecter des utilisateurs à un jalon à la demande, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page intégration d’application **À la demande un jalon **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Attribuer aux utilisateurs")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
