<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SimpleNexus | Microsoft Azure" 
    description="Découvrez comment utiliser SimpleNexus avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Didacticiel : Intégration d’Azure Active Directory avec SimpleNexus
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et SimpleNexus.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un SimpleNexus de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à SimpleNexus sera en mesure d’authentification unique dans l’application sur votre site de société SimpleNexus (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour SimpleNexus
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scénario")
##<a name="enabling-the-application-integration-for-simplenexus"></a>L’activation de l’intégration des applications pour SimpleNexus
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour SimpleNexus.

###<a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Pour activer l’intégration des applications pour SimpleNexus, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **nexus simple**.

    ![Galerie d’applications] (./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **SimpleNexus**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Nexus simple] (./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Nexus simple")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à SimpleNexus avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **SimpleNexus** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SimpleNexus** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **SimpleNexus URL de connexion** , tapez votre URL à l’aide du modèle suivant «*https://simplenexus.com/CompanyName\_connexion*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en SimpleNexus** , cliquez sur **télécharger les métadonnées**et puis transférer le fichier de métadonnées à l’équipe de support SimpleNexus.

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurer l’authentification unique")

    >[AZURE.NOTE] Authentification unique doit être activée par l’équipe de support SimpleNexus.

5.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à SimpleNexus, ils doivent être configurées dans SimpleNexus.  
Dans le cas SimpleNexus, mise en service est une tâche manuelle effectuée par l’administrateur client.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SimpleNexus utilisateur compte outils de création ou API fournies par SimpleNexus aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Pour affecter des utilisateurs à SimpleNexus, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **SimpleNexus **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).