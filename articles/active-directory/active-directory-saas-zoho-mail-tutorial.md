<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec courrier Zoho | Microsoft Azure" 
    description="Apprenez à utiliser la messagerie Zoho avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="09/09/2016" 
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Didacticiel : Intégration d’Azure Active Directory avec Zoho courrier
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Zoho courrier.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client de messagerie Zoho
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à la messagerie Zoho seront en mesure d’authentification unique dans l’application sur votre site de société Zoho Mail (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour la messagerie Zoho
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scénario")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>L’activation de l’intégration des applications pour la messagerie Zoho
  
L’objectif de cette section est hiérarchique comment activer l’intégration de l’application pour la messagerie Zoho.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour la messagerie Zoho, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Zoho Mail**.

    ![Galerie d’applications] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Courrier Zoho**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Messagerie Zoho] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Messagerie Zoho")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à la messagerie Zoho avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Zoho courrier** , cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Zoho courrier** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurer des URL de l’application")

    un. Dans la zone de texte **Zoho Mail se d’URL** , tapez l’URL de votre selon le modèle suivant :`http://<company name>.ZohoMail.com`

    b. Cliquez sur **suivant**.


4.  Dans la page **configuration de l’authentification unique Zoho le courrier** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Zoho courrier en tant qu’administrateur.

6.  Accédez au **Panneau de configuration**.

    ![Le panneau de configuration] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Le panneau de configuration")

7.  Cliquez sur l’onglet **Authentification SAML** .

    ![Authentification SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Authentification SAML")

8.  Dans la section **Informations d’authentification SAML** , effectuez les opérations suivantes :

    ![Détails d’authentification SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Détails d’authentification SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique Zoho le courrier** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique Zoho le courrier** , copiez la valeur **URL déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique Zoho le courrier** , copiez la valeur de **l’URL de mot de passe de modification** et puis collez-les dans la zone de texte **URL de mot de passe de modification** .
    4.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et collez-le dans la zone de texte **clé publique** .
    6.  En tant **algorithme**, sélectionnez **RSA**.
    7.  Cliquez sur **OK**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD ouvrir une session dans votre courrier électronique Zoho, ils doivent être configurées dans votre courrier électronique Zoho.  
Dans le cas Zoho courrier, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **Zoho courrier** en tant qu’administrateur.

2.  Accédez à **panneau \> de messagerie et de documents**.

3.  Accédez à **Détails de l’utilisateur \> Ajouter utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Ajouter un utilisateur")

4.  Dans la boîte de dialogue **Ajouter des utilisateurs** , effectuez les opérations suivantes :

    ![Ajouter un utilisateur] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Ajouter un utilisateur")

    1.  Tapez le **prénom**, **Nom**, **ID de courrier électronique**, **votre mot de passe** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **OK**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevront un message électronique avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres courrier Zoho utilisateur compte outils de création ou API fournies par courrier postal Zoho aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Pour attribuer des utilisateurs à la messagerie Zoho, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **Zoho courrier **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).