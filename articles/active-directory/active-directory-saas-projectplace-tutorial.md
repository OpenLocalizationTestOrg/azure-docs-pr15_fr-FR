<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Projectplace | Microsoft Azure" 
    description="Découvrez comment utiliser Projectplace avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Didacticiel : Intégration d’Azure Active Directory avec Projectplace
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Projectplace.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Projectplace d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Projectplace sera en mesure d’authentification unique dans l’application sur votre site de société Projectplace (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Projectplace
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scénario")
##<a name="enabling-the-application-integration-for-projectplace"></a>L’activation de l’intégration des applications pour Projectplace
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Projectplace.

###<a name="to-enable-the-application-integration-for-projectplace-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Projectplace, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-projectplace-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-projectplace-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Projectplace**.

    ![Galerie d’applications] (./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Projectplace**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ProjectPlace] (./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Projectplace avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Projectplace** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Projectplace** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Projectplace d’adresse URL** , tapez l’URL de votre client ProjectPlace (par exemple : «*http://company.projectplace.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Projectplace** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurer la fonctionnalité d’authentification unique")

5.  Envoyer la metadatafile à l’équipe de support Projectplace.

    >[AZURE.NOTE] La configuration de l’authentification unique doit être effectuée par l’équipe de support Projectplace. Vous recevez une notification dès que la configuration est terminée.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Projectplace, ils doivent être configurées dans Projectplace.  
Dans le cas Projectplace, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Projectplace** en tant qu’administrateur.

2.  Accédez à **contacts**, puis cliquez sur **membres**.

    ![Personnes] (./media/active-directory-saas-projectplace-tutorial/IC790228.png "Personnes")

3.  Cliquez sur **Ajouter un membre**.

    ![Ajouter des membres] (./media/active-directory-saas-projectplace-tutorial/IC790232.png "Ajouter des membres")

4.  Dans la section **Ajouter un membre** , procédez comme suit :

    ![Nouveaux membres] (./media/active-directory-saas-projectplace-tutorial/IC790233.png "Nouveaux membres")

    1.  Dans la zone de texte de **Nouveaux membres** , tapez l’adresse de messagerie d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Envoyer**

        >[AZURE.NOTE] Un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active est envoyé au titulaire du compte Azure Active Directory.
    
>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Projectplace utilisateur compte outils de création ou API fournies par Projectplace aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-projectplace-perform-the-following-steps"></a>Pour affecter des utilisateurs à Projectplace, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Projectplace **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-projectplace-tutorial/IC790234.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-projectplace-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).