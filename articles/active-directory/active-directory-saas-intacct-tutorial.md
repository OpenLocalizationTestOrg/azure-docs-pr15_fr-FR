<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Intacct | Microsoft Azure" 
    description="Découvrez comment utiliser Intacct avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Didacticiel : Intégration d’Azure Active Directory avec Intacct
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Intacct.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Intacct
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Intacct sera en mesure d’authentification unique dans l’application sur votre site de société Intacct (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Intacct
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Scénario")
##<a name="enabling-the-application-integration-for-intacct"></a>L’activation de l’intégration des applications pour Intacct
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Intacct.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Intacct, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Intacct**.

    ![Galerie d’applications] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Intacct**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Intacct avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Intacct** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Intacct** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Intacct d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*https://Intacct.com/company*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Intacct** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Intacct en tant qu’administrateur.

6.  Cliquez sur l’onglet de la **société** , puis cliquez sur **Infos société**.

    ![Société] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Société")

7.  Cliquez sur l’onglet **sécurité** , puis cliquez sur **Modifier**.

    ![Sécurité] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Sécurité")

8.  Dans la section **authentification unique (SSO)** , procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Authentification unique")

    1.  Sélectionnez **Activer l’authentification unique**.
    2.  En tant que **type de fournisseur d’identité**, sélectionnez **SAML 2.0**.
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Intacct** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte **URL de l’émetteur** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Intacct** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    5.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte de **certificat**
    7.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Intacct, ils doivent être configurées dans Intacct.  
Dans le cas Intacct, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Intacct** .

2.  Cliquez sur l’onglet de la **société** , puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Utilisateurs")

3.  Cliquez sur l’onglet **Ajouter**

    ![Ajouter] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Ajouter")

4.  Dans la section **Informations utilisateur** , effectuez les opérations suivantes :

    ![Informations utilisateur] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Informations utilisateur")

    1.  Tapez l' **ID d’utilisateur**, le **nom**, **prénom**, l' **adresse de messagerie**, le **titre** et le **téléphone** d’un compte Azure AD que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Sélectionnez les **privilèges d’administrateur** d’un compte Azure AD que vous souhaitez mettre en service.
    3.  Cliquez sur **Enregistrer**.
        
        >[AZURE.NOTE] Le titulaire du compte DAS recevra un message électronique et suivre un lien pour confirmer leur propre compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Intacct utilisateur compte outils de création ou API fournies par Intacct aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Pour affecter des utilisateurs à Intacct, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Intacct **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).