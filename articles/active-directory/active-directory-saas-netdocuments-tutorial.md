<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec NetDocuments | Microsoft Azure" 
    description="Découvrez comment utiliser NetDocuments avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Didacticiel : Intégration d’Azure Active Directory avec NetDocuments
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et NetDocuments.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client NetDocuments
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à NetDocuments sera en mesure d’authentification unique dans l’application sur votre site de société NetDocuments (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour NetDocuments
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scénario")
##<a name="enabling-the-application-integration-for-netdocuments"></a>L’activation de l’intégration des applications pour NetDocuments
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour NetDocuments.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Pour activer l’intégration des applications pour NetDocuments, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **NetDocuments**.

    ![Galerie d’applications] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **NetDocuments**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à NetDocuments avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour NetDocuments, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **NetDocuments** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à NetDocuments** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurer des URL de l’application")

    1.  Dans la zone **URL de connexion** , entrez votre URL utilisée par vos utilisateurs pour se connecter à votre application NetDocuments (par exemple : «*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*»).
    2.  Dans la zone de texte **URL de réponse NetDocuments** , tapez la même valeur que vous avez tapé dans la la zone de texte **URL sur connexion** .  

        >[AZURE.NOTE]Vous pouvez trouver la valeur correcte à la fin de la boîte de dialogue **Identité fédérée** (voir la capture d’écran de l’étape 9).

    3.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en NetDocuments** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise NetDocuments en tant qu’administrateur.

6.  Accédez à **administrateur**.

7.  Cliquez sur **Ajouter et supprimer des utilisateurs et groupes**.

    ![Référentiel] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

8.  Cliquez sur **configurer les options de l’authentification avancées**.

    ![Configurer les options de l’authentification avancées] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurer les options de l’authentification avancées")

9.  Boîte de dialogue de **L’identité fédérée** , effectuez les opérations suivantes :

    ![Fédérés Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Fédérés Identitty")

    1.  En tant que **type de serveur d’identité fédérés**, sélectionnez **Active Directory Federation Services**.
    2.  Cliquez sur **Choisir un fichier**, pour télécharger le fichier de métadonnées téléchargé.
    3.  Cliquez sur **OK**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à NetDocuments, ils doivent être configurées dans NetDocuments.  
Dans le cas NetDocuments, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Ouverture de session une session sur le site de votre entreprise **NetDocuments** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Administrateur] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Administrateur")

3.  Cliquez sur **Ajouter et supprimer des utilisateurs et groupes**.

    ![Référentiel] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

4.  Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service, puis cliquez sur **Ajouter un utilisateur**.

    ![Adresse de messagerie] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Adresse de messagerie")

    >[AZURE.NOTE]Le titulaire du compte Azure Active Directory obtenez un message électronique contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres NetDocuments utilisateur compte outils de création ou API fournies par NetDocuments aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Pour affecter des utilisateurs à NetDocuments, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **NetDocuments **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).