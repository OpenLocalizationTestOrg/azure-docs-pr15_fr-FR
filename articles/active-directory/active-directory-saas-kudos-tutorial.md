<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec chapeau | Microsoft Azure" 
    description="Découvrez comment utiliser chapeau avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Didacticiel : Intégration d’Azure Active Directory avec chapeau
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et chapeau.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client chapeau
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à chapeau sera en mesure d’authentification unique dans l’application sur votre site de société chapeau (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour chapeau
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Scénario")
##<a name="enabling-the-application-integration-for-kudos"></a>L’activation de l’intégration des applications pour chapeau
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour chapeau.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Pour activer l’intégration des applications pour chapeau, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **chapeau**.

    ![Galerie d’applications] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **chapeau**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Félicitations] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Félicitations")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à chapeau avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **chapeau** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787802.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à chapeau** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787803.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se chapeau d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*https://company.kudosnow.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Félicitations** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787805.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise chapeau en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

7.  Cliquez sur **intégrations \> SSO**.

8.  Dans la section **authentification unique** , procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787807.png "Authentification unique")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en chapeau** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **se sur l’URL** .
    2.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP]
        Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **certificat X.509**
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en chapeau** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL de déconnexion** .
    5.  Dans la zone de texte **URL chapeau votre** , tapez le nom de votre société.
    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787808.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à chapeau, ils doivent être configurées dans chapeau.  
Dans le cas de chapeau, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **chapeau** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

3.  Cliquez sur **administrateur de l’utilisateur**.

4.  Cliquez sur l’onglet **utilisateurs** , puis cliquez sur **Ajouter un utilisateur**.

    ![Administrateur de l’utilisateur] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Administrateur de l’utilisateur")

5.  Dans la section **Ajouter un utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Ajouter un utilisateur")

    1.  Tapez le **prénom**, **Nom**, **courrier électronique** et autres détails d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **créer un utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres chapeau utilisateur compte outils de création ou API fournies par chapeau aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Pour affecter des utilisateurs à chapeau, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **Félicitations **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).