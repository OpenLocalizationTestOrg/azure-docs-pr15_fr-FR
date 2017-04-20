<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jobscience | Microsoft Azure" 
    description="Découvrez comment utiliser Jobscience avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Didacticiel : Intégration d’Azure Active Directory avec Jobscience
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Jobscience.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Jobscience de l’authentification unique activée
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Jobscience sera en mesure d’authentification unique dans l’application sur votre site de société Jobscience (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Jobscience
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scénario")
##<a name="enabling-the-application-integration-for-jobscience"></a>L’activation de l’intégration des applications pour Jobscience
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Jobscience.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Jobscience, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **jobscience**.

    ![Galerie d’applications] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Jobscience**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Jobscience avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Jobscience, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Jobscience en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programme d’installation")

3.  Dans le volet de navigation gauche, dans la section **administrer** , cliquez sur **Gestion des domaines** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été correctement configuré, assurez-vous qu’il se trouve dans «**Étape 4 déployée vers des utilisateurs**» et passez en revue vos «**Mes paramètres de domaine**».

    ![Domaine déployé à utilisateur] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domaine déployé à utilisateur")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre portail classique Azure.

6.  Dans la page de l’intégration **Jobscience** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurer l’authentification unique")

7.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Jobscience** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurer l’authentification unique")

8.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Jobscience URL de connexion** , entrez votre URL à l’aide du modèle suivant «*http://company.my.salesforce.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurer des URL de l’application")

9.  Dans la page **configuration de l’authentification unique en Jobscience** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurer l’authentification unique")

10. Sur le site de société Jobscience, cliquez sur **Les contrôles de sécurité**, puis cliquez sur **Paramètres d’authentification unique**.

    ![Contrôles de sécurité] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Contrôles de sécurité")

11. Dans la section **Paramètres d’authentification unique** , procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Paramètres d’authentification unique")

    1.  Sélectionnez **SAML activé**.
    2.  Cliquez sur **Nouveau**.

12. Dans la boîte de dialogue **SAML unique authentification paramètre modifier** , procédez comme suit :

    ![Paramètre authentification unique de le SAML] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "Paramètre authentification unique de le SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en Jobscience** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur**
    3.  Dans la zone de texte **Id de l’entité** , tapez **https://salesforce-jobscience.com**
    4.  Cliquez sur **Parcourir** pour télécharger votre certificat Azure AD.
    5.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de la fédération de l’objet utilisateur**.
    6.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité est dans l’élément NameIdentfier de l’instruction de l’objet**.
    7.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en Jobscience** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité**
    8.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique en Jobscience** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion du fournisseur d’identité**
    9.  Cliquez sur **Enregistrer**.

13. Dans le volet de navigation gauche, dans la section **administrer** , cliquez sur **Gestion des domaines** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

14. Dans la page de **Mon domaine** , dans la section **Page de connexion personnalisation** , cliquez sur **Modifier**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Page de connexion de personnalisation")

15. Dans la page de **Personnalisation de Page de connexion** , dans la section **Service d’authentification** , le nom de vos **Paramètres de l’authentification unique SAML** s’affiche. Sélectionnez-le, puis cliquez sur **Enregistrer**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Page de connexion de personnalisation")

16. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurer l’authentification unique")
  
Pour obtenir le SP authentification unique créé par l’URL de connexion cliquez sur les **paramètres d’authentification unique** dans la section de menu **Des contrôles de sécurité** .

![Contrôles de sécurité] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Contrôles de sécurité")
  
Cliquez sur le profil de l’authentification unique que vous avez créé à l’étape précédente.  
Cette page affiche l’authentification unique sur l’URL de votre société (par exemple, *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Jobscience, ils doivent être configurées dans Jobscience.  
Dans le cas Jobscience, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Jobscience** en tant qu’administrateur.

2.  Accédez au programme d’installation

    ![Programme d’installation] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programme d’installation")

3.  Accédez à **Gérer les utilisateurs \> utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Tous les utilisateurs")

5.  Dans la boîte de dialogue **Modifier l’utilisateur** , procédez comme suit :

    ![Modifier utilisateur] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "Modifier utilisateur")

    1.  Tapez le prénom nom, alias, messagerie, les propriétés de nom et surnom utilisateur de l’utilisateur Azure AD que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD obtenez un message électronique contenant un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Jobscience utilisateur compte outils de création ou API fournies par Jobscience aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Pour affecter des utilisateurs à Jobscience, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Jobscience **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).