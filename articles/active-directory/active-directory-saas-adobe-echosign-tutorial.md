<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Adobe EchoSign | Microsoft Azure" 
    description="Découvrez comment utiliser Adobe EchoSign avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Didacticiel : Intégration d’Azure Active Directory avec Adobe EchoSign

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Adobe EchoSign.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un EchoSign Adobe unique se lors de l’abonnement activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Adobe EchoSign sera en mesure d’authentification unique dans l’application sur votre site de société Adobe EchoSign (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Adobe EchoSign
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scénario")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>L’activation de l’intégration des applications pour Adobe EchoSign

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Adobe EchoSign.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Adobe EchoSign, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Adobe EchoSign**.

    ![Galerie d’applications] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Adobe EchoSign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Adobe EchoSign avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Adobe EchoSign** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Adobe EchoSign** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Adobe EchoSign se d’URL** , tapez l’URL de votre selon le modèle suivant «*https://company.echosign.com/*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Adobe EchoSign** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Adobe EchoSign en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **le compte**et puis, dans le volet de navigation sur le dé gauche, cliquez sur **Paramètres de SAML** sous **Paramètres du compte**.

    ![Compte] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Compte")

7.  Dans la section Paramètres SAML, effectuez les opérations suivantes :

    ![Paramètres SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Paramètres SAML")

    1.  En tant que **Mode SAML**, sélectionnez **SAML obligatoire**.
    2.  Sélectionnez **Autoriser les administrateurs EchoSign compte pour vous connecter à l’aide de leurs informations d’identification EchoSign**.
    3.  **Création d’un utilisateur**, sélectionnez **Ajouter automatiquement les utilisateurs authentifiés par le biais SAML**.

8.  Déplacer, effectuez les opérations suivantes :

    ![Paramètres SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Paramètres SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Adobe EchoSign** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité IdP** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Adobe EchoSign** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion IdP** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Adobe EchoSign** , copiez la valeur **URL déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion IdP** .
    4.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **IdP certificat** 6.  Cliquez sur **Enregistrer les modifications**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Adobe EchoSign, ils doivent être configurées dans EchoSign Adobe.  
Dans le cas d’Adobe EchoSign, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **Adobe EchoSign** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **compte**et puis, dans le volet de navigation sur le dé gauche, cliquez sur **utilisateurs et groupes**, puis cliquez sur **créer un nouvel utilisateur**.

    ![Compte] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Compte")

3.  Dans la section **Créer un nouvel utilisateur** , procédez comme suit :

    ![Création d’utilisateur] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Création d’utilisateur")

    1.  Tapez **l’Adresse de messagerie**, le **prénom** et le **Nom** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **créer un utilisateur**.

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevront un message électronique contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Adobe EchoSign utilisateur compte outils de création ou API fournies par Adobe EchoSign aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Pour affecter des utilisateurs à Adobe EchoSign, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **Adobe EchoSign **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
