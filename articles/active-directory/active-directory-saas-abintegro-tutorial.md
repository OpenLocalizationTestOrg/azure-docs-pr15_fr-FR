<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Abintegro | Microsoft Azure" 
    description="Découvrez comment utiliser Abintegro avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Didacticiel : Intégration d’Azure Active Directory avec Abintegro

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Abintegro.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Abintegro d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Abintegro sera en mesure d’authentification unique dans l’application sur votre site de société Abintegro (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Abintegro
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scénario")
##<a name="enabling-the-application-integration-for-abintegro"></a>L’activation de l’intégration des applications pour Abintegro

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Abintegro.

###<a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Abintegro, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-abintegro-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-abintegro-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **abintegro**.

    ![Galerie d’applications] (./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Abintegro**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Abintegro] (./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Abintegro avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Abintegro** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Abintegro** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Abintegro d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à Abintegro (par exemple : `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Abintegro** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurer la fonctionnalité d’authentification unique")

5.  Envoyer la metadatafile à l’équipe de support Abintegro.

    >[AZURE.NOTE] La configuration de l’authentification unique doit être effectuée par l’équipe de support Abintegro. Vous recevez une notification dès que la configuration est terminée.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Abintegro de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à Abintegro à l’aide du panneau d’accès, Abintegro vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Abintegro.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Pour affecter des utilisateurs à Abintegro, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Abintegro **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-abintegro-tutorial/IC790084.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-abintegro-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
