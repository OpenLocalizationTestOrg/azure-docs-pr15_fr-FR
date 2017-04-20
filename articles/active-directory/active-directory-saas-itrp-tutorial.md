<properties
    pageTitle="Didacticiel : Azure Active Directory intégration avec ITRP | Microsoft Azure" 
    description="Découvrez comment utiliser ITRP avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Didacticiel : Azure Active Directory intégration avec ITRP
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et ITRP.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client ITRP
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à ITRP sera en mesure d’authentification unique dans l’application sur votre site de société ITRP (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour ITRP
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Scénario")
##<a name="enabling-the-application-integration-for-itrp"></a>L’activation de l’intégration des applications pour ITRP
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour ITRP.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Pour activer l’intégration des applications pour ITRP, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **ITRP**.

    ![Galerie d’applications] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **ITRP**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à ITRP avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour ITRP, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **ITRP** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-itrp-tutorial/IC771709.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ITRP** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **ITRP URL de connexion** , tapez votre URL à l’aide du modèle suivant « https://*\<nom de client\>. ITRP.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en ITRP** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez le fichier de certificat localement en tant que **c:\\ITRP.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise ITRP en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Dans le volet de navigation gauche, sélectionnez **De l’authentification unique**.

    ![Authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Authentification unique")

8.  Dans la section de configuration de l’authentification unique, procédez comme suit :

    ![Authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Authentification unique")

    ![Authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Authentification unique")

    1.  Cliquez sur **Activer**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en ITRP** , copiez la valeur **URL déconnexion à distance** et collez-le dans la zone de texte **URL déconnexion à distance** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en ITRP** , copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **URL de l’authentification unique SAML** .
    4.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **Empreinte de certificat** .
        
        >[AZURE.TIP]Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    5.  Cliquez sur **Enregistrer**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à ITRP, ils doivent être configurées dans ITRP.  
Dans le cas ITRP, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **ITRP** .

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **enregistrements**.

    ![Administrateur] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Administrateur")

3.  Dans le menu contextuel, sélectionnez **contacts**.

    ![Personnes] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Personnes")

4.  Cliquez sur **Ajouter une nouvelle personne** (« + »).

    ![Administrateur] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Administrateur")

5.  Dans la boîte de dialogue Ajouter une nouvelle personne, procédez comme suit :

    ![Utilisateur] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Utilisateur")

    1.  Tapez le **nom**, la **messagerie** d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ITRP utilisateur compte outils de création ou API fournies par ITRP aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Pour affecter des utilisateurs à ITRP, effectuez les opérations suivantes :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Dans la page de l’intégration **ITRP **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).