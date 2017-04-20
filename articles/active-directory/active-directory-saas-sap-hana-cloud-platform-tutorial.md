<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SAP HANA Cloud plateforme | Microsoft Azure" 
    description="Découvrez comment utiliser SAP HANA Cloud plateforme avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Didacticiel : Intégration d’Azure Active Directory avec plateforme en nuage HANA SAP
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et plateforme en nuage HANA SAP.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un compte de plateforme en nuage HANA SAP
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à SAP HANA Cloud plateforme sera en mesure d’authentification unique dans l’application à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Vous devez déployer votre propre application ou s’abonner à une application sur votre compte SAP HANA Cloud plateforme pour tester l’authentification unique. Dans ce didacticiel, une application est déployée dans le compte.
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour SAP HANA Cloud plateforme
2.  Configuration de l’authentification unique
3.  Attribution d’un rôle à un utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scénario")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>L’activation de l’intégration des applications pour SAP HANA Cloud plateforme
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour SAP HANA Cloud plateforme.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Pour activer l’intégration des applications pour plateforme en nuage HANA SAP, procédez comme suit :

1.  Dans le portail de gestion Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Plateforme en nuage HANA SAP**.

    ![Galerie d’applications] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Plateforme en nuage HANA SAP**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Hana SAP] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "Hana SAP")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à SAP HANA Cloud plateforme avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 à votre client SAP HANA Cloud plateforme.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **SAP HANA Cloud plateforme** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SAP HANA Cloud plateforme** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurer l’authentification unique")

3.  Dans une fenêtre de navigateur web différente, connectez-vous à SAP HANA Cloud plateforme Cockpit en https://account. \<hôte paysage\>.ondemand.com/cockpit (par exemple : *https://account.hanatrial.ondemand.com/cockpit*).

4.  Cliquez sur l’onglet **gestion de la confidentialité** .

    ![Gestion de la confidentialité] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Gestion de la confidentialité")

5.  Dans la section Gestion de la confidentialité, procédez comme suit :

    ![Obtenir des métadonnées] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Obtenir des métadonnées")

    1.  Cliquez sur l’onglet **Fournisseur de services Local** .
    2.  Pour télécharger le fichier de métadonnées SAP HANA Cloud plateforme, cliquez sur **Obtenir des métadonnées**.

6.  Dans le portail classique Azure Active, dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application de **Plateforme en nuage HANA SAP** . Il s’agit de l’URL spécifiques aux comptes d’une ressource protégée dans votre application de plateforme en nuage HANA SAP. L’URL est basée sur le modèle suivant : https:// *\<applicationName\>\<nom de compte\>.\< hôte paysage\>.ondemand.com/\<chemin d’accès\_à\_protégé\_ressource\> * (par exemple : *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Il s’agit de l’URL dans votre application de plateforme en nuage HANA SAP qui requiert que l’utilisateur s’authentifier.

    2.  Ouvrir le fichier de métadonnées SAP HANA Cloud plateforme téléchargé et recherchez la balise **ns3:AssertionConsumerService** .
    3.  Copiez la valeur de l’attribut **emplacement** et collez-le dans la zone de texte **URL de réponse SAP HANA Cloud plateforme** .

7.  Dans la page **configuration de l’authentification unique à SAP HANA Cloud plateforme** , cliquez sur **télécharger les métadonnées**pour télécharger les métadonnées de votre et puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurer l’authentification unique")

8.  Sur le Cockpit SAP HANA Cloud plateforme, dans la section **Fournisseur de services Local** , procédez comme suit :

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gestion d’approbation")

    1.  Cliquez sur **Modifier**.
    2.  **Type de Configuration**, sélectionnez **Personnaliser**.
    3.  En tant que **Nom du fournisseur Local**, conservez la valeur par défaut.
    4.  Pour générer une **Clé de signature** et une paire de clés de **Certificat de signature** , cliquez sur **Générer la paire de clés**.
    5.  **Propagation Principal**, sélectionnez **désactivé**.
    6.  **Forcer l’authentification**, sélectionnez **désactivé**.
    7.  Cliquez sur **Enregistrer**.

9.  Cliquez sur l’onglet **Fournisseur d’identité approuvés** , puis cliquez sur **Ajouter un fournisseur identité approuvé**.

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gestion d’approbation")

    >[AZURE.NOTE]Pour gérer la liste des fournisseurs d’identité approuvé, vous devez avoir choisi le type de configuration personnalisée dans la section fournisseur de services Local. Pour type de configuration par défaut, vous avez une approbation non modifiables et implicite vers le Service d’ID SAP. Pour aucun, vous n’avez pas les paramètres de gestion de la confidentialité.

10. Cliquez sur l’onglet **Général** , puis cliquez sur **Parcourir** pour télécharger le fichier de métadonnées téléchargé.

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gestion d’approbation")

    >[AZURE.NOTE] Après avoir téléchargé le fichier de métadonnées, les valeurs des **URL d’authentification unique**, **URL déconnexion** et de **Certificat de signature** sont remplis automatiquement.

11. Cliquez sur l’onglet **attributs** .

12. Sous l’onglet **attributs** , procédez comme suit :

    ![Attributs] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributs")

    1.  En cliquant sur **Add Assertion-Based attribut**, ajoutez les attributs d’assertion suivants :

        |Attribut assertion| Attribut principal|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   Prénom|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Surname|        nom|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/EmailAddress|Messagerie|

    >[AZURE.NOTE]La configuration des attributs dépend de comment les applications sur HCP sont développés, c'est-à-dire les attributs qu’ils s’attendre dans la réponse SAML et sous quel nom (attribut Principal) ils accèdent à cet attribut dans le code.
    >  
    >un.  L' **Attribut par défaut** dans la capture d’écran est uniquement à des fins d’illustration. Il n’est pas nécessaire de faire fonctionner le scénario.  
    >
    >b.  Les noms et les valeurs pour l' **Attribut Principal** indiqué dans la capture d’écran dépendent de la façon dont l’application est développée. Il est possible que votre application nécessite différents mappages.

13. Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en plateforme en nuage HANA SAP** , sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurer l’authentification unique")
  
Une étape facultative, vous pouvez configurer les groupes assertion pour votre fournisseur d’identité Azure Active Directory

>[AZURE.NOTE]À l’aide de groupes SAP HANA Cloud plateformes permet d’affecter dynamiquement un ou plusieurs utilisateurs à un ou plusieurs rôles dans vos applications SAP HANA Cloud plateforme, déterminées par les valeurs des attributs de l’assertion SAML 2.0. Par exemple, si l’assertion contient l’attribut «*contrat = temporaire*», vous souhaiterez peut-être affectés toutes les utilisateurs à ajouter au groupe «*temporaire*». Le groupe «*temporaire*» peut contenir un ou plusieurs rôles à partir d’une ou plusieurs applications déployées dans votre compte de plateforme en nuage HANA SAP.
>  
>Utiliser des groupes basés sur assertion si vous voulez pour plusieurs utilisateurs à un ou plusieurs des rôles d’applications dans votre compte SAP HANA Cloud plateforme l’attribution de masse. Si vous voulez uniquement affecter un seul ou un petit nombre d’utilisateurs à (a) ou les rôles spécifiques, nous vous recommandons de leur affectation directement dans l’onglet «**autorisations**» du cockpit plateforme en nuage HANA SAP.

##<a name="assigning-a-role-to-a-user"></a>Attribution d’un rôle à un utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à SAP HANA Cloud plateforme, vous devez attribuer des rôles dans la plateforme en nuage HANA SAP leur.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Pour attribuer un rôle à un utilisateur, procédez comme suit :

1.  Connectez-vous à votre **Plateforme en nuage SAP HANA** cockpit.

2.  Effectuez les opérations suivantes :

    ![Autorisations] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorisations")

    1.  Cliquez sur **autorisation**.
    2.  Cliquez sur l’onglet **utilisateurs** .
    3.  Dans la zone de texte **utilisateur** , tapez l’adresse de messagerie de l’utilisateur.
    4.  Cliquez sur **affecter** pour affecter l’utilisateur à un rôle.
    5.  Cliquez sur **Enregistrer**.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Pour affecter des utilisateurs à plateforme en nuage HANA SAP, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **SAP HANA Cloud plateforme** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).