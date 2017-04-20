<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec plus d’informations sur Microsoft | Microsoft Azure" 
    description="Découvrez comment utiliser des itinéraires sur Microsoft avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Didacticiel : Intégration d’Azure Active Directory avec plus d’informations sur Microsoft

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure Active Directory et des itinéraires sur Microsoft.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un itinéraire lors de l’abonnement Microsoft

Si vous n’avez pas un itinéraire fédéré lors de l’abonnement Microsoft encore, une demande à envoyer par courrier électronique «*service@DirectionsOnMicrosoft.com*».

Au terme de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affecté à plus d’informations sur Microsoft seront en mesure d’authentification unique dans l’application à l’aide de l’authentification unique.

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour plus d’informations sur Microsoft
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scénario")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>L’activation de l’intégration des applications pour plus d’informations sur Microsoft

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour plus d’informations sur Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Pour activer l’intégration des applications pour plus d’informations sur Microsoft, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **plus d’informations sur Microsoft**.

    ![Galerie d’applications] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **plus d’informations sur Microsoft**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Scénario] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scénario")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à plus d’informations sur Microsoft avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **plus d’informations sur Microsoft** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à plus d’informations sur Microsoft** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Singel authentification] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel authentification")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte URL de connexion, tapez **https://www.directionsonmicrosoft.com/user/login**, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à plus d’informations sur Microsoft** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées pour les instructions à l’équipe de support technique de Microsoft (*service@DirectionsOnMicrosoft.com*). Pour activer les instructions à l’équipe de support technique Microsoft localiser votre adhésion site fédéré, inclure les informations de votre société dans votre message électronique.

    >[AZURE.NOTE] Authentification unique pour obtenir des instructions sur Microsoft doit être activé par les instructions à l’équipe de support technique Microsoft.
Vous recevrez une notification lors de l’authentification unique a été activé.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Il n’existe aucun élément action pour vous permettent de configurer approvisionnement pour plus d’informations sur Microsoft de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à plus d’informations sur Microsoft à l’aide du panneau d’accès, plus d’informations sur Microsoft vérifie l’existence de l’utilisateur. Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par plus d’informations sur Microsoft.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Pour attribuer aux utilisateurs pour plus d’informations sur Microsoft, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **plus d’informations sur Microsoft **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Oui")
