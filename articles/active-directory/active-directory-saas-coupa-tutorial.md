<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Coupa | Microsoft Azure" 
    description="Découvrez comment utiliser Coupa avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Didacticiel : Intégration d’Azure Active Directory avec Coupa

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Coupa.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Coupa d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Coupa sera en mesure d’authentification unique dans l’application à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Coupa
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Scénario")
##<a name="enabling-the-application-integration-for-coupa"></a>L’activation de l’intégration des applications pour Coupa

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Coupa.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Coupa, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Coupa**.

    ![Galerie d’applications] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Coupa**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Coupa avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Coupa, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Coupa en tant qu’administrateur.

2.  Accédez à **le programme d’installation \> contrôle de sécurité**.

    ![Contrôles de sécurité] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Contrôles de sécurité")

3.  Pour télécharger le fichier de métadonnées Coupa sur votre ordinateur, cliquez sur **télécharger et importer les métadonnées SP**.

    ![Métadonnées Coupa SP] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Métadonnées Coupa SP")

4.  Dans une autre fenêtre de navigateur, connectez-vous au portail classique Azure.

5.  Dans la page de l’intégration **Coupa** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurer l’authentification unique")

6.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Coupa** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurer l’authentification unique")

7.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez les URL utilisée par vos utilisateurs pour se connecter à votre application Coupa (par exemple : «*http://company.Coupa.com*»).
    2.  Ouvrir votre fichier de métadonnées Coupa téléchargé, puis copiez l' **index AssertionConsumerService/URL**.
    3.  Dans la zone de texte **URL de réponse Coupa** , collez la valeur **AssertionConsumerService index/URL** .
    4.  Cliquez sur **suivant**.

8.  Dans la page **configuration de l’authentification unique en Coupa** , pour télécharger votre fichier de métadonnées, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurer l’authentification unique")

9.  Sur le site de société Coupa, accédez à **le programme d’installation \> contrôle de sécurité**.

    ![Contrôles de sécurité] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Contrôles de sécurité")

10. Dans la section **se connecter à l’aide des informations d’identification Coupa** , effectuez les opérations suivantes :

    ![Connectez-vous à l’aide des informations d’identification Coupa] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Connectez-vous à l’aide des informations d’identification Coupa")

    1.  Sélectionnez **se connecter à l’aide de SAML**.
    2.  Cliquez sur **Parcourir** pour télécharger votre fichier de métadonnées Azure Active téléchargé.
    3.  Cliquez sur **Enregistrer**.

11. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Coupa, ils doivent être configurées dans Coupa.  
Dans le cas Coupa, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Coupa** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **le programme d’installation**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Utilisateurs")

3.  Cliquez sur **créer**.

    ![Créer des utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Créer des utilisateurs")

4.  Dans la section **Utilisateur créer** , procédez comme suit :

    ![Détails de l’utilisateur] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Détails de l’utilisateur")

    1.  Tapez la **connexion**, **prénom**, **Nom**, **ID d’authentification unique**, les attributs de **messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **créer**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un message électronique avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Coupa utilisateur compte outils de création ou API fournies par Coupa aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Pour affecter des utilisateurs à Coupa, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Coupa **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
