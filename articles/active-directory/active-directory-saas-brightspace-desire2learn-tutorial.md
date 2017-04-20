<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Brightspace par Desire2Learn | Microsoft Azure" 
    description="Découvrez comment utiliser Brightspace par Desire2Learn avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Didacticiel : Intégration d’Azure Active Directory avec Brightspace par Desire2Learn

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Brightspace par Desire2Learn.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un Brightspace par Desire2Learn SSO activé abonnement

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Brightspace par Desire2Learn sera en mesure d’authentification unique dans l’application auprès de votre Brightspace en Desire2Learn page d’accueil (service fournisseur initialisé par l’ouverture de session), ou en utilisant l' [Introduction pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Brightspace par Desire2Learn
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scénario")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>L’activation de l’intégration des applications pour Brightspace par Desire2Learn

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Brightspace par Desire2Learn.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Brightspace par Desire2Learn, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Brightspace par Desire2Learn**.

    ![Galerie apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galerie apllication")

7.  Dans le volet résultats, sélectionnez **Brightspace par Desire2Learn**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Brightspace par Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace par Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Brightspace par Desire2Learn avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Brightspace par Desire2Learn** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Brightspace par Desire2Learn** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre **Brightspace par Desire2Learn** (par exemple : *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en Brightspace par Desire2Learn** , cliquez sur **télécharger les métadonnées**pour télécharger les métadonnées de votre et puis enregistrez les métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre Brightspace par l’équipe de support Desire2Learn.

    >[AZURE.NOTE] Votre Brightspace par l’équipe de support Desire2Learn doit effectuer la configuration de l’authentification unique réelle.
Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Brightspace par Desire2Learn, ils doivent être configurées dans Brightspace par Desire2Learn.  
Dans le cas de Brightspace par Desire2Learn, les comptes d’utilisateurs doivent être créés par votre Brightspace par l’équipe de support Desire2Learn.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre Brightspace par les outils de création de compte utilisateur Desire2Learn ou API fournies par Brightspace par Desire2Learn mise en service Azure Active Directory aux comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Pour affecter des utilisateurs à Brightspace par Desire2Learn, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Brightspace par Desire2Learn **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
