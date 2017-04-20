<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec PolicyStat | Microsoft Azure" 
    description="Découvrez comment utiliser PolicyStat avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Didacticiel : Azure Active Directory intégration avec PolicyStat
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et PolicyStat.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client PolicyStat
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à PolicyStat sera en mesure d’authentification unique dans l’application sur votre site de société PolicyStat (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour PolicyStat
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Scénario")
##<a name="enabling-the-application-integration-for-policystat"></a>L’activation de l’intégration des applications pour PolicyStat
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Pour activer l’intégration des applications pour PolicyStat, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **PolicyStat**.

    ![Galerie d’applications] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **PolicyStat**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à PolicyStat avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Votre application PolicyStat attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **PolicyStat** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à PolicyStat** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application PolicyStat URL (par exemple : *« https://demo-azure.policystat.com »*), puis cliquez sur **suivant**.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique en PolicyStat** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise PolicyStat en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin** , puis cliquez sur **Configuration de l’authentification unique** dans le volet de navigation gauche.

    ![Menu de l’administrateur] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu de l’administrateur")

7.  Dans la section **paramètres** , sélectionnez **Activer la seule authentification l’intégration**.

    ![Configuration de l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuration de l’authentification unique")

8.  Cliquez sur **Configurer les attributs**et puis, dans la section **Configurer les attributs** , procédez comme suit :

    ![Configuration de l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration de l’authentification unique")

    1.  Dans la zone de texte **Attribut Username** , tapez **uid**.
    2.  Dans la zone de texte **Prénom attribut** , tapez le **prénom**.
    3.  Dans la zone de texte **Dernier attribut de nom** , tapez le **nom**.
    4.  Dans la zone de texte **Attribut de messagerie** , tapez **emailaddress**.
    5.  Cliquez sur **Enregistrer les modifications**.

9.  Cliquez sur **Votre IDP métadonnées**et puis, dans la section **Your IDP métadonnées** , procédez comme suit :

    ![Configuration de l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration de l’authentification unique")

    1.  Ouvrir votre fichier de métadonnées téléchargé, copiez le contenu et collez-le dans la zone de texte **Votre identité fournisseur de métadonnées**
    2.  Cliquez sur **Enregistrer les modifications**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurer l’authentification unique")

11. 12. Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributs")

13. Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributs")

    1.  Cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez **uid**.
    3.  Dans la zone de texte **Valeur de l’attribut** , sélectionnez **ExtractMailPrefix()**.
    4.  Dans la liste **courrier** , sélectionnez **User.mail**.
    5.  Cliquez sur **terminé**.
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à PolicyStat, ils doivent être configurées dans PolicyStat.  
PolicyStat prend en charge seulement dans les temps approvisionnement de l’utilisateur. Cela signifie, vous n’avez pas besoin ajouter manuellement les utilisateurs à PolicyStat.  
Les utilisateurs seront ajoutés automatiquement lors de leur première connexion via l’authentification unique sur.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres PolicyStat utilisateur compte outils de création ou API fournies par PolicyStat aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Pour affecter des utilisateurs à PolicyStat, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **PolicyStat **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).