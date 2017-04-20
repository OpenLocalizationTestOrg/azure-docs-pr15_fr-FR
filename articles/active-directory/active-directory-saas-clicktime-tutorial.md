<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ClickTime | Microsoft Azure" 
    description="Découvrez comment utiliser ClickTime avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Didacticiel : Intégration d’Azure Active Directory avec ClickTime

Dans ce didacticiel, vous allez apprendre à intégrer ClickTime Azure Active Directory (AD Azure).

Intégration de ClickTime avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à ClickTime
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to ClickTime (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec ClickTime, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique ClickTime lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de ClickTime à partir de la galerie
2. Configuration et test Azure AD authentification unique

##<a name="adding-clicktime-from-the-gallery"></a>Ajout de ClickTime à partir de la galerie

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour ClickTime.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Pour activer l’intégration des applications pour ClickTime, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **ClickTime**.

    ![Galerie d’applications] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **ClickTime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec ClickTime basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans ClickTime est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans ClickTime doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans ClickTime.

Pour configurer et tester Azure AD SSO avec ClickTime, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un ClickTime tester l’utilisateur](#creating-a-clicktime-test-user)** - d’avoir un homologue de Britta Simon dans ClickTime est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à ClickTime avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  


>[AZURE.IMPORTANT] Afin de pouvoir configurer l’authentification unique sur votre client ClickTime, vous devez contacter le support technique ClickTime pour accéder à cette fonctionnalité est activée en premier.

**Pour configurer Azure AD SSO avec ClickTime, effectuez les opérations suivantes :**

1.  Dans le portail classique Azure, dans la page de l’intégration **ClickTime** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Activer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ClickTime** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "De configurer l’authentification unique")

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    un. Dans la zone de texte **IdentifierL** , tapez l’URL à l’aide du modèle suivant : **https://app.clicktime.com/sp/**
    
    b. Dans la zone de texte **URL de réponse** , tapez l’URL à l’aide du modèle suivant : **https://app.clicktime.com/Login/**

    c. Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en ClickTime** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "De configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise ClickTime en tant qu’administrateur.

5.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Préférences**, puis cliquez sur **Paramètres de sécurité**.

6.  Dans la section configuration de **Préférences d’authentification unique** , procédez comme suit :

    ![Paramètres de sécurité] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Paramètres de sécurité")

    un.  Sélectionnez **Autoriser** se connecter à l’aide d’authentification unique (SSO) avec **Azure AD**.
    
    b.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en ClickTime** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte de **Point de terminaison fournisseur identité** .

    c.  Ouvrez le certificat codé base 64 dans **le bloc-notes**, copiez le contenu et collez-le dans la zone de texte **Certificat X.509** .
    
    d.  Cliquez sur **Enregistrer**.

7.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à ClickTime, ils doivent être configurées dans ClickTime.  
Dans le cas ClickTime, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **ClickTime** .

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **société**, puis cliquez sur **personnes**.

    ![Personnes] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personnes")

3.  Cliquez sur **Ajouter une personne**.

    ![Ajouter des personnes] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Ajouter des personnes")

4.  Dans la section nouvelle personne, procédez comme suit :

    ![Personnes] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personnes")

    un.  Dans la zone de texte **adresse de messagerie** , tapez l’adresse de messagerie de votre compte Azure AD.
    
    b.  Dans la zone de texte **nom complet** , tapez le nom de votre compte Azure AD.  

    >[AZURE.NOTE] Si vous le souhaitez, vous pouvez définir des propriétés supplémentaires du nouvel objet personne.

    c.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ClickTime utilisateur compte outils de création ou API fournies par ClickTime mise en service des comptes d’utilisateurs Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux ClickTime.

![Affecter utilisateur][200]

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

**Pour attribuer Britta Simon à ClickTime, procédez comme suit**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **ClickTime**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]

## <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette ClickTime dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application ClickTime.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png