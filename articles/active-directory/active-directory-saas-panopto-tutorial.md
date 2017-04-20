<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panopto | Microsoft Azure" 
    description="Découvrez comment utiliser Panopto avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Didacticiel : Intégration d’Azure Active Directory avec Panopto
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Panopto.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Panopto
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Panopto sera en mesure d’authentification unique dans l’application sur votre site de société Panopto (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Panopto
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Scénario")
##<a name="enabling-the-application-integration-for-panopto"></a>L’activation de l’intégration des applications pour Panopto
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Panopto.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Panopto, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Panopto**.

    ![Galerie Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Galerie Appkication")

7.  Dans le volet résultats, sélectionnez **Panopto**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Panopto avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Panopto** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777667.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Panopto** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777668.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Panopto URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Panopto.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Panopto** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777669.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Panopto en tant qu’administrateur.

6.  Dans la barre d’outils sur la gauche, cliquez sur **système**, puis cliquez sur **Fournisseurs d’identité**.

    ![Système] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Système")

7.  Cliquez sur **Ajouter un fournisseur**.

    ![Fournisseurs d’identité] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Fournisseurs d’identité")

8.  Dans la section de fournisseur SAML, effectuez les opérations suivantes :

    ![Configuration de SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuration de SaaS")

    1.  Dans la liste **Type de fournisseur** , sélectionnez **SAML20**
    2.  Dans la zone de texte **Nom de l’Instance** , tapez un nom pour l’instance.
    3.  Dans la zone de texte **Description claire** , tapez une description conviviale.
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Panopto** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    5.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Panopto** , copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **Url de la Page rebond** .
    6.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    7.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Clé publique**
    8.  Cliquez sur **Enregistrer**.
        ![Enregistrer] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Enregistrer")

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777674.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Panopto de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à Panopto à l’aide du panneau d’accès, Panopto vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Panopto.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Panopto utilisateur compte outils de création ou API fournies par Panopto mise en service des comptes d’utilisateurs Azure AD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Pour affecter des utilisateurs à Panopto, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Panopto **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).