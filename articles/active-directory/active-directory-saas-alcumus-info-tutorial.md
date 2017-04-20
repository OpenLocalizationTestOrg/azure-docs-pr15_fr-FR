<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Alcumus informations Exchange | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Alcumus informations Exchange."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-alcumus-info-exchange"></a>Didacticiel : Intégration d’Azure Active Directory avec Alcumus informations Exchange

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Alcumus informations Exchange Azure Active Directory (AD Azure).  
Intégration d’Alcumus informations Exchange avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à Alcumus informations Exchange 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Alcumus informations Exchange (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec Alcumus informations Exchange, vous devez les éléments suivants :

- Un abonnement [Azure AD](https://azure.microsoft.com/)
- Une authentification unique [Alcumus informations Exchange](http://www.alcumusgroup.com/) lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout de Alcumus informations Exchange à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-alcumus-info-exchange-from-the-gallery"></a>Ajout de Alcumus informations Exchange à partir de la galerie
Pour configurer l’intégration des Alcumus informations Exchange dans Azure AD, vous devez ajouter Alcumus informations Exchange à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Alcumus informations Exchange à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Alcumus informations Exchange**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Alcumus informations Exchange**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][400]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Alcumus informations Exchange basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Alcumus informations Exchange à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Alcumus informations Exchange doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Alcumus informations Exchange.
 
Pour configurer et tester Azure AD SSO avec Alcumus informations Exchange, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un échange d’informations Alcumus tester l’utilisateur](#creating-a-alcumus-info-exchange-test-user)** - d’avoir un homologue de Britta Simon dans Exchange informations Alcumus lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Alcumus informations Exchange.

**Pour configurer Azure AD SSO avec Alcumus informations Exchange, procédez comme suit :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **Alcumus informations Exchange** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6]

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Alcumus informations Exchange** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][7]

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes : 

    ![Authentification Azure AD unique][8]
 
    un. dans la zone de texte **URL de réponse** , tapez l’URL de l’utilisateur qui a été définie pour vous par votre équipe de support Alcumus informations Exchange.

    > [AZURE.NOTE] Si vous ne savez pas quelle est la valeur correcte, contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

    b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique en Alcumus informations Exchange** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Qu’est Azure AD Connect][9]

5. Contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com)lui fournir le fichier de métadonnées et les informer qu’ils doivent activer l’authentification unique pour vous.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Qu’est Azure AD Connect][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.
  
    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.
  
    c. Cliquez sur Suivant.



6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png) 
  

    un. Dans la zone de texte **nom** , tapez **Brian**.  
  
    b. Dans le **Nom de famille** txtbox, type, **Simon**.
  
    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.
  
    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
  
    e. Cliquez sur **suivant**.


7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png) 
 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.
  
    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-alcumus-info-exchange-test-user"></a>Création d’un utilisateur de test Alcumus informations Exchange

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans Alcumus informations Exchange.

**Pour créer un utilisateur appelé Britta Simon dans Alcumus informations Exchange, procédez comme suit :**

1. Contactez l’équipe de support Alcumus informations Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Alcumus informations Exchange.

![Affecter utilisateur][200]

**Pour attribuer Britta Simon à Alcumus informations Exchange, procédez comme suit :**

1. Dans le portail Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **Alcumus informations Exchange**.

    ![Affecter utilisateur][202]

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette Alcumus informations Exchange dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Alcumus informations Exchange.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png