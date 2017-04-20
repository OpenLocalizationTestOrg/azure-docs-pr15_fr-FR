<properties 
    pageTitle="Didacticiel : Azure Active Directory case l’intégration à | Microsoft Azure" 
    description="Découvrez comment utiliser zone avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Didacticiel : Azure Active Directory case l’intégration à


  
L’objectif de ce didacticiel consiste à afficher l’intégration d’Azure et boîte.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client de test dans la zone
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à la zone sera en mesure d’authentification unique dans l’application sur votre site de société zone (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications de zone
2.  Configuration de l’authentification unique
3.  Configuration utilisateur et le groupe mise en service
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-box-tutorial/IC769537.png "Scénario")



##<a name="enabling-the-application-integration-for-box"></a>L’activation de l’intégration des applications de zone
  
L’objectif de cette section est hiérarchique comment activer l’intégration de l’application zone.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Pour activer l’intégration de l’application zone, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-box-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-box-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **zone**.

    ![Galerie d’applications] (./media/active-directory-saas-box-tutorial/IC701023.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **zone**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zone] (./media/active-directory-saas-box-tutorial/IC701024.png "Zone")



##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à zone avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez télécharger les métadonnées sur Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **zone** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769538.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la zone** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769539.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **URL de client de zone** , tapez l’URL de votre client zone (par exemple : https://<mydomainname>. box.com), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique zone à** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**, puis sur le fichier de données localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC669824.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à zone équipe de support technique. L’équipe de support technique a configure de l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769540.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
L’objectif de cette section est hiérarchique comment activer la mise en service des comptes d’utilisateurs Active Directory à boîte.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail classique Azure, dans la page de l’intégration d’application **boîte** , cliquez sur la **Configuration des utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** . 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769541.png "Activer la mise en service automatique de l’utilisateur")

2. Dans la page de dialogue **utilisateur à la zone de mise en service** , cliquez sur **Activer la mise en service de l’utilisateur**. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769544.png "Activer la mise en service automatique de l’utilisateur")

3. Dans la page **se connecter à accorder l’accès à la zone** , fournissent les informations d’identification requises, puis cliquez sur **Autoriser**. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769546.png "Activer la mise en service automatique de l’utilisateur")


4. Cliquez sur **accorder l’accès à la zone** pour autoriser cette opération et revenir au portail classique Azure. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769549.png "Activer la mise en service automatique de l’utilisateur")


5. Dans la page **Options de mise en service** , les cases à cocher des **types d’objets à disposition** permettent de choisir les objets de groupe sont mis en service à zone en plus des objets utilisateur ou non.  Pour plus d’informations, voir « Section utilisateurs et groupes affectation » ci-dessous.


6. Pour terminer la configuration, cliquez sur le bouton d’achèvement. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769551.png "Activer la mise en service automatique de l’utilisateur")



##<a name="assigning-a-test-user"></a>Affectation d’un utilisateur de test
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Pour attribuer des utilisateurs à la zone, procédez comme suit :

1. Dans le portail classique Azure, créez un compte de test.

2. Dans la page de l’intégration application **boîte **, cliquez sur **attribuer aux utilisateurs**. 

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-box-tutorial/IC769552.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir. 

    ![Oui] (./media/active-directory-saas-box-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendez 10 minutes et vérifiez que le compte a été synchronisé pour la zone.

Vérification d’abord, vous pouvez vérifier l’état de mise à disponibilité, en cliquant sur tableau de bord dans le D sur la page de l’intégration d’application zone sur le portail classique Azure.

![Tableau de bord] (./media/active-directory-saas-box-tutorial/IC769553.png "Tableau de bord")

Un utilisateur terminé cycle de mise en service est indiqué par un état associé :

![Statut d’intégration] (./media/active-directory-saas-box-tutorial/IC769555.png "Statut d’intégration")


Dans votre client de la boîte de dialogue, les utilisateurs synchronisés sont répertoriés sous **Managed Users** dans la **Console d’administration**.

![Statut d’intégration] (./media/active-directory-saas-box-tutorial/IC769556.png "Statut d’intégration")


##<a name="assigning-users-and-groups"></a>Affectation d’utilisateurs et groupes

La **zone > utilisateurs et groupes** onglet dans le portail classique Azure permet de vous permettent de spécifier les utilisateurs et groupes doivent être autorisés à accéder à la zone. Affectation d’un utilisateur ou groupe entraîne les opérations suivantes pour se produisent :

* Azure AD permet à l’utilisateur affectée (soit par affectation directe ou l’appartenance aux groupes) s’authentifier à zone. Si un utilisateur n’est pas affecté, Azure AD ne permettra pas à se connecter à la zone et renverra une erreur dans la page de connexion Azure AD.

* Vignette d’une application pour la zone est ajouté à l’utilisateur [Lanceur d’applications](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Si la mise en service automatique est activée, les utilisateurs affectés et/ou les groupes sont ajoutés à la mise à disponibilité file pour être configuré automatiquement.

    * Si une seule objets utilisateur ont été configurés pour être mis en service, puis tous les utilisateurs directement affectées sont placées dans la file d’attente mise à disponibilité, et tous les utilisateurs qui font partie d’aucun groupe affectées seront placés dans la file d’attente mise en service. 
    
    * Si les objets de groupe ont été configurés pour être mis en service, tous les objets de groupe affecté sont mis en service pour la zone, ainsi que tous les utilisateurs qui appartiennent à ces groupes. L’appartenance aux groupes et d’utilisateurs est conservés lors écrits dans la zone.
    
Vous pouvez utiliser la **attributs > authentification unique** onglet pour configurer les attributs de l’utilisateur (ou revendications) sont présentées à zone pendant l’authentification basée sur les SAML et la **attributs > Provisioning** onglet pour configurer comment attributs utilisateur et le groupe flux à partir d’Azure AD zone au cours de la mise en service des opérations. Consultez les ressources ci-après pour plus d’informations.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Personnalisation des affirmations émises dans le jeton SAML](active-directory-saml-claims-customization.md)
* [Mise en service : Personnaliser les mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md)
* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
