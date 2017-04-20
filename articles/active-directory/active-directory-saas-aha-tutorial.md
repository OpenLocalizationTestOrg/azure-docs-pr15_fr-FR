<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant Aha ! | Microsoft Azure" 
    description="Découvrez comment utiliser Aha ! avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Didacticiel : Azure Active Directory intégrant Aha !

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Aha.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un sentiment ! authentification unique activée abonnement

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Aha ! seront en mesure d’authentification unique dans l’application auprès de votre Aha ! site de la société (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Aha !
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-aha-tutorial/IC798944.png "Scénario")
##<a name="enabling-the-application-integration-for-aha"></a>L’activation de l’intégration des applications pour Aha !

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Aha !.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Aha !, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-aha-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Aha !**.

    ![Galerie d’applications] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Aha !**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sentiment !] (./media/active-directory-saas-aha-tutorial/IC802746.png "Sentiment !")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section est hiérarchique comment permettre aux utilisateurs de s’authentifier sur Aha ! avec leur propre compte dans Azure Active Directory à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, sur le **Aha !** intégration de l’application de page, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurer l’authentification unique")

2.  Sur la **Comment souhaitez-vous aux utilisateurs de se connecter à Aha !** la page, sélectionnez **Microsoft Azure AD SSO**, puis sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans le **Aha ! Signe d’URL** zone de texte, tapez l’URL utilisées par vos utilisateurs pour vous connecter à votre Aha ! Application (par exemple : «*https://company.aha.io/session/new*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurer des URL de l’application")

4.  Sur la **configurer l’authentification unique en Aha !** page pour télécharger votre fichier de métadonnées, cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre Aha ! site d’entreprise en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-aha-tutorial/IC798950.png "Paramètres")

7.  Cliquez sur **compte**.

    ![Profil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Profil")

8.  Cliquez sur **sécurité et l’authentification unique**.

    ![Sécurité et l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798952.png "Sécurité et l’authentification unique")

9.  Dans la section **Authentification unique** , en tant que **Fournisseur d’identité**, sélectionnez **SAML2.0**.

    ![Sécurité et l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798953.png "Sécurité et l’authentification unique")

10. Dans la page de configuration **De l’authentification unique** , procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-aha-tutorial/IC798954.png "Authentification unique")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Pour **configurer à l’aide**, sélectionnez **Fichier de métadonnées**.
    3.  Pour télécharger votre fichier de métadonnées téléchargé, cliquez sur **Parcourir**.
    4.  Cliquez sur **mettre à jour**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Aha !, ils doivent être mis en service dans Aha !.  
Dans le cas d’Aha !, mise en service est une tâche automatique.  
Il n’existe aucun élément action à votre place.
  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous authentification unique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre Aha ! outils de création de comptes utilisateur ou API fournies par Aha ! mise en service des comptes d’utilisateurs AAD.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Pour attribuer aux utilisateurs Aha !, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Sur la **sentiment !** intégration de l’application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-aha-tutorial/IC798956.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-aha-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
