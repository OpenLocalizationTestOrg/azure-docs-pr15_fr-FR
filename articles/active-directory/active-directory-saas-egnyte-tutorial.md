<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Egnyte | Microsoft Azure" 
    description="Découvrez comment utiliser Egnyte avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Didacticiel : Intégration d’Azure Active Directory avec Egnyte
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Egnyte.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Egnyte d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Egnyte sera en mesure d’authentification unique dans l’application sur votre site de société Egnyte (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md)
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Egnyte
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scénario")
##<a name="enabling-the-application-integration-for-egnyte"></a>L’activation de l’intégration des applications pour Egnyte
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Egnyte.

###<a name="to-enable-the-application-integration-for-egnyte-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Egnyte, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-egnyte-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-egnyte-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **egnyte**.

    ![Galerie d’applications] (./media/active-directory-saas-egnyte-tutorial/IC787813.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Egnyte**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Egnyte] (./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Egnyte avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Egnyte** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Egnyte** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Egnyte URL de connexion** , entrez votre URL à l’aide du modèle suivant «*https://company.egnyte.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Egnyte** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Egnyte en tant qu’administrateur.

6.  Cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-egnyte-tutorial/IC787819.png "Paramètres")

7.  Dans le menu, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-egnyte-tutorial/IC787820.png "Paramètres")

8.  Cliquez sur l’onglet **Configuration** , puis cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-egnyte-tutorial/IC787821.png "Sécurité")

9.  Dans la section **Authentification d’authentification unique** , procédez comme suit :

    ![Authentification unique d’authentification] (./media/active-directory-saas-egnyte-tutorial/IC787822.png "Authentification unique d’authentification")

    1.  En tant **authentification d’authentification unique**, sélectionnez **SAML 2.0**.
    2.  En tant que **fournisseur d’identité**, sélectionnez **AzureAD**.
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Egnyte** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Egnyte** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité identité fournisseur** .
    5.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et collez-le dans la zone de texte **certificat fournisseur d’identité** .
    7.  En tant que **mappage utilisateur par défaut**, sélectionnez **adresse de messagerie**.
    8.  En tant que d' **utiliser la valeur émetteur spécifique à un domaine**, sélectionnez **désactivé**.
    9.  Cliquez sur **Enregistrer**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Egnyte, ils doivent être configurées dans Egnyte.  
Dans le cas Egnyte, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Egnyte **Egnyte** en tant qu’administrateur.

2.  Accédez à **paramètres \> utilisateurs et groupes**.

3.  Cliquez sur **Ajouter un nouvel utilisateur**, puis sélectionnez le type d’utilisateur que vous voulez ajouter.

    ![Utilisateurs] (./media/active-directory-saas-egnyte-tutorial/IC787824.png "Utilisateurs")

4.  Dans la section **Nouvel utilisateur Standard** , procédez comme suit :

    ![Nouvel utilisateur Standard] (./media/active-directory-saas-egnyte-tutorial/IC787825.png "Nouvel utilisateur Standard")

    1.  Tapez la **messagerie**, **nom d’utilisateur** et d’autres détails d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevront un message électronique de notification.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Egnyte utilisateur compte outils de création ou API fournies par Egnyte aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-egnyte-perform-the-following-steps"></a>Pour affecter des utilisateurs à Egnyte, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Egnyte **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-egnyte-tutorial/IC787826.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-egnyte-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).