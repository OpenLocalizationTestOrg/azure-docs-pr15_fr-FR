<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec Bonus.ly | Microsoft Azure" 
    description="Découvrez comment utiliser Bonus.ly avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Didacticiel : Azure Active Directory intégration avec Bonus.ly

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Bonus.ly. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client test dans Bonus.ly

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Bonus.ly
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Scénario")
##<a name="enabling-the-application-integration-for-bonusly"></a>L’activation de l’intégration des applications pour Bonus.ly

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Bonus.ly.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Bonus.ly, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Activer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Activer l’authentification unique")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Bonus.ly**.

    ![Galerie d’applications] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Bonus.ly**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Bonus.ly avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Bonus.ly, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Bonus.ly** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Bonus.ly** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773683.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **URL de client Bonus.ly** , tapez l’URL de votre selon le modèle suivant « https://*\<nom de client\>. Bonus.ly*», puis cliquez sur **suivant**: 

    ![Configurer des URL de l’application] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Bonus.ly** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement en tant que **c:\\Bonusly.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773685.png "De configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, connectez-vous à votre client **Bonus.ly** .

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**, puis **intégrations d’entreprise et les applications**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Sous **Authentification unique**, sélectionnez **SAML**.

8.  Dans la page de dialogue **SAML** , effectuez les opérations suivantes :

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Bonus.ly** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL cible IdP l’authentification unique** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Bonus.ly** , copiez la valeur **d’ID de l’émetteur** et puis collez-les dans la zone de texte **IdP émetteur** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Bonus.ly** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion IdP** .
    4.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat** .

        >[AZURE.TIP] Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

9.  Cliquez sur **Enregistrer**.

10. Dans le portail classique de Microsoft Azure, sélectionnez la confirmation de la configuration, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773689.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Bonus.ly, ils doivent être configurées dans Bonus.ly.  
Dans le cas Bonus.ly, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans une fenêtre de navigateur web, connectez-vous à votre client Bonus.ly.

2.  Cliquez sur **paramètres**

    ![Paramètres] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Paramètres")

3.  Cliquez sur l’onglet **utilisateurs et bonus** .

    ![Utilisateurs et bonus] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Utilisateurs et bonus")

4.  Cliquez sur **Gérer les utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Gérer les utilisateurs")

5.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Ajouter un utilisateur")

6.  Dans la boîte de dialogue **Ajouter un utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Ajouter un utilisateur")

    1.  Tapez «**messagerie**, **prénom**, **nom**» d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte DAS recevront un message électronique contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Bonus.ly utilisateur compte outils de création ou API fournies par Bonus.ly aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Pour affecter des utilisateurs à Bonus.ly, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration Bonus.ly application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
