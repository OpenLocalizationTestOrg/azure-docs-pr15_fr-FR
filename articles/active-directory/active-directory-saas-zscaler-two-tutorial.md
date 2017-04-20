<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec deux Zscaler | Microsoft Azure" 
    description="Découvrez comment utiliser deux Zscaler avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Didacticiel : Azure Active Directory intégration avec deux Zscaler

L’objectif de ce didacticiel consiste à afficher l’intégration de deux ZScaler et Azure.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un deux ZScaler de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à deux ZScaler sera en mesure d’authentification unique dans l’application sur votre site de société deux ZScaler (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md)
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour deux ZScaler
2.  Configuration de l’authentification unique
3.  Configuration des paramètres de proxy
4.  Configuration de mise en service de l’utilisateur
5.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configurer l’authentification unique")

##<a name="enabling-the-application-integration-for-zscaler-two"></a>L’activation de l’intégration des applications pour deux ZScaler
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour deux ZScaler.

###<a name="to-enable-the-application-integration-for-zscaler-two-perform-the-following-steps"></a>Pour activer l’intégration des applications pour deux ZScaler, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Deux ZScaler**.

    ![Galerie d’applications] (./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **ZScaler deux**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ZScaler deux] (./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler deux")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à deux ZScaler avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 à votre client ZScaler deux.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration application **ZScaler deux** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à deux ZScaler** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **ZScaler deux se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application ZScaler deux, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configurer des URL de l’application")

    >[AZURE.NOTE] Vous pouvez obtenir la valeur réelle pour votre environnement à partir de votre équipe de support technique ZScaler deux si vous en avez besoin.

4.  Dans la page **configuration de l’authentification unique en deux ZScaler** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise ZScaler deux en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")

7.  Sous **Gérer les administrateurs et les rôles**, cliquez sur **Gérer les utilisateurs et l’authentification**.

    ![Gérer les utilisateurs et l’authentification] (./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Gérer les utilisateurs et l’authentification")

8.  Dans la section **Choisir les Options d’authentification pour votre organisation** , effectuez les opérations suivantes :

    ![Authentification] (./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentification")

    1.  Sélectionnez **authentifier à l’aide de l’authentification unique de le SAML**.
    2.  Cliquez sur **configurer les paramètres d’authentification unique de le SAML**.

9.  Dans la page de dialogue **Configurer SAML authentification paramètres uniques** , effectuez les opérations suivantes, puis cliquez sur **terminé**:

    ![Authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Authentification unique")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en deux ZScaler** , copiez la valeur de **l’URL de la demande d’authentification** et collez-le dans la zone de texte **URL du portail SAML auquel les utilisateurs sont envoyés pour l’authentification** .
    2.  Dans la zone de texte **attribut contenant le nom de connexion** , tapez **NameID**.
    3.  Pour télécharger votre certificat téléchargé, cliquez sur **Zscaler pem**.
    4.  Sélectionnez **Activer SAML automatique-mise en service**.

10. Dans la page de la boîte de dialogue **Configurer l’authentification des utilisateurs** , effectuez les opérations suivantes :

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")

    1.  Cliquez sur **Enregistrer**.
    2.  Cliquez sur **Activer maintenant**.

11. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en deux ZScaler** , sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configurer l’authentification unique")

##<a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1.  Démarrez **Internet Explorer**.

2.  Sélectionnez **options Internet** dans le menu **Outils** pour ouvrir la boîte de dialogue **Options Internet** .

    ![Options Internet] (./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Options Internet")

3.  Cliquez sur l’onglet **connexions** .

    ![Connexions] (./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connexions")

4.  Cliquez sur **paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres du réseau local** .

5.  Dans la section serveur Proxy, procédez comme suit :

    ![Serveur proxy] (./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Serveur proxy")

    1.  Sélectionnez utiliser un serveur proxy pour votre réseau local.
    2.  Dans la zone de texte adresse, tapez **gateway.zscalerone.net**.
    3.  Dans la zone de texte Port, tapez **80**.
    4.  Sélectionnez **Ignorer le serveur proxy pour les adresses locales**.
    5.  Cliquez sur **OK** pour fermer la boîte de dialogue **paramètres du réseau Local (LAN)** .

6.  Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet** .

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à deux ZScaler, elles doivent être configurées à deux ZScaler.  
Dans le cas de deux ZScaler, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Zscaler** .

2.  Cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")

3.  Cliquez sur **Gestion des utilisateurs**.

    ![Ajouter] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Ajouter")

4.  Dans l’onglet **utilisateurs** , cliquez sur **Ajouter**.

    ![Ajouter] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Ajouter")

5.  Dans la section Ajouter un utilisateur, procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Ajouter un utilisateur")

    1.  Tapez le **nom d’utilisateur**, **Nom d’affichage utilisateur**, **mot de passe**, **Confirmer le mot de passe**, puis **groupes** et le **service** d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ZScaler deux utilisateur compte outils de création ou API fournies par deux ZScaler aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-zscaler-two-perform-the-following-steps"></a>Pour affecter des utilisateurs à deux ZScaler, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page d’intégration d’application **ZScaler deux** , cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).