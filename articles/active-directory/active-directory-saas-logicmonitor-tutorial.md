<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec LogicMonitor | Microsoft Azure" 
    description="Découvrez comment utiliser LogicMonitor avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Didacticiel : Intégration d’Azure Active Directory avec LogicMonitor
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et LogicMonitor.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client LogicMonitor
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour LogicMonitor
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scénario")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>L’activation de l’intégration des applications pour LogicMonitor
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour LogicMonitor.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Pour activer l’intégration des applications pour LogicMonitor, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **logicmonitor**.

    ![Galerie d’applications] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **LogicMonitor**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à LogicMonitor avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **LogicMonitor **application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à LogicMonitor** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à LogicMonitor \(e, g, : «*http://company.logicmonitor.com*»\), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en LogicMonitor** , cliquez sur **télécharger les métadonnées**et enregistrez-le sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurer l’authentification unique")

5.  Connectez-vous à votre site d’entreprise **LogicMonitor** en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Paramètres")

7.  Dans le bat navigation sur le côté gauche, cliquez sur **Authentification**

    ![Authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Authentification unique")

8.  Dans la section **paramètres d’authentification unique (SSO)** , procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Paramètres d’authentification unique")

    1.  Sélectionnez **Activer l’authentification unique**.
    2.  En tant **Attribution de rôle par défaut**, sélectionnez **en lecture seule**.
    3.  Ouvrez le fichier de métadonnées téléchargé dans le bloc-notes et collez le contenu du fichier dans la zone de texte **Métadonnées de fournisseur d’identité** .
    4.  Cliquez sur **Enregistrer les modifications**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour les utilisateurs AAD peut se connecter, ils doivent être configurées à l’application LogicMonitor à l’aide de leur nom d’utilisateur Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise LogicMonitor en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**, puis cliquez sur **utilisateurs et les rôles**.

    ![Rôles et les utilisateurs] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Rôles et les utilisateurs")

3.  Cliquez sur **Ajouter**.

4.  Dans la section **Ajouter un compte** , procédez comme suit :

    ![Ajouter un compte] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Ajouter un compte")

    1.  Tapez les valeurs de **nom d’utilisateur**, **l’E-mail**, le **mot de passe** et **Retapez le mot de passe** de l’utilisateur Azure Active Directory que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Sélectionnez les **rôles**, **autorisations d’affichage** et l' **état**.
    3.  Cliquez sur **Envoyer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres LogicMonitor utilisateur compte outils de création ou API fournies par LogicMonitor mise en service Azure Active Directory aux comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Pour affecter des utilisateurs à LogicMonitor, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **LogicMonitor** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




