<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Freshdesk | Microsoft Azure" 
    description="Découvrez comment utiliser Freshdesk avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Azure Active Directory intégration avec Freshdesk
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Freshdesk.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Freshdesk
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Freshdesk sera en mesure d’authentification unique dans l’application sur votre site de société Freshdesk (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Freshdesk
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scénario")
##<a name="enabling-the-application-integration-for-freshdesk"></a>L’activation de l’intégration des applications pour Freshdesk
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Freshdesk.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Freshdesk, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Freshdesk**.

    ![Galerie d’applications] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Freshdesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Freshdesk avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Freshdesk, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Freshdesk** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Freshdesk** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Freshdesk URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. Freshdesk.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Freshdesk** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\Freshdesk.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Freshdesk en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Administrateur] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administrateur")

7.  Dans l’onglet **Paramètres généraux** , cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sécurité")

8.  Dans la section **sécurité** , effectuez les opérations suivantes :

    ![Authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Authentification unique")

    1.  **Pour **Unique se (SSO)**, sélectionnez.**
    2.  Sélectionnez **L’authentification unique SAML**.
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Freshdesk** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion SAML** .
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Freshdesk** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .
    5.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat de sécurité** .  

        >[AZURE.TIP]Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Freshdesk, ils doivent être configurées dans Freshdesk.  
Dans le cas Freshdesk, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Freshdesk** .

2.  Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Administrateur] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administrateur")

3.  Dans l’onglet **Paramètres généraux** , cliquez sur **Agents**.

    ![Agents] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Cliquez sur **nouvel Agent**.

    ![Nouvel Agent] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nouvel Agent")

5.  Dans la boîte de dialogue informations sur l’Agent, procédez comme suit :

    ![Informations de l’agent] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informations de l’agent")

    1.  Dans la zone de texte **Nom complet** , tapez le nom du compte Azure AD que vous souhaitez mettre en service.
    2.  Dans la zone de texte de **messagerie** , tapez l’adresse de messagerie Azure AD du compte Azure AD que vous souhaitez mettre en service.
    3.  Dans la zone de texte **titre** , tapez le titre du compte Azure AD que vous souhaitez mettre en service.
    4.  Sélectionnez **rôle exemptés**, puis cliquez sur **attribuer**.
    5.  Cliquez sur **Enregistrer**.
    
        >[AZURE.NOTE] Le titulaire du compte Azure AD obtenez un message électronique contenant un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Freshdesk utilisateur compte outils de création ou API fournies par Freshdesk aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Freshdesk, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Freshdesk **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).