<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Work.com | Microsoft Azure" 
    description="Découvrez comment utiliser Work.com avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Didacticiel : Intégration d’Azure Active Directory avec Work.com
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Work.com.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Work.com d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs AAD auquel vous avez affecter Work.com accès sera en mesure d’unique se dans l’application sur votre site de société Work.com (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Work.com
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Scénario")

##<a name="enabling-the-application-integration-for-workcom"></a>L’activation de l’intégration des applications pour Work.com
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Work.com.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Work.com, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Work.com**.

    ![Galerie d’applications] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Work.com**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Work.com avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat dans Work.com.com.

>[AZURE.NOTE] Pour configurer l’authentification unique, vous devez configurer un nom de domaine personnalisé Work.com encore. Vous devez définir au moins un nom de domaine, testez votre nom de domaine et le déployez dans votre organisation entière.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre client Work.com en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

3.  Dans le volet de navigation gauche, dans la section **administrer** , cliquez sur **Gestion des domaines** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été correctement configuré, assurez-vous qu’il se trouve dans «**Étape 4 déployée vers des utilisateurs**» et passez en revue vos «**Mes paramètres de domaine**».

    ![Domaine déployé à utilisateur] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Domaine déployé à utilisateur")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre portail classique Azure.

6.  Dans la page de l’intégration **Work.com **application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurer l’authentification unique")

7.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Work.com** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurer l’authentification unique")

8.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Work.com d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Work.com (par exemple : « *http://company.my.salesforce.com*»), puis cliquez sur **suivant**: 

    ![Configurer des URL de l’application] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurer des URL de l’application")

9.  Dans la page **configuration de l’authentification unique en Work.com** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurer l’authentification unique")

10. Connectez-vous à votre client Work.com.

11. Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

12. Développer le menu **Des contrôles de sécurité** , puis cliquez sur **Paramètres d’authentification unique**.

    ![Paramètres d’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Paramètres d’authentification unique")

13. Dans la page de la boîte de dialogue **Paramètres d’authentification unique** , procédez comme suit :

    ![SAML activé] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML activé")

    1.  Sélectionnez **SAML activé**.
    2.  Cliquez sur **Nouveau**.

14. Dans la section **Paramètres d’authentification unique SAML** , effectuez les opérations suivantes :

    ![Paramètre authentification unique de le SAML] (./media/active-directory-saas-work-com-tutorial/IC794114.png "Paramètre authentification unique de le SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.  

        >[AZURE.NOTE] Fournir une valeur pour **nom** remplit automatiquement la zone de texte **Nom de l’API** .

    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Work.com** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur** .
    3.  Pour télécharger le certificat téléchargé, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **Id de l’entité** , tapez **https://salesforce-work.com**.
    5.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de la fédération de l’objet utilisateur**.
    6.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité est dans l’élément NameIdentfier de l’instruction de l’objet**.
    7.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Work.com** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .
    8.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Work.com** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    9.  En tant que **Service fournisseur initialisé par demande de liaison**, sélectionnez **HTTP Post**.
    10. Cliquez sur **Enregistrer**.

15. Dans votre portail classique Work.com, dans le volet de navigation gauche, cliquez sur **Gestion des domaines** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Mon domaine")

16. Dans la page de **Mon domaine** , dans la section **Page de connexion personnalisation** , cliquez sur **Modifier**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Page de connexion de personnalisation")

17. Dans la page de **Personnalisation de Page de connexion** , dans la section **Service d’authentification** , le nom de vos **Paramètres de l’authentification unique SAML** s’affiche. Sélectionnez-le, puis cliquez sur **Enregistrer**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Page de connexion de personnalisation")

18. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour les utilisateurs Azure Active Directory doivent pouvoir se connecter, ils doivent être configurées pour Work.com.  
Dans le cas Work.com, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Work.com en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

3.  Accédez à **Gérer les utilisateurs \> utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Gérer les utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Tous les utilisateurs")

5.  Dans la section Modifier l’utilisateur, procédez comme suit :

    ![Modifier utilisateur] (./media/active-directory-saas-work-com-tutorial/IC794118.png "Modifier utilisateur")

    1.  Tapez le **Nom** **Alias**, **messagerie**, **nom d’utilisateur** et attributs **surnom** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Sélectionnez le **rôle**, de **licence utilisateur** et de **profil**.
    3.  Cliquez sur **Enregistrer**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Work.com utilisateur compte outils de création ou API fournies par Work.com aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Work.com, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration Work.com application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendez 10 minutes et vérifiez que le compte a été synchronisé avec Work.com.com.
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).