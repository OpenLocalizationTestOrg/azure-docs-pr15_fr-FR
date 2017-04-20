<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec BlueJeans | Microsoft Azure" 
    description="Découvrez comment utiliser BlueJeans avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Didacticiel : Azure AD intégration avec BlueJeans

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et BlueJeans.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement BlueJeans d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à BlueJeans sera en mesure d’authentification unique dans l’application sur votre site de société BlueJeans (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour BlueJeans
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scénario")
##<a name="enabling-the-application-integration-for-bluejeans"></a>L’activation de l’intégration des applications pour BlueJeans

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour BlueJeans.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Pour activer l’intégration des applications pour BlueJeans, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **BlueJeans**.

    ![Galerie d’applications] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **BlueJeans**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à BlueJeans avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **BlueJeans** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à BlueJeans** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se BlueJeans d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*https://company.BlueJeans.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en BlueJeans** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

6.  Accédez à **administrateur \> paramètres de groupe \> sécurité**.

    ![Administrateur] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administrateur")

7.  Dans la section **sécurité** , effectuez les opérations suivantes :

    ![Authentification unique SAML] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Authentification unique SAML")

    1.  Sélectionnez **SAML authentification unique**.
    2.  Sélectionnez **Activer la mise à disponibilité automatique**.

8.  Déplacer dans les étapes suivantes :

    ![Chemin d’accès de certificat] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Chemin d’accès de certificat")

    1.  Cliquez sur **Fichier**, puis téléchargez le certificat téléchargé.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en BlueJeans** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en BlueJeans** , copiez la valeur de **Modifier votre mot de passe l’URL** et collez-le dans la zone de texte **URL de changement de mot de passe** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en BlueJeans** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .

9.  Déplacer dans les étapes suivantes :

    ![Enregistrer les modifications] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Enregistrer les modifications")

    1.  Dans la zone de texte **nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Dans la zone de texte de **messagerie** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Cliquez sur **Enregistrer les modifications**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à BlueJeans, ils doivent être configurées dans BlueJeans.  
Dans le cas BlueJeans, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **BlueJeans** en tant qu’administrateur.

2.  Accédez à **administrateur \> gérer les utilisateurs \> Ajouter utilisateur**.

    ![Administrateur] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administrateur")

    >[AZURE.IMPORTANT] L’onglet **Ajouter un utilisateur** est uniquement disponible si, dans l' **onglet sécurité**, **Activer la mise en service automatique** est désactivée.

3.  Dans la section **Ajouter un utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Ajouter un utilisateur")

    1.  Tapez un **Nom d’utilisateur BlueJeans**, une **adresse de messagerie**, un **ID de la réunion BlueJeans**, un **Code secret modérateur**, un **Nom complet**, la **société** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres BlueJeans utilisateur compte outils de création ou API fournies par BlueJeans aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Pour affecter des utilisateurs à BlueJeans, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **BlueJeans **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
