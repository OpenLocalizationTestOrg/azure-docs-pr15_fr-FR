<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Bime | Microsoft Azure" 
    description="Découvrez comment utiliser Bime avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Didacticiel : Azure Active Directory intégration avec Bime

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Bime.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Bime

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Bime sera en mesure d’authentification unique dans l’application sur votre site de société Bime (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Bime
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bime-tutorial/IC775552.png "Scénario")
##<a name="enabling-the-application-integration-for-bime"></a>L’activation de l’intégration des applications pour Bime

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Bime.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Bime, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-bime-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Bime**.

    ![Galerie d’applications] (./media/active-directory-saas-bime-tutorial/IC775553.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Bime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Bime avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Bime, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Bime** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC771709.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Bime** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775555.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Bime URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Bimeapp.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bime-tutorial/IC775556.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Bime** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\Bime.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775557.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Bime en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **administrateur**, puis sur **compte**.

    ![Administrateur] (./media/active-directory-saas-bime-tutorial/IC775558.png "Administrateur")

7.  Dans la page configuration de compte, effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775559.png "Configurer l’authentification unique")

    1.  Sélectionnez **Activer SAML authentification**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Bime** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion à distance** .
    3.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    4.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775560.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Bime, ils doivent être configurées dans Bime.  
Dans le cas Bime, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Bime** .

2.  Dans la barre d’outils, cliquez sur **administrateur**, puis **utilisateurs**.

    ![Administrateur] (./media/active-directory-saas-bime-tutorial/IC775561.png "Administrateur")

3.  Dans la **Liste des utilisateurs**, cliquez sur **Ajouter un nouvel utilisateur** (« + »).

    ![Utilisateurs] (./media/active-directory-saas-bime-tutorial/IC775562.png "Utilisateurs")

4.  Dans la page de la boîte de dialogue **Détails de l’utilisateur** , procédez comme suit :

    ![Détails de l’utilisateur] (./media/active-directory-saas-bime-tutorial/IC775563.png "Détails de l’utilisateur")

    1.  Entrez le prénom, nom, connexion, messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur Enregistrer.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Bime utilisateur compte outils de création ou API fournies par Bime aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Pour affecter des utilisateurs à Bime, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Bime **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-bime-tutorial/IC775564.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-bime-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
