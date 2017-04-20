<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec FM : systèmes | Microsoft Azure" 
    description="Découvrez comment utiliser FM : systèmes avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Didacticiel : Intégration d’Azure Active Directory avec FM : systèmes
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et FM:Systems.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement FM:Systems d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à FM:Systems sera en mesure d’authentification unique dans l’application sur votre site de société FM:Systems (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour FM:Systems
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scénario")
##<a name="enabling-the-application-integration-for-fmsystems"></a>L’activation de l’intégration des applications pour FM:Systems
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour FM:Systems.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Pour activer l’intégration des applications pour FM:Systems, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **FM:Systems**.

    ![Galerie d’applications] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **FM:Systems**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![FM : systèmes] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM : systèmes")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à FM:Systems avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **FM:Systems** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à FM:Systems** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **Se FM:Systems d’adresse URL** , tapez votre FM:Systems **URL de réponse** (par exemple : *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Vous pouvez obtenir cette valeur à partir de votre FM : systèmes de l’équipe de support.

    2.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en FM:Systems** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez les métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurer l’authentification unique")

5.  Soumettre le fichier téléchargé métadonnées à votre FM : systèmes de l’équipe de support.

    >[AZURE.NOTE] Votre FM : Équipe de support technique de systèmes doit effectuer la configuration de l’authentification unique réelle.
Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à FM:Systems, ils doivent être configurées dans FM:Systems.  
Dans le cas FM:Systems, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans une fenêtre de navigateur web, connectez-vous à votre site d’entreprise FM:Systems en tant qu’administrateur.

2.  Accédez à **Administration système \> gérer la sécurité \> utilisateurs \> liste des utilisateurs**.

    ![Administration système] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administration système")

3.  Cliquez sur **créer un utilisateur**.

    ![Créer un nouvel utilisateur] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Créer un nouvel utilisateur")

4.  Dans la section **Créer un utilisateur** , procédez comme suit :

    ![Création d’utilisateur] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Création d’utilisateur")

    1.  Tapez le nom d’utilisateur, le mot de passe et sa confirmation, l’adresse de messagerie et l’ID d’employé d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **suivant**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres FM:Systems utilisateur compte outils de création ou API fournies par FM:Systems aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Pour affecter des utilisateurs à FM:Systems, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **FM:Systems **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).