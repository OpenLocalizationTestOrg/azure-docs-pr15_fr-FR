<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Kintone | Microsoft Azure" 
    description="Découvrez comment utiliser Kintone avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Didacticiel : Azure Active Directory intégration avec Kintone
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Kintone.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Kintone d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Kintone sera en mesure d’authentification unique dans l’application sur votre site de société Kintone (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Kintone
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Scénario")
##<a name="enabling-the-application-integration-for-kintone"></a>L’activation de l’intégration des applications pour Kintone
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Kintone.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Kintone, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Kintone**.

    ![Galerie d’applications] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Kintone**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Kintone avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Kintone** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Kintone** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se Kintone d’adresse URL** , tapez l’URL de votre selon le modèle suivant «*https://company.kintone.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Kintone** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

6.  Cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

7.  Cliquez sur **utilisateurs et Administration du système**.

    ![Utilisateurs et Administration du système] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Utilisateurs et Administration du système")

8.  Sous **Administration système \> sécurité** cliquez sur **Login**.

    ![Connexion] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Connexion")

9.  Cliquez sur **authentification SAML activer**.

    ![Authentification SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Authentification SAML")

10. Dans la section authentification SAML, effectuez les opérations suivantes :

    ![Authentification SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Authentification SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Kintone** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Kintone** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .
    3.  Cliquez sur **Parcourir** pour télécharger votre certificat téléchargé.
    4.  Cliquez sur **Enregistrer**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Kintone, ils doivent être configurées dans Kintone.  
Dans le cas Kintone, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Kintone** en tant qu’administrateur.

2.  Cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

3.  Cliquez sur **utilisateurs et Administration du système**.

    ![Administration système & utilisateur] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Administration système & utilisateur")

4.  Sous **Administration des utilisateurs**, cliquez sur **services et les utilisateurs**.

    ![Département et les utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Département et les utilisateurs")

5.  Cliquez sur **nouvel utilisateur**.

    ![Nouveaux utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Nouveaux utilisateurs")

6.  Dans la section **Nouvel utilisateur** , procédez comme suit :

    ![Nouveaux utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Nouveaux utilisateurs")

    1.  Tapez un **Nom complet**, **Nom de connexion**, **Nouveau mot de passe**, **Confirmer le mot de passe**, **Adresse de messagerie** et d’autres détails d’un compte DAS valide que vous souhaitez mettre en service dans la texboxes connexes.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Kintone utilisateur compte outils de création ou API fournies par Kintone aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Pour affecter des utilisateurs à Kintone, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Kintone **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).