<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Picturepark | Microsoft Azure" 
    description="Découvrez comment utiliser Picturepark avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Didacticiel : Azure Active Directory intégration avec Picturepark
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Picturepark.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Picturepark
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Picturepark sera en mesure d’authentification unique dans l’application sur votre site de société Picturepark (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Picturepark
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scénario")

##<a name="enabling-the-application-integration-for-picturepark"></a>L’activation de l’intégration des applications pour Picturepark
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Picturepark.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Picturepark, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Picturepark**.

    ![Galerie d’applications] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Picturepark**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Picturepark avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Picturepark, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, Découvrez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)...

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Picturepark** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Picturepark** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Picturepark d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*http://company.picturepark.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Picturepark** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Picturepark en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Outils d’administration**, puis cliquez sur **Management Console**.

    ![Console de gestion] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console de gestion")

7.  Cliquez sur **authentification**, puis cliquez sur **fournisseurs d’identité**.

    ![Authentification] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentification")

8.  Dans la section **configuration de fournisseur d’identité** , effectuez les opérations suivantes :

    ![Configuration de fournisseur d’identité] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuration de fournisseur d’identité")

    1.  Cliquez sur **Ajouter**.
    2.  Tapez un nom pour votre configuration.
    3.  Sélectionnez **définir par défaut**.
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Picturepark** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis collez-les dans la zone de texte **URI émetteur** .
    5.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Approuvés émetteur pouce imprimer** .  

        >[AZURE.TIP]Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Cliquez sur **JoinDefaultUsersGroup**.
    7.  Pour définir l’attribut **Emailaddress** dans la zone de texte **réclamer** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Configuration] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
    8.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Picturepark, ils doivent être configurées dans Picturepark.  
Dans le cas Picturepark, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Picturepark** .

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Outils d’administration**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Utilisateurs")

3.  Sous l’onglet **vue d’ensemble des utilisateurs** , cliquez sur **Nouveau**.

    ![Gestion des utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gestion des utilisateurs")

4.  Dans la boîte de dialogue **Créer un utilisateur** , procédez comme suit :

    ![Création d’utilisateur] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Création d’utilisateur")

    1.  Type de la : **adresse de messagerie**, **votre mot de passe**, **Confirmer le mot de passe**, **prénom**, **nom**, **société**, **pays**, **code postal**, **Ville** d’un valide utilisateur Azure Active Directory que vous voulez disposition ot dans les zones de texte liée.
    2.  Sélectionnez une **langue**.
    3.  Cliquez sur **créer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Picturepark utilisateur compte outils de création ou API fournies par Picturepark aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Pour affecter des utilisateurs à Picturepark, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Picturepark **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).