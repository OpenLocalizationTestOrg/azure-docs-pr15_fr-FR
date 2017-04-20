<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk | Microsoft Azure" 
    description="Découvrez comment utiliser Jitbit Helpdesk avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Jitbit Helpdesk.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Jitbit Helpdesk
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Jitbit Helpdesk seront en mesure d’authentification unique dans l’application sur votre site de société Jitbit Helpdesk (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Jitbit Helpdesk
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scénario")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>L’activation de l’intégration des applications pour Jitbit Helpdesk
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Jitbit Helpdesk.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Jitbit Helpdesk, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Jitbit du support technique**.

    ![Galerie d’applications] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Jitbit du support technique**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Jitbit Helpdesk avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML. .  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Afin de pouvoir configurer l’authentification unique sur votre client Jitbit Helpdesk, vous devez contacter le support technique Jitbit Helpdesk pour accéder à cette fonctionnalité est activée en premier.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Du support technique Jitbit** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Jitbit du support technique** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Jitbit support technique URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Jitbit.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au support technique Jitbit** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\Jitbit Helpdesk.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Jitbit Helpdesk en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  Cliquez sur **Paramètres généraux**.

    ![Utilisateurs, sociétés et autorisations] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilisateurs, sociétés et autorisations")

8.  Dans la section configuration de **paramètres d’authentification** , procédez comme suit :

    ![Paramètres d’authentification] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Paramètres d’authentification")

    1.  Sélectionnez **Activer SAML 2.0 unique se** se connecter à l’aide d’authentification unique (SSO) avec **OneLogin**.
    2.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique au support technique Jitbit** , copiez la valeur **Service Provider (SP) initié point de terminaison** et collez-le dans la zone de texte **URL du point de terminaison** .
    3.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé base 64 et copiez le contenu de celle-ci dans votre Presse-papiers puis collez-le dans la zone de texte **Certificat X.509**
    5.  Cliquez sur **Enregistrer les modifications**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Jitbit Helpdesk, ils doivent être mis en service dans Jitbit Helpdesk.  
Dans le cas Jitbit Helpdesk, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Jitbit du support technique** .

2.  Dans le menu dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  Cliquez sur **utilisateurs, sociétés et autorisations**.

    ![Utilisateurs, sociétés et autorisations] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilisateurs, sociétés et autorisations")

4.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Ajouter un utilisateur")

5.  Dans la section créer, tapez les données du compte Azure AD que vous souhaitez mettre en service dans les zones de texte suivants : **nom d’utilisateur**, **E-mail**, **prénom**, deuxième **Prénom**

    ![Créer] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Créer")

6.  Cliquez sur **créer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Jitbit Helpdesk utilisateur compte outils de création ou API fournies par Jitbit Helpdesk aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Jitbit Helpdesk, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Du support technique Jitbit **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).