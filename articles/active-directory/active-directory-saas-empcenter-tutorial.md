<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec EmpCenter | Microsoft Azure" 
    description="Découvrez comment utiliser EmpCenter avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-empcenter"></a>Didacticiel : Intégration d’Azure Active Directory avec EmpCenter
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et EmpCenter.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un EmpCenter de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à EmpCenter sera en mesure d’authentification unique dans l’application sur votre site de société EmpCenter (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour EmpCenter
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-empcenter-tutorial/IC802916.png "Scénario")
##<a name="enabling-the-application-integration-for-empcenter"></a>L’activation de l’intégration des applications pour EmpCenter
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour EmpCenter.

###<a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>Pour activer l’intégration des applications pour EmpCenter, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-empcenter-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-empcenter-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-empcenter-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **EmpCenter**.

    ![Galerie d’applications] (./media/active-directory-saas-empcenter-tutorial/IC802917.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **EmpCenter**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![EmpCentral] (./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à EmpCenter avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **EmpCenter** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à EmpCenter** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-empcenter-tutorial/IC802921.png "Configurer les paramètres de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application EmpCenter (par exemple : *https://partner-authenticati.empcenter.com/workforce/SSO.do*).
    2.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en EmpCenter** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre équipe de support EmpCenter.

    >[AZURE.NOTE] L’équipe de support EmpCenter doit effectuer la configuration de l’authentification unique réelle.
Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à EmpCenter, ils doivent être configurées dans EmpCenter.  
Dans le cas d’EmpCenter, les comptes d’utilisateurs doivent être créés par l’équipe de support EmpCenter.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres EmpCenter utilisateur compte outils de création ou API fournies par EmpCenter mise en service Azure Active Directory aux comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>Pour affecter des utilisateurs à EmpCenter, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **EmpCenter **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-empcenter-tutorial/IC802924.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-empcenter-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).