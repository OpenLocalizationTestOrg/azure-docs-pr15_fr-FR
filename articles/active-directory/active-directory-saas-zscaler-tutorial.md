<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Zscaler | Microsoft Azure" 
    description="Découvrez comment utiliser Zscaler avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Didacticiel : Intégration d’Azure Active Directory avec Zscaler
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Zscaler. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client dans Zscaler
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Zscaler
2.  Configuration de l’authentification unique
3.  Configuration des paramètres de proxy
4.  Configuration de mise en service de l’utilisateur
5.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scénario")

##<a name="enabling-the-application-integration-for-zscaler"></a>L’activation de l’intégration des applications pour Zscaler
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Zscaler.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Zscaler, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Zscaler**.

    ![Galerie d’applications] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Zscaler**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Zscaler avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat dans Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Zscaler** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Zscaler** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Ouverture de session configurer unique] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Ouverture de session configurer unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Zscaler URL de connexion** , tapez votre inscription URL auprès du Zscaler, puis cliquez sur **suivant**: 

    >[AZURE.NOTE] Veuillez contacter l’équipe de support Zscaler si vous ne savez pas quel est votre URL de connexion.

    ![Configurer des URL de l’application] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Zscaler** , effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "De configurer l’authentification unique")

    1.  Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement en tant que **c:\\Zscaler.cer**.
    2.  Copiez l' **URL de la demande d’authentification** dans votre Presse-papiers.

5.  Connectez-vous à votre client Zscaler.

6.  Dans le menu dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7.  Sous **Gérer les administrateurs et les rôles**, cliquez sur **Gérer les utilisateurs et l’authentification**.

    ![Gérer les administrateurs et les rôles] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gérer les administrateurs et les rôles")

8.  Dans la section **Option d’authentification choisir pour votre organisation** , effectuez les opérations suivantes :

    ![Choisissez les Options d’authentification] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choisissez les Options d’authentification")

    1.  Sélectionnez **authentifier à l’aide de l’authentification unique SAML**.
    2.  Cliquez sur **configurer les paramètres d’authentification unique de le SAML**.

9.  Dans la page de dialogue **Configurer SAML authentification paramètres uniques** , effectuez les opérations suivantes, puis cliquez sur **terminé**:

    ![Télécharger le certificat] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Télécharger le certificat")

    1.  Dans la zone de texte **URL du portail SAML auquel les utilisateurs sont envoyés pour l’authentification** , collez la valeur du champ **URL de la demande d’authentification** à partir du portail classique Azure.
    2.  Dans la zone de texte **attribut contenant le nom de connexion** , tapez **NameID**.
    3.  Dans le champ **Télécharger le certificat Public SSL** , téléchargez le certificat que vous avez téléchargé à partir du portail classique Azure.
    4.  Sélectionnez **Activer SAML automatique-mise en service**.

10. Dans la page de la boîte de dialogue **Configurer l’authentification des utilisateurs** , effectuez les opérations suivantes :

    ![Configurer l’authentification des utilisateurs] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurer l’authentification des utilisateurs")

    1.  Cliquez sur **Enregistrer**.
    2.  Cliquez sur **Activer maintenant**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "De configurer l’authentification unique")

##<a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1.  Démarrez **Internet Explorer**.

2.  Sélectionnez **options Internet** dans le menu **Outils** pour ouvrir la boîte de dialogue **Options Internet** .

    ![Options Internet] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Options Internet")

3.  Cliquez sur l’onglet **connexions** .

    ![Connexions] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connexions")

4.  Cliquez sur **paramètres réseau** pour ouvrir la boîte de dialogue **Paramètres du réseau local** .

5.  Dans la section serveur Proxy, procédez comme suit :

    ![Serveur proxy] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Serveur proxy")

    1.  Sélectionnez utiliser un serveur proxy pour votre réseau local.
    2.  Dans la zone de texte adresse, tapez **gateway.zscalertwo.net**.
    3.  Dans la zone de texte Port, tapez **80**.
    4.  Sélectionnez **Ignorer le serveur proxy pour les adresses locales**.
    5.  Cliquez sur **OK** pour fermer la boîte de dialogue **paramètres du réseau Local (LAN)** .

6.  Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet** .

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Zscaler, ils doivent être configurées dans Zscaler.  
Dans le cas Zscaler, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Zscaler** .

2.  Cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3.  Cliquez sur **Gestion des utilisateurs**.

    ![Gestion des utilisateurs] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gestion des utilisateurs")

4.  Dans l’onglet **utilisateurs** , cliquez sur **Ajouter**.

    ![Ajouter] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Ajouter")

5.  Dans la section Ajouter un utilisateur, procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Ajouter un utilisateur")

    1.  Tapez le **nom d’utilisateur**, **Nom d’affichage utilisateur**, **mot de passe**, **Confirmer le mot de passe**, puis **groupes** et le **service** d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre Zscaler utilisateur compte outil de création ou API fournies par Zscaler aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zscaler, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Zscaler** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
