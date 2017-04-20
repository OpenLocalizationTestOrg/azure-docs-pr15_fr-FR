<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Boomi | Microsoft Azure" 
    description="Découvrez comment utiliser Boomi avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Didacticiel : Intégration d’Azure Active Directory avec Boomi

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Boomi.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un Boomi de l’authentification unique activée abonnement

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Boomi sera en mesure d’authentification unique dans l’application sur votre site de société Boomi (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Boomi
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Scénario")
##<a name="enabling-the-application-integration-for-boomi"></a>L’activation de l’intégration des applications pour Boomi

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Boomi.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Boomi, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Boomi**.

    ![Galerie d’applications] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Boomi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Boomi avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Boomi** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Boomi** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **URL de réponse Boomi** , tapez votre **Boomi AtomSphere l’URL de connexion** (par exemple : «*https://platform.boomi.com/sso/AccountName/saml*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Boomi** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Boomi en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur votre nom de la société, puis **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Programme d’installation")

7.  Cliquez sur **Options de l’authentification unique**.

    ![Options de l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Options de l’authentification unique")

8.  Dans la section **Options d’authentification unique** , procédez comme suit :

    ![Options d’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Options d’authentification unique")

    1.  Sélectionnez **Activer SAML SSO**.
    2.  Cliquez sur **Importer**, pour télécharger le certificat téléchargé.
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Boomi** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .
    4.  En tant **Fédération Id emplacement**, sélectionnez **numéro de fédération est en cours d’élément NameID du sujet**.
    5.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Boomi, ils doivent être configurées dans Boomi.  
Dans le cas Boomi, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Boomi** en tant qu’administrateur.

2.  Accédez à **Gestion des utilisateurs \> utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Utilisateurs")

3.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Ajouter un utilisateur")

4.  Dans la page de la boîte de dialogue **Ajouter des rôles utilisateur** , effectuez les opérations suivantes :

    ![Ajouter un utilisateur] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Ajouter un utilisateur")

    1.  Tapez le **prénom**, le **Nom** et la **messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur OK.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Boomi utilisateur compte outils de création ou API fournies par Boomi aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Pour affecter des utilisateurs à Boomi, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Boomi **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
