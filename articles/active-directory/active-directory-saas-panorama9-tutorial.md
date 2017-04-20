<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panorama9 | Microsoft Azure" 
    description="Découvrez comment utiliser Panorama9 avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Didacticiel : Intégration d’Azure Active Directory avec Panorama9
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Panorama9.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Panorama9 d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Panorama9 sera en mesure d’authentification unique dans l’application sur votre site de société Panorama9 (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Panorama9
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scénario")
##<a name="enabling-the-application-integration-for-panorama9"></a>L’activation de l’intégration des applications pour Panorama9
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Panorama9.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Panorama9, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Panorama9**.

    ![Galerie d’applications] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Panorama9**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Panorama9 avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Panorama9, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Panorama9** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Panorama9** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Panorama9 d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à Panorama9 (par exemple : «*https://dashboard.panorama9.com/saml/access/3262*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Panorama9** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Panorama9 en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Gérer**, puis cliquez sur **Extensions**.

    ![Extensions] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")

7.  Dans la boîte de dialogue **Extensions** , cliquez sur **Authentification unique**.

    ![Authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Authentification unique")

8.  Dans la section **paramètres** , procédez comme suit :

    ![Paramètres] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Paramètres")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Panorama9** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL du fournisseur d’identité** .
    2.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **empreinte de certificat** .  

        >[AZURE.TIP]Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    3.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Panorama9, ils doivent être configurées dans Panorama9.  
Dans le cas Panorama9, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Panorama9** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **Gérer**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Utilisateurs")

3.  Cliquez sur **+**.

4.  Dans la section de données utilisateur, effectuez les opérations suivantes :

    ![Utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Utilisateurs")

    1.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Panorama9 utilisateur compte outils de création ou API fournies par Panorama9 aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Pour affecter des utilisateurs à Panorama9, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Panorama9** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).