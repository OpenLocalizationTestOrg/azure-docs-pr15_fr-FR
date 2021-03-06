<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec serre | Microsoft Azure" 
    description="Découvrez comment utiliser serre avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Didacticiel : Intégration d’Azure Active Directory avec serre
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et serre.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un serre seul authentification abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à serre sera en mesure d’authentification unique dans l’application sur votre site de société serre (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour serre
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scénario")
##<a name="enabling-the-application-integration-for-greenhouse"></a>L’activation de l’intégration des applications pour serre
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour serre.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Pour activer l’intégration des applications pour serre, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **serre**.

    ![Galerie d’applications] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **serre**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Serre] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Serre")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à serre avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **serre** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à serre** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se d’URL** , tapez l’URL de votre selon le modèle suivant «*https://company.greenhouse.io*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en serre** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à l’équipe de support technique serre.

    >[AZURE.NOTE] Authentification unique doit être activée par l’équipe de support serre.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à serre, ils doivent être configurées dans serre.  
Dans le cas de serre, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **serre** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **configurer**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utilisateurs")

3.  Cliquez sur **nouveaux utilisateurs**.

    ![Nouvel utilisateur] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Nouvel utilisateur")

4.  Dans la section **Ajouter un nouvel utilisateur** , procédez comme suit :

    ![Ajouter un nouvel utilisateur] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Ajouter un nouvel utilisateur")

    1.  Dans la zone de texte des **messages électroniques entrée utilisateur** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.
        
        >[AZURE.NOTE] Les propriétaires de comptes Azure Active Directory recevront un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres serre utilisateur compte outils de création ou API fournies par serre aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Pour affecter des utilisateurs à serre, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **serre **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).