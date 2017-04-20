<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SmarterU | Microsoft Azure" 
    description="Découvrez comment utiliser SmarterU avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Didacticiel : Azure Active Directory intégration avec SmarterU
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et SmarterU.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client SmarterU
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à SmarterU sera en mesure d’authentification unique dans l’application sur votre site de société SmarterU (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour SmarterU
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scénario")

##<a name="enabling-the-application-integration-for-smarteru"></a>L’activation de l’intégration des applications pour SmarterU
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour SmarterU.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Pour activer l’intégration des applications pour SmarterU, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **SmarterU**.

    ![Fallery d’application] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery d’application")

7.  Dans le volet résultats, sélectionnez **SmarterU**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à SmarterU avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **SmarterU** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SmarterU** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurer l’authentification unique")

3.  Sur **configuration de l’authentification unique en SmarterU** page, afin de télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**, et puis les fichier de données localement en tant que **c:\\SmarterUMetaData.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise SmarterU en tant qu’administrateur.

5.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Paramètres du compte**.

    ![Paramètres du compte] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Paramètres du compte")

6.  Dans la page configuration de compte, effectuez les opérations suivantes :

    ![Autorisation externes] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorisation externes")

    1.  Sélectionnez **Activer l’autorisation d’externes**.
    2.  Dans la section **Contrôle Login masque** , sélectionnez l’onglet **SmarterU** .
    3.  Dans la section **Connexion par défaut de l’utilisateur** , sélectionnez l’onglet **SmarterU** .
    4.  Sélectionnez **Activer Okta**.
    5.  Copiez le contenu du fichier téléchargé métadonnées et collez-le dans la zone de texte **Okta métadonnées** .
    6.  Cliquez sur **Enregistrer**.

7.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à SmarterU, ils doivent être configurées dans SmarterU.  
Dans le cas SmarterU, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **SmarterU** .

2.  Accédez à **utilisateurs**.

3.  Dans la section utilisateur, effectuez les opérations suivantes :

    ![Nouvel utilisateur] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nouvel utilisateur")

    1.  Cliquez sur **+ utilisateur**.
    2.  Tapez les valeurs associées attribut du compte d’utilisateur Azure AD dans les zones de texte suivante : **de messagerie principal**, **Réf employé**, **mot de passe**, **Vérifiez votre mot de passe**, **remplacé par nom**, **prénom**.
    3.  Cliquez sur **actif**.
    4.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SmarterU utilisateur compte outils de création ou API fournies par SmarterU aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Pour affecter des utilisateurs à SmarterU, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **SmarterU **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).