<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Gigya | Microsoft Azure" 
    description="Découvrez comment utiliser Gigya avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Didacticiel : Intégration d’Azure Active Directory avec Gigya
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Gigya.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Une authentification unique Gigya activé abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Gigya sera en mesure d’authentification unique dans l’application sur votre site de société Gigya (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Gigya
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurer l’authentification unique")
##<a name="enabling-the-application-integration-for-gigya"></a>L’activation de l’intégration des applications pour Gigya
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Gigya.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Gigya, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Gigya**.

    ![Galerie d’applications] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Gigya**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Gigya avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Gigya** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Gigya** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Gigya d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*http://company.gigya.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Gigya** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Gigya en tant qu’administrateur.

6.  Accédez à **paramètres \> SAML Login**, puis cliquez sur le bouton **Ajouter** .

    ![Connexion SAML] (./media/active-directory-saas-gigya-tutorial/IC789532.png "Connexion SAML")

7.  Dans la section **SAML Login** , effectuez les opérations suivantes :

    ![Configuration SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuration SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Gigya** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Gigya** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL du Service unique authentification** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Gigya** , copiez la valeur **Nom identificateur Format** et puis collez-les dans la zone de texte **Nom ID Format** .
    5.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat X.509** .
    7.  Cliquez sur **Enregistrer les paramètres**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Gigya, ils doivent être configurées dans Gigya.  
Dans le cas Gigya, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Gigya** en tant qu’administrateur.

2.  Accédez à **administrateur \> gérer les utilisateurs**, puis cliquez sur **Inviter des utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Gérer les utilisateurs")

3.  Dans la boîte de dialogue inviter des utilisateurs, effectuez les opérations suivantes :

    ![Inviter des utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Inviter des utilisateurs")

    1.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **inviter un utilisateur**.
    
        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevront un message électronique contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Gigya utilisateur compte outils de création ou API fournies par Gigya aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Pour affecter des utilisateurs à Gigya, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Gigya **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).