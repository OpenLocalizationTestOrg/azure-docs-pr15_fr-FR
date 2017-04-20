<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec un logiciel de vous organisez | Microsoft Azure" 
    description="Découvrez comment utiliser les logiciels fonctionner avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Didacticiel : Intégration d’Azure Active Directory avec vous organisez le logiciel
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et les logiciels de fonctionner.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client logiciel fonctionner
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour fonctionner logiciel
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scénario")
##<a name="enabling-the-application-integration-for-rally-software"></a>L’activation de l’intégration des applications pour fonctionner logiciel
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour fonctionner logiciel.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Pour activer l’intégration des applications pour fonctionner logiciel, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **vous organisez**.

    ![Galerie d’applications] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galerie d’applications")

7.  Dans le volet des résultats, sélectionner un **Logiciel de fonctionner**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Logiciel de fonctionner] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Logiciel de fonctionner")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier au logiciel de fonctionner avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat au logiciel de fonctionner.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration des **Logiciels fonctionner **application, cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à vous organisez** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD unique authentification] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD unique authentification")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Fonctionner logiciel client URL** , tapez votre URL à l’aide du modèle suivant «*https://\<nom de client\>. rally.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en vous organisez** , cliquez sur téléchargement métadonnées et enregistrez-le sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "De configurer l’authentification unique")

5.  Connectez-vous à votre client **Logiciel de fonctionner** .

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **le programme d’installation**, puis sélectionnez **abonnement**.

    ![Abonnement] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abonnement")

7.  Cliquez sur le bouton **d’Action** dans la barre d’outils dans la partie supérieure du côté droit, puis sélectionnez **Modifier un abonnement**.

8.  Dans la page de dialogue **abonnement** , effectuez les opérations suivantes, puis cliquez sur **Enregistrer et fermer**:

    ![Authentification] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentification")

    1.  Sélectionnez **l’authentification vous organisez ou l’authentification unique** de liste déroulante de l’authentification
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique au logiciel de fonctionner** , copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte **URL du fournisseur d’identité**
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique au logiciel de fonctionner** , copiez la valeur **URL déconnexion à distance** .

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour les utilisateurs AAD peut se connecter, ils doivent être configurées pour l’application du logiciel de fonctionner à l’aide de leur nom d’utilisateur Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client logiciel de fonctionner.

2.  Accédez à **le programme d’installation \> utilisateurs**, puis cliquez sur **+ Add New**.

    ![Utilisateurs] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utilisateurs")

3.  Tapez le nom dans la zone de texte nouvel utilisateur, puis cliquez sur **Ajouter des détails**.

4.  Dans la section **Créer un utilisateur** , procédez comme suit :

    ![Création d’utilisateur] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Création d’utilisateur")

    1.  Dans la zone de texte **Nom d’utilisateur** , tapez le nom de l’utilisateur Azure AD que vous souhaitez mettre en service.
    2.  Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de l’utilisateur Azure AD que vous souhaitez mettre en service.
    3.  Cliquez sur **Enregistrer et fermer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres logiciel de fonctionner utilisateur compte outils de création ou API fournies par le logiciel fonctionner aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Pour affecter des utilisateurs à fonctionner logiciel, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration des **Logiciels fonctionner** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




