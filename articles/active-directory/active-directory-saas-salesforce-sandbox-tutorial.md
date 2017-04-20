<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Salesforce Sandbox | Microsoft Azure"
    description="Découvrez comment utiliser Salesforce Sandbox avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Didacticiel : Intégration d’Azure Active Directory avec Salesforce Sandbox
>[AZURE.TIP]Pour obtenir des commentaires, cliquez sur [ici](http://go.microsoft.com/fwlink/?LinkId=521878).
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Salesforce Sandbox.  
Sandbox vous offre la possibilité de créer plusieurs copies de votre organisation dans les environnements distincts pour diverses raisons, par exemple de développement, de test et de formation, sans compromettre les données et les applications de votre organisation de production Salesforce.  
Pour plus d’informations, voir [Vue d’ensemble de bac à sable](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un bac à sable dans Salesforce.com
  
Si vous n’avez encore un bac à sable valide dans Salesforce.com, vous devez contacter Salesforce.
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Salesforce Sandbox
2.  Configuration de l’authentification unique
3.  L’activation de votre domaine
4.  Configuration de mise en service de l’utilisateur
5.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scénario")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>L’activation de l’intégration des applications pour Salesforce Sandbox
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour Salesforce sandbox.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Salesforce sandbox, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis cliquez sur **Ajouter une application pour mon organisation à utiliser**.

    ![Que voulez-vous faire ?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, tapez **Salesforce Sandbox**.

    ![Galerie d’applications] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galerie d’applications")

6.  Dans le volet résultats, sélectionnez **Salesforce Sandbox**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Force de vente Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Force de vente Sandbox")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à force de vente avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Salesforce Sandbox** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Salesforce Sandbox** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Force de vente Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Force de vente Sandbox")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **URL de connexion** , entrez votre URL à l’aide du modèle suivant `http://company.my.salesforce.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurer des URL de l’application")

4. Si vous avez déjà configuré de l’authentification unique pour une autre instance Salesforce Sandbox dans votre annuaire, vous devez également configurer l' **identificateur** pour que la même valeur que l' **URL de session**. Le champ **identificateur** sont accessibles en cochant la case à cocher **Afficher les paramètres avancés** dans la page **Configurer les URL de l’application** de la boîte de dialogue.

4.  Dans la page **configuration de l’authentification unique en bac à sable Salesforce** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre sandbox Salesforce en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Programme d’installation")

7.  Dans le volet de navigation gauche, cliquez sur **Les contrôles de sécurité**, puis cliquez sur **Paramètres d’authentification unique**.

    ![Paramètres d’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Paramètres d’authentification unique")

8.  Dans la section Paramètres d’authentification unique, procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Paramètres d’authentification unique")

    un.  Sélectionnez **SAML activé**.
    
    b.  Cliquez sur **Nouveau**.

9.  Dans la section Paramètres d’authentification unique SAML, effectuez les opérations suivantes :

    ![Paramètres d’authentification unique SAML] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Paramètres d’authentification unique SAML")

    un.  Dans la zone de texte Nom, tapez le nom de la configuration (par exemple : *SPSSOWAAD\_Test*).
    
    b.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en bac à sable Salesforce** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    
    c.  Dans la zone de texte **Id de l’entité** , tapez **https://test.salesforce.com** s’il s’agit de la première instance Salesforce Sandbox que vous ajoutez à votre annuaire. Si vous avez déjà ajouté une instance de Salesforce Sandbox, puis pour le type **d’ID de l’entité** dans l' **URL de connexion**, qui doit être au format suivant :`http://company.my.salesforce.com`
    
    d.  Cliquez sur **Parcourir** pour télécharger le certificat téléchargé.
    
    e.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de la fédération de l’objet utilisateur**.
    
    f.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité est dans l’élément NameIdentifier de l’instruction de l’objet**.
    
    g.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en bac à sable Salesforce** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .
    
    h.  SFDC ne prend pas en charge SAML déconnexion.  Pour résoudre ce problème, collez « https://login.windows.net/common/wsfederation?wa=wsignout1.0 » dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    
    J’ai.  En tant que **Service fournisseur initialisé par demande de liaison**, sélectionnez **HTTP POST**.
    
    j. Cliquez sur **Enregistrer**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurer l’authentification unique")

##<a name="enabling-your-domain"></a>L’activation de votre domaine
  
Cette section suppose que vous avez déjà créé un domaine.  
Pour plus d’informations, voir [Définir votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Pour activer votre domaine, procédez comme suit :

1.  Dans le volet de navigation gauche, cliquez sur **Gestion des domaines**, puis sur **mon domaine.**

    ![Mon domaine] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mon domaine")

    >[AZURE.NOTE]Vérifiez que votre domaine a été configuré correctement.

2.  Dans la section **Paramètres de Page de connexion** , cliquez sur **Modifier**, puis, en tant que **Service d’authentification**, sélectionnez le nom de la SAML authentification paramètre unique de la section précédente et enfin, cliquez sur **Enregistrer**.

    ![Mon domaine] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mon domaine")
  
Dès que vous avez un domaine configuré, vos utilisateurs devraient utiliser l’URL de domaine pour vous connecter à la sandbox Salesforce.  
Pour obtenir la valeur de l’URL, cliquez sur le profil de l’authentification unique que vous avez créé dans la section précédente.
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
L’objectif de cette section est hiérarchique l’activation d’utilisateur mise en service des comptes d’utilisateurs Active Directory Salesforce Sandbox.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans le portail Salesforce, dans la barre de navigation supérieure, sélectionnez votre nom pour développer votre menu utilisateur :

    ![Mes paramètres] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mes paramètres")

2.  Dans le menu utilisateur, sélectionnez **Paramètres du site Mon** pour ouvrir votre page **Mes paramètres** .

3.  Dans le volet gauche, cliquez sur **personnel** pour développer la section personnelle, puis cliquez sur **Réinitialiser mon jeton de sécurité**:

    ![Mes paramètres] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mes paramètres")

4.  Dans la page **Réinitialiser mon jeton de sécurité** , cliquez sur **Réinitialiser le jeton de sécurité** pour demander un message électronique qui contient votre jeton de sécurité Salesforce.com.

    ![Nouveau jeton] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nouveau jeton")

5.  Vérifiez votre boîte de réception pour un message électronique à partir de Salesforce.com avec «**confirmation de sécurité Salesforce.com**» comme objet.

6.  Passez en revue ce courrier électronique et copiez la valeur du jeton de sécurité.

7.  Dans le portail classique Azure, dans la page de l’intégration d’application **salesforce Sandbox** , cliquez sur la **Configuration des utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

    ![Configuration des utilisateurs de configurer] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configuration des utilisateurs de configurer")

8.  Dans la page **Entrez vos informations d’identification Salesforce Sandbox pour activer la mise en service automatique de l’utilisateur** , fournissent des paramètres de configuration suivants :

    ![Force de vente Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Force de vente Sandbox")

    un.  Dans la zone de texte **Nom d’utilisateur Salesforce Sandbox** , tapez un nom de compte de sandbox Salesforce qui comporte le profil de **l’Administrateur système** dans Salesforce.com affectées.

    b.  Dans la zone de texte **Mot de passe administrateur Salesforce Sandbox** , tapez le mot de passe pour ce compte.

    c.  Dans la zone de texte **Jeton de sécurité utilisateur** , collez la valeur du jeton de sécurité.

    d.  Cliquez sur **Valider** pour vérifier votre configuration.

    e.  Cliquez sur le bouton **suivant** pour ouvrir la page de **Confirmation** .

9.  Dans la page de **Confirmation** , cliquez sur **terminé** pour enregistrer votre configuration.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Pour affecter des utilisateurs à Salesforce Sandbox, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **Salesforce Sandbox **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendez 10 minutes et vérifiez que le compte a été synchronisé avec Salesforce Sandbox.
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
