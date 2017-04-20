<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Wikispaces | Microsoft Azure" 
    description="Découvrez comment utiliser Wikispaces avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Didacticiel : Intégration d’Azure Active Directory avec Wikispaces
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Wikispaces.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Wikispaces d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Wikispaces sera en mesure d’authentification unique dans l’application sur votre site de société Wikispaces (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Wikispaces
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>L’activation de l’intégration des applications pour Wikispaces
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Wikispaces.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Wikispaces, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Wikispaces**.

    ![Galerie d’applications] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Wikispaces**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Wikispaces avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Wikispaces** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Wikispaces** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Wikispaces d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*http://company.wikispaces.net*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Wikispaces** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurer l’authentification unique")

5.  Envoyer la metadatafile à l’équipe de support Wikispaces.

    >[AZURE.NOTE] La configuration de l’authentification unique doit être effectuée par l’équipe de support Wikispaces. Vous recevez une notification dès que la configuration est terminée.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Wikispaces, ils doivent être configurées dans Wikispaces.  
Dans le cas Wikispaces, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Wikispaces** en tant qu’administrateur.

2.  Accédez aux **membres**.

    ![Membres] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membres")

3.  Cliquez sur **Inviter des personnes**.

    ![Inviter des personnes] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Inviter des personnes")

4.  Dans la section **Inviter des personnes** , effectuez les opérations suivantes :

    ![Inviter des personnes] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Inviter des personnes")

    1.  Tapez les **noms d’utilisateur ou adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Envoyer**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory permet de recevoir un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Wikispaces utilisateur compte outils de création ou API fournies par Wikispaces aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Pour affecter des utilisateurs à Wikispaces, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Wikispaces **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).