<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec TeamSeer | Microsoft Azure" 
    description="Découvrez comment utiliser TeamSeer avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Didacticiel : Intégration d’Azure Active Directory avec TeamSeer
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et TeamSeer.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client TeamSeer
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à TeamSeer sera en mesure d’authentification unique dans l’application sur votre site de société TeamSeer (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour TeamSeer
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scénario")

##<a name="enabling-the-application-integration-for-teamseer"></a>L’activation de l’intégration des applications pour TeamSeer
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour TeamSeer.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Pour activer l’intégration des applications pour TeamSeer, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **TeamSeer**.

    ![Galerie d’applications] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **TeamSeer**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à TeamSeer avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **TeamSeer** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à TeamSeer** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **TeamSeer URL de connexion** , entrez votre URL à l’aide du modèle suivant «*http://www.teamseer.com/companyid*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en TeamSeer** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise TeamSeer en tant qu’administrateur.

6.  Accédez à **administrateur HR**.

    ![Administrateur de ressources humaines] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "Administrateur de ressources humaines")

7.  Cliquez sur **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Programme d’installation")

8.  Cliquez sur **définir les détails du fournisseur SAML**.

    ![Paramètres SAML] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "Paramètres SAML")

9.  Dans la section Détails du fournisseur SAML, effectuez les opérations suivantes :

    ![Paramètres SAML] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "Paramètres SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en TeamSeer** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL** .
    2.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Certificat Public IdP** .

10. Pour terminer la configuration du fournisseur SAML, effectuez les opérations suivantes :

    ![Paramètres SAML] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "Paramètres SAML")

    1.  Dans **Tester les adresses de messagerie**, tapez adresse de messagerie de l’utilisateur de test.
    2.  Dans la zone de texte de **l’émetteur** , tapez l’URL de l’émetteur du fournisseur de services.
    3.  Cliquez sur **Enregistrer**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à TeamSeer, ils doivent être configurées dans ShiftPlanning.  
Dans le cas TeamSeer, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TeamSeer** en tant qu’administrateur.

2.  Effectuez les opérations suivantes :

    ![Administrateur de ressources humaines] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "Administrateur de ressources humaines")

    1.  Accédez à **Administrateur RH \> utilisateurs**.
    2.  Cliquez sur **exécuter l’Assistant Nouvel utilisateur**.

3.  Dans la section **Détails de l’utilisateur** , procédez comme suit :

    ![Détails de l’utilisateur] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Détails de l’utilisateur")

    1.  Tapez le **prénom**, **nom**, **nom d’utilisateur (adresse de messagerie)** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **suivant**.

4.  Suivez les instructions à l’écran pour ajouter un nouvel utilisateur, puis cliquez sur **Terminer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres TeamSeer utilisateur compte outils de création ou API fournies par TeamSeer mise en service des comptes d’utilisateurs Azure AD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Pour affecter des utilisateurs à TeamSeer, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **TeamSeer **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).