<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SERIE.jour.ouvre | Microsoft Azure" 
    description="Découvrez comment utiliser journée de travail avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Didacticiel : Intégration d’Azure Active Directory avec journée de travail
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et journée de travail. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client dans la journée de travail
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour la journée de travail
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Configuration de mise en service de l’utilisateur

![Scénario] (./media/active-directory-saas-workday-tutorial/IC782919.png "Scénario")

##<a name="enabling-the-application-integration-for-workday"></a>L’activation de l’intégration des applications pour la journée de travail
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Salesforce.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Pour activer l’intégration des applications pour la journée de travail, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis cliquez sur **Ajouter une application pour mon organisation à utiliser**.

    ![Que voulez-vous faire ?] (./media/active-directory-saas-workday-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, tapez **journée de travail**.

    ![SERIE.jour.ouvre] (./media/active-directory-saas-workday-tutorial/IC701021.png "SERIE.jour.ouvre")

6.  Dans le volet résultats, sélectionnez **journée de travail**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SERIE.jour.ouvre] (./media/active-directory-saas-workday-tutorial/IC701022.png "SERIE.jour.ouvre")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier sur journée de travail avec leur propre compte dans Azure Active Directory à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page de l’intégration d’application **journée de travail** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782920.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la journée de travail** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782921.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurer des URL de l’application")

    un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à se connecter à jour ouvré selon le modèle suivant :`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  Dans la zone de texte **URL de réponse journée de travail** , tapez l’URL de réponse journée de travail à l’aide du modèle suivant :`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]L’URL de votre réponse doit avoir un sous-domaine (par exemple : www, wd2, wd3, implémentation de wd3, wd5, wd5-l’implémentation). 
    >Fonctionne à l’aide de quelque chose comme «*http://www.myworkday.com*» mais pas «*http://myworkday.com*». 
 
4.  Dans la page **configuration de l’authentification unique en journée de travail** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782922.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise journée de travail en tant qu’administrateur.

6.  Accédez à **Menu \> atelier**.

    ![Atelier] (./media/active-directory-saas-workday-tutorial/IC782923.png "Atelier")

7.  Accédez à **l’Administration des comptes**.

    ![Administration des comptes] (./media/active-directory-saas-workday-tutorial/IC782924.png "Administration des comptes")

8.  Accédez à **Modifier le programme d’installation client – sécurité**.

    ![Modifier la sécurité du client] (./media/active-directory-saas-workday-tutorial/IC782925.png "Modifier la sécurité du client")

9.  Dans la section de **La Redirection d’URL** , procédez comme suit :

    ![URL de redirection] (./media/active-directory-saas-workday-tutorial/IC7829581.png "URL de redirection")

    un. Cliquez sur **Ajouter une ligne**.

    b. Dans la zone de texte **Rediriger l’URL de connexion** et la zone de texte **URL rediriger Mobile** , tapez l' **URL de client journée de travail** que vous avez entré dans la page **Configurer l’URL application** du portail classique Azure.
    
    c. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en journée de travail** , copiez l' **URL du Service Sign-Out unique**et collez-le dans la zone de texte **URL rediriger déconnexion** .

    d.  Dans la zone de texte **environnement** , tapez le nom de l’environnement.  


    >[AZURE.NOTE] La valeur de l’attribut environnement est liée à la valeur de l’URL de client :
    >
    >-   Si le nom de domaine du client SERIE.jour.ouvre URL commence par l’implémentation (par exemple : *https://impl.workday.com/\<client\>/login-saml2.htmld*), l’attribut **environnement** doit être défini à l’implémentation.
    >-   Si le nom de domaine commence par un autre processus, vous devez contacter journée de travail pour obtenir la valeur **d’environnement** correspondante.

10. Dans la section **Configuration SAML** , effectuez les opérations suivantes :

    ![Programme d’installation SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Programme d’installation SAML")

    un.  Sélectionnez **Activer l’authentification SAML**.

    b.  Cliquez sur **Ajouter une ligne**.

11. Dans la section fournisseurs d’identité SAML, effectuez les opérations suivantes :

    ![Fournisseurs d’identité SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Fournisseurs d’identité SAML")

    un. Dans la zone de texte Nom du fournisseur identité, tapez un nom de fournisseur (par exemple : *SPInitiatedSSO*).

    b. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en journée de travail** , copiez la valeur de **l’ID de fournisseur d’identité** et collez-le dans la zone de texte de **l’émetteur** .

    c. Sélectionnez **Activer Workday Initialted déconnexion**.

    d. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en journée de travail** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL de la requête déconnexion** .


    e. Cliquez sur **Le certificat de clé publique identité fournisseur**, puis cliquez sur **créer**. 

    ![Créer] (./media/active-directory-saas-workday-tutorial/IC782928.png "Créer")

    f. Cliquez sur **créer x509 clé publique**. 
        
    ![Créer] (./media/active-directory-saas-workday-tutorial/IC782929.png "Créer")


1. Dans la section **Afficher x509 la clé publique** , effectuez les opérations suivantes : 

    ![Afficher x509 la clé publique] (./media/active-directory-saas-workday-tutorial/IC782930.png "Afficher x509 la clé publique") 

    un. Dans la zone de texte **nom** , tapez un nom pour votre certificat (par exemple : *EPI\_SP*).
        
    b. Dans la zone de texte **Valide à partir de** , tapez la valide à partir de la valeur de l’attribut de votre certificat.
    
    c.  Dans la zone de texte **Valide à** , tapez la valeur de l’attribut de votre certificat valide.
        
    >[AZURE.NOTE] Vous pouvez obtenir le valide à partir de date et le valide à la date à partir du certificat téléchargé en double-cliquant dessus. Les dates sont répertoriés sous l’onglet **Détails** .

    d. Créer un fichier **codé en Base 64** à partir de votre certificat téléchargé.  

    >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    e.  Ouvrez votre certificat codé base 64 dans le bloc-notes, puis copiez le contenu de celle-ci.
    
    f.  Dans la zone de texte **certificat** , collez le contenu du Presse-papiers.
    
    g.  Cliquez sur **OK**.

12.  Effectuez les opérations suivantes : 

    ![Configuration de l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuration de l’authentification unique")

    un.  Activer la **x509 clé privée**.

    b.  Dans la zone de texte **ID du fournisseur de Service** , tapez **http://www.workday.com**.

    c.  Sélectionnez **Activer SP initialisé par l’authentification SAML**.

    d.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en journée de travail** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL du Service IdP l’authentification unique** .
     
    e. Sélectionnez **compressé pas de demande d’authentification SP exécutée par le serveur**.

    f. En tant que **Méthode de Signature de demande d’authentification**, sélectionnez **SHA256**. 
        
    ![Méthode de Signature de demande d’authentification] (./media/active-directory-saas-workday-tutorial/IC782932.png "Méthode de Signature de demande d’authentification") 
 
    g. Cliquez sur **OK**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Dans le portail classique Azure, dans la page **configuration de l’authentification unique en journée de travail** , cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782934.png "De configurer l’authentification unique")

13. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782935111.png "De configurer l’authentification unique")



##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour obtenir un utilisateur de test mis en service dans la journée de travail, vous devez contacter l’équipe de support journée de travail.  
L’équipe de support journée de travail créera l’utilisateur pour vous.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Pour attribuer aux utilisateurs pour la journée de travail, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **journée de travail **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-workday-tutorial/IC782935.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-workday-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).