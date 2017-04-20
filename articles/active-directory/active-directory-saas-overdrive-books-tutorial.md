<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec dispositif fonction livres | Microsoft Azure" 
    description="Découvrez comment utiliser DISPOSITIF fonction livres avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Didacticiel : Intégration d’Azure Active Directory avec dispositif fonction livres
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et dispositif fonction.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un dispositif fonction de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à dispositif fonction seront en mesure d’authentification unique dans l’application sur votre site de société DISPOSITIF fonction (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour dispositif fonction
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scénario")
##<a name="enabling-the-application-integration-for-overdrive"></a>L’activation de l’intégration des applications pour dispositif fonction
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour dispositif fonction.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Pour activer l’intégration des applications pour dispositif fonction, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **DISPOSITIF fonction**.

    ![Galerie d’applications] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **DISPOSITIF fonction**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![DISPOSITIF fonction] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "DISPOSITIF fonction")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à dispositif fonction avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **DISPOSITIF fonction** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à dispositif fonction** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **DISPOSITIF fonction URL de connexion** , entrez votre URL à l’aide du modèle suivant «*http://mslibrarytest.libraryreserve.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurer des URL de l’application")

4.  Sur la **configuration de l’authentification unique en dispositif fonction** page, pour télécharger le fichier de métadonnées et envoyez-le à l’équipe de support DISPOSITIF fonction.

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "De configurer l’authentification unique")

    >[AZURE.NOTE]L’équipe de support DISPOSITIF fonction configure de l’authentification unique pour vous et vous envoie une notification lorsque la configuration est terminée.

5.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer la configuration des utilisateurs à dispositif fonction.  
Lorsqu’un utilisateur tente de se connecter à la vitesse supérieure, un dispositif fonction compte est automatiquement créé si nécessaire.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres dispositif fonction utilisateur compte outils de création ou API fournies par dispositif fonction aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Pour affecter des utilisateurs à dispositif fonction, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **DISPOSITIF fonction **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).