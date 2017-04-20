<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Innotas | Microsoft Azure"
    description="Découvrez comment utiliser Innotas avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Didacticiel : Intégration d’Azure Active Directory avec Innotas
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Innotas.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Innotas
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Innotas sera en mesure d’authentification unique dans l’application sur votre site de société Innotas (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Innotas
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Scénario")
##<a name="enabling-the-application-integration-for-innotas"></a>L’activation de l’intégration des applications pour Innotas
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Innotas.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Innotas, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Innotas**.

    ![Galerie d’applications] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Innotas**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Innotas avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Innotas** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777334.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Innotas** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777335.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Innotas URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Innotas.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurer des URL de l’application")

4.  Sur **configuration de l’authentification unique en Innotas** page, afin de télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**, et puis les fichier de données localement en tant que **c:\\InnotasMetaData.xml**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777337.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à Innotas équipe de support technique. L’équipe de support technique a configure de l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777338.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Innotas de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à Innotas à l’aide du panneau d’accès, Innotas vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Innotas.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Pour affecter des utilisateurs à Innotas, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Innotas **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).