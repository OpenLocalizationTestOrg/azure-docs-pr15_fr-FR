<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec ArcGIS | Microsoft Azure" 
    description="Découvrez comment utiliser ArcGIS avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Didacticiel : Azure Active Directory intégration avec ArcGIS

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et ArcGIS. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un ArcGIS de l’authentification unique activée abonnement

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à ArcGIS sera en mesure d’authentification unique dans l’application sur votre site de société ArcGIS (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour ArcGIS
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scénario")
##<a name="enabling-the-application-integration-for-arcgis"></a>L’activation de l’intégration des applications pour ArcGIS

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour ArcGIS.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Pour activer l’intégration des applications pour ArcGIS, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **ArcGIS**.

    ![Galerie Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galerie Applcation")

7.  Dans le volet résultats, sélectionnez **ArcGIS**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à ArcGIS avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **ArcGIS** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ArcGIS** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **ArcGIS URL de connexion** , tapez l’URL utilisées par vos utilisateurs à se connecter à l’aide du modèle suivant «*https://company.maps.arcgis.com*», puis sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en ArcGIS** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise ArcGIS en tant qu’administrateur.

6.  Cliquez sur **Modifier les paramètres**.

    ![Modifier les paramètres] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Modifier les paramètres")

7.  Cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sécurité")

8.  Sous **Connexions d’entreprise**, cliquez sur **Définir fournisseur d’identité**.

    ![Connexions de l’entreprise] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Connexions de l’entreprise")

9.  Dans la page de configuration du **Fournisseur d’identité un jeu** , procédez comme suit :

    ![Définir le fournisseur d’identité] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Définir le fournisseur d’identité")

    1.  Dans la zone de texte Nom, tapez le nom de votre organisation.
    2.  Pour les **que métadonnées pour le fournisseur d’identité entreprise seront fournies à l’aide de**, choisissez **Un fichier**.
    3.  Pour télécharger votre fichier de métadonnées téléchargé, cliquez sur **Choisir un fichier**.
    4.  Cliquez sur **définir le fournisseur d’identité**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à ArcGIS, ils doivent être configurées dans ArcGIS.  
Dans le cas ArcGIS, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **ArcGIS** .

2.  Cliquez sur **Inviter des membres**.

    ![Inviter des membres] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Inviter des membres")

3.  Sélectionnez **Ajouter des membres automatiquement sans l’envoyer un message électronique**, puis cliquez sur **suivant**.

    ![Ajouter automatiquement des membres] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Ajouter automatiquement des membres")

4.  Dans la page de dialogue **membres** , procédez comme suit :

    ![Ajouter et révision] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Ajouter et révision")

    1.  Mise en service, entrez le **prénom**, le **Nom** et la **messagerie** d’un compte DAS valide souhaité.
    2.  Cliquez sur **Ajouter et vérifier**.

5.  Passez en revue les données que vous avez entré, puis cliquez sur **Ajouter des membres**.

    ![Ajouter un membre] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Ajouter un membre")

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ArcGIS utilisateur compte outils de création ou API fournies par ArcGIS aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Pour affecter des utilisateurs à ArcGIS, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **ArcGIS **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
