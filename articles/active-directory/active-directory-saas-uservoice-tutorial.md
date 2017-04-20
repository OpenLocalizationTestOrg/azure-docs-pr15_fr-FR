<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec UserVoice | Microsoft Azure" 
    description="Découvrez comment utiliser UserVoice avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Didacticiel : Azure Active Directory intégration avec UserVoice
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et UserVoice.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client UserVoice
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à UserVoice sera en mesure d’authentification unique dans l’application sur votre site de société UserVoice (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour UserVoice
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scénario")

##<a name="enabling-the-application-integration-for-uservoice"></a>L’activation de l’intégration des applications pour UserVoice
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour UserVoice.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Pour activer l’intégration des applications pour UserVoice, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **UserVoice**.

    ![Galerie d’applications] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **UserVoice**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![UserVoice] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à UserVoice avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour UserVoice, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **UserVoice** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à UserVoice** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **UserVoice URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. UserVoice.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en UserVoice** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\UserVoice.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise UserVoice en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur paramètres et sélectionnez portail Web dans le menu.

    ![Paramètres] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Paramètres")

7.  Sous l’onglet **portail Web** , dans la section **authentification des utilisateurs** , cliquez sur **Modifier** pour ouvrir la page de la boîte de dialogue **Modifier l’authentification des utilisateurs**

    ![Portail Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portail Web")

8.  Dans la page de la boîte de dialogue **Modifier l’authentification des utilisateurs** , effectuez les opérations suivantes :

    ![Modifier l’authentification des utilisateurs] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Modifier l’authentification des utilisateurs")

    1.  Cliquez sur **Authentification unique (SSO)**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en UserVoice** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **Connexion distant l’authentification unique** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en UserVoice** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la **zone de texte Sign-Out distant l’authentification unique**.
    4.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **actuel empreinte SHA1 de certificat** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    5.  Cliquez sur **Enregistrer les paramètres d’authentification**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à UserVoice, ils doivent être configurées dans UserVoice.  
Dans le cas UserVoice, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **UserVoice** .

2.  Accédez à **paramètres**.

    ![Paramètres] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Paramètres")

3.  Cliquez sur **Général**.

4.  Cliquez sur **autorisations et Agents**.

    ![Agents et autorisations] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents et autorisations")

5.  Cliquez sur **Ajouter les administrateurs**.

    ![Ajouter les administrateurs] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Ajouter les administrateurs")

6.  Dans la boîte de dialogue **inviter les administrateurs** , procédez comme suit :

    ![Inviter les administrateurs] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Inviter les administrateurs")

    1.  Dans le TextBox de messages électroniques, tapez l’adresse de messagerie du compte que vous souhaitez mettre en service, puis cliquez sur **Ajouter**.
    2.  Cliquez sur **inviter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres UserVoice utilisateur compte outils de création ou API fournies par UserVoice aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Pour affecter des utilisateurs à UserVoice, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **UserVoice **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).