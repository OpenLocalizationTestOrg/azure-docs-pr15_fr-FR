<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration avec MCM | Microsoft Azure" 
    description="Découvrez comment utiliser MCM avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Didacticiel : Intégration d’Azure Active Directory avec MCM
  
L’objectif de ce didacticiel consiste à vous montrer comment intégrer MCM Azure Active Directory (AD Azure).

Intégration MCM avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à MCM
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to MCM (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec MCM, vous devez les éléments suivants :

- Un abonnement Azure valide
- Une authentification unique MCM lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de MCM à partir de la galerie
2. Configuration et test Azure AD authentification unique

## <a name="adding-mcm-from-the-gallery"></a>Ajout de MCM à partir de la galerie
Pour configurer l’intégration des MCM dans Azure AD, vous devez ajouter MCM à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter MCM à partir de la galerie, procédez comme suit :**

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **MCM**.

    ![Galerie d’applications] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **MCM**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec MCM basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans MCM à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans MCM doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans MCM.

Pour configurer et tester Azure AD SSO avec MCM, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un MCM tester l’utilisateur](#creating-a-mcm-test-user)** - d’avoir un homologue de Britta Simon dans MCM est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO
  
Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application MCM.

**Pour configurer Azure AD SSO avec MCM, effectuez les opérations suivantes :**

1.  Dans le portail classique Azure, dans la page de l’intégration **MCM** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à MCM** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD unique authentification] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD unique authentification")

3.  Dans la page de la boîte de dialogue Configurer les paramètres de l’application, effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurer des URL de l’application")

    un. Dans la zone de texte **URL de connexion** , tapez : `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en MCM** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurer l’authentification unique")

5. Pour obtenir l’authentification unique configuré pour votre application, contactez votre équipe de support MCM. Joindre le fichier téléchargé métadonnées et partagez-le avec équipe MCM pour configurer l’authentification unique sur leur côté.

6.  Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurer l’authentification unique")

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurer l’authentification unique")


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

###<a name="creating-a-mcm-test-user"></a>Création d’un utilisateur de test MCM
  
Dans cette section, vous créez un utilisateur appelé Britta Simon dans MCM. Contactez l’équipe de support MCM afin d’ajouter des utilisateurs dans la plateforme MCM.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres MCM utilisateur compte outils de création ou API fournies par MCM aux comptes d’utilisateurs AAD mise en service.


###<a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
  
L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à MCM.
    
![Attribuer aux utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Attribuer aux utilisateurs")

**Pour attribuer Britta Simon à MCM, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Attribuer aux utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Attribuer aux utilisateurs")

2. Dans la liste des applications, sélectionnez **MCM**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Attribuer aux utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Attribuer aux utilisateurs")

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Attribuer aux utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Attribuer aux utilisateurs")


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette MCM dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application MCM.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)