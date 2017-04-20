<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lucidchart | Microsoft Azure" 
    description="Découvrez comment utiliser Lucidchart avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Didacticiel : Intégration d’Azure Active Directory avec Lucidchart
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Lucidchart.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Lucidchart d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Lucidchart sera en mesure d’authentification unique dans l’application sur votre site de société Lucidchart (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Lucidchart
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scénario")
##<a name="enabling-the-application-integration-for-lucidchart"></a>L’activation de l’intégration des applications pour Lucidchart
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Lucidchart.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Lucidchart, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Lucidchart**.

    ![Galerie d’applications] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Lucidchart**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Lucidchart avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Lucidchart** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Lucidchart** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Lucidchart d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Lucidchart (par exemple : «*https://chart2.office.lucidchart.com/saml/sso/azure*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Lucidchart** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de données localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Lucidchart en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **une équipe**.

    ![Équipe] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Équipe")

7.  Cliquez sur **Application \> gérer SAML**.

    ![Gérer les SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gérer les SAML")

8.  Dans la page de la boîte de dialogue **Paramètres d’authentification SAML** , effectuez les opérations suivantes :

    1.  Sélectionnez **Activer l’authentification SAML**, puis cliquez sur **facultatif**.
        ![Paramètres d’authentification SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Paramètres d’authentification SAML")
    2.  Dans la zone de texte **domaine** , tapez votre domaine, puis cliquez sur **Changer de certificat**.
        ![Changer de certificat] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Changer de certificat")
    3.  Ouvrir votre fichier de métadonnées téléchargé, copiez le contenu et collez-le dans la zone de texte **Télécharger les métadonnées** .
        ![Télécharger des métadonnées] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Télécharger des métadonnées")
    4.  Sélectionnez **Ajouter automatiquement un nouvel utilisateur à l’équipe**, puis cliquez sur **Enregistrer les modifications**.
        ![Enregistrer les modifications] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Enregistrer les modifications")

9.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Lucidchart de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Lucidchart.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lucidchart, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Lucidchart **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).