<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Onit | Microsoft Azure" 
    description="Découvrez comment utiliser Onit avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Didacticiel : Intégration d’Azure Active Directory avec Onit
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Onit.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Onit d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Onit sera en mesure d’authentification unique dans l’application sur votre site de société Onit (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Onit
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-onit-tutorial/IC791166.png "Scénario")
##<a name="enabling-the-application-integration-for-onit"></a>L’activation de l’intégration des applications pour Onit
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Onit.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Onit, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-onit-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Onit**.

    ![Galerie d’applications] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Onit**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Onit avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Onit, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).
  
Votre application Onit attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Authentification unique] (./media/active-directory-saas-onit-tutorial/IC791168.png "Authentification unique")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Onit** , dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-onit-tutorial/IC791169.png "Attributs")

2.  Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    
  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|nom|User.userPrincipalName|
  	|Messagerie|User.Mail|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    3.  Dans la liste **Valeur de l’attribut** , sélectionnez la valeur de l’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

3.  Cliquez sur **appliquer les modifications**.

4.  Dans votre navigateur, cliquez sur **précédent** pour ouvrir la boîte de dialogue de **Démarrage rapide** de nouveau.

5.  Cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurer l’authentification unique")

6.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Onit** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurer l’authentification unique")

7.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Onit d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Onit (par exemple : «*https://ms-sso-test.onit.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurer des URL de l’application")

8.  Dans la page **configuration de l’authentification unique en Onit** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurer l’authentification unique")

9.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Onit en tant qu’administrateur.

10. Dans le menu dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")

11. Cliquez sur **Modifier Corporation**.

    ![Modifier Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Modifier Corporation")

12. Cliquez sur l’onglet **sécurité** .

    ![Modifier info.] (./media/active-directory-saas-onit-tutorial/IC791176.png "Modifier info.")

13. Sous l’onglet **sécurité** , effectuez les opérations suivantes :

    ![Authentification unique] (./media/active-directory-saas-onit-tutorial/IC791177.png "Authentification unique")

    1.  En tant que **Stratégie d’authentification**, sélectionnez **authentification et mot de passe**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Onit** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL cible Idp** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Onit** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion Idp** .
    4.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Idp certificat empreinte (SHA1)** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    5.  En tant que **Type de l’authentification unique**, sélectionnez **SAML**.
    6.  Dans la zone de **texte de bouton de connexion de l’authentification unique** , tapez un texte de bouton que vous le souhaitez.
    7.  Sélectionnez **Login avec l’authentification unique : requis pour les utilisateurs/domaines suivants**, tapez l’adresse de messagerie d’un utilisateur de test dans la zone de texte liée, puis cliquez sur **mettre à jour**.
        ![Modifier Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Modifier Corporation")

14. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Onit, ils doivent être configurées dans Onit.  
Dans le cas Onit, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Onit** en tant qu’administrateur.

2.  Cliquez sur **Ajouter un utilisateur**.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")

3.  Dans la page de la boîte de dialogue **Ajouter un utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-onit-tutorial/IC791181.png "Ajouter un utilisateur")

    1.  Tapez le **nom** et l' **Adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **créer**.  

        >[AZURE.NOTE] Le propriétaire du compte peuvent accéder à un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Onit utilisateur compte outils de création ou API fournies par Onit aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Pour affecter des utilisateurs à Onit, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Onit **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-onit-tutorial/IC791182.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-onit-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).