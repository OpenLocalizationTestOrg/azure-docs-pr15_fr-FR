<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Clever | Microsoft Azure" 
    description="Découvrez comment utiliser Clever avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Didacticiel : Intégration d’Azure Active Directory avec Clever

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Clever. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client intelligent

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Clever sera en mesure d’authentification unique dans l’application sur votre site d’entreprise intelligente (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Clever
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-clever-tutorial/IC798977.png "Scénario")
##<a name="enabling-the-application-integration-for-clever"></a>L’activation de l’intégration des applications pour Clever

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Clever.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Clever, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-clever-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Clever**.

    ![Galerie d’applications] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Clever**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Intelligente] (./media/active-directory-saas-clever-tutorial/IC798979.png "Intelligente")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Clever avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Votre application intelligente attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs] (./media/active-directory-saas-clever-tutorial/IC798980.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Clever** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Clever** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **S’intelligents d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application intelligente (par exemple : *https://clever.com/in/azsandbox*), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Clever** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise intelligente en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **Login instantanée**.

    ![Connexion instantanée] (./media/active-directory-saas-clever-tutorial/IC798984.png "Connexion instantanée")

7.  Dans la page de **Connexion instantanée** , effectuez les opérations suivantes :

    ![Connexion instantanée] (./media/active-directory-saas-clever-tutorial/IC798985.png "Connexion instantanée")

    1.  Tapez l' **URL de connexion**.  

        >[AZURE.NOTE] L' **URL de connexion** est une valeur personnalisée.
Vous pouvez obtenir la valeur réelle à partir de votre équipe de support intelligente.

    2.  En tant que **Identité système**, sélectionnez **ADFS**.
    3.  Cliquez sur **Enregistrer**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurer l’authentification unique")

9.  Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-clever-tutorial/IC795920.png "Attributs")

10. Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    ![attributs jeton SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "attributs jeton SAML")

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|clever.Student.Credentials.district\_nom d’utilisateur|User.userPrincipalName|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    3.  Dans la zone de texte **Valeur de l’attribut** , sélectionnez la valeur de l’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

11. Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Clever, ils doivent être configurées dans Clever.  
Dans le cas Clever, mise en service est une tâche manuelle qui doit être effectuée par votre équipe de support intelligente.

>[AZURE.NOTE] Vous pouvez utiliser les autres outils de création de compte utilisateur intelligente ou API fournies par Clever aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Pour affecter des utilisateurs à Clever, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Clever **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-clever-tutorial/IC798987.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-clever-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
