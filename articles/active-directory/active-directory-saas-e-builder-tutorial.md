<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec e-Builder | Microsoft Azure" 
    description="Découvrez comment utiliser e-générateur avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Didacticiel : Intégration d’Azure Active Directory avec e-Builder
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et e-générateur.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client e-Générateur de rapports
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez attribué un générateur de e seront en mesure d’authentification unique dans l’application sur votre site de société e-Générateur (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour le créateur de e
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scénario")
##<a name="enabling-the-application-integration-for-e-builder"></a>L’activation de l’intégration des applications pour le créateur de e
  
L’objectif de cette section est hiérarchique comment activer l’intégration de l’application pour le créateur de e.

###<a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le créateur de e, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-e-builder-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-e-builder-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **E-Générateur de rapports**.

    ![Galerie d’applications] (./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **E-Générateur de rapports**, puis cliquez sur **terminé** pour ajouter l’application.

    ![Générateur de e] (./media/active-directory-saas-e-builder-tutorial/IC777380.png "Générateur de e")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à e-créateur avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **E-Générateur** d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-e-builder-tutorial/IC777381.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à e-Générateur de rapports** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-e-builder-tutorial/IC777382.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone **e-générateur connectez-vous URL** , entrez votre URL à l’aide du modèle suivant «*https://\<nom de client\>.e Builder.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurer des URL de l’application")

4.  Sur **configuration de l’authentification unique au Générateur de e** page, afin de télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**, et puis les fichier de données localement en tant que **c:\\e-BuilderMetaData.xml**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-e-builder-tutorial/IC777384.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à e-Générateur d’équipe de support technique. L’équipe de support technique a configure de l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-e-builder-tutorial/IC777385.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement e-Générateur de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à e-Générateur de rapports à l’aide du panneau d’accès, e-générateur vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créée par le Générateur de e.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Pour attribuer aux utilisateurs Générateur de e, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **E-Générateur **d’application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-e-builder-tutorial/IC777386.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-e-builder-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
