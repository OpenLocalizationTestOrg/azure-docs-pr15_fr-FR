<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec @Task| Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et @Task."
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


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Didacticiel : Intégration d’Azure Active Directory avec@Task

L’objectif de ce didacticiel consiste à vous montrer comment intégrer @Task avec Azure Active Directory (AD Azure).  
Intégration @Task avec Azure AD vous fournit les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à@Task
- Vous pouvez autoriser les utilisateurs à obtenir automatiquement connecté sur à @Task (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec @Task, besoin des éléments suivants :

- Un abonnement Azure AD
- Un @Task authentification unique lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout de @Task à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-task-from-the-gallery"></a>Ajout de @Task à partir de la galerie
Pour configurer l’intégration de @Task dans Azure Active Directory, vous devez ajouter @Task à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter des @Task à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2] 

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3] 

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4] 

6. Dans la zone Rechercher, tapez **@Task**.

    ![Applications][5] 

7. Dans le volet résultats, sélectionnez **@Task**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique

L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec @Task basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD a besoin de savoir ce que l’utilisateur homologue dans @Task à un utilisateur dans Active Directory Azure est. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans @Task doit être établi.   
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans @Task.
 
Pour configurer et tester Azure AD SSO avec @Task, que vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un @Tasktest utilisateur](#creating-a-halogen-software-test-user) ** - d’avoir un homologue de Britta Simon dans @Taskthat est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre @Task application.

**Pour configurer Azure AD SSO avec @Task, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, sur le **@Task** intégration des applications de page, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Sur la **comment vous voulez que les utilisateurs à accéder au @Task ** de page, sélectionnez **Azure AD SSO**, puis sur **suivant**.

    ![Authentification Azure AD unique][7] 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application][8] 
 
     un. Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre @Task application (par exemple :*https://<Tenant name>.attask ondemand.com*).

     b. Cliquez sur **suivant**.

4. Sur la **configurer l’authentification unique en @Task ** page et cliquez sur **télécharger les métadonnées**, enregistrez le fichier de métadonnées localement sur votre ordinateur, puis cliquez sur **suivant**.

    ![Qu’est Azure AD Connect][9] 



1. Ouverture de session sur votre @Task site d’entreprise en tant qu’administrateur.

2. Accédez à la **Configuration de l’authentification unique**.


1. Dans la boîte de dialogue **De l’authentification unique** , procédez comme suit

    ![Configurer l’authentification unique][23]

    un. En tant que **Type**, sélectionnez **SAML 2.0**.

    b. Sélectionnez **l’ID du fournisseur de Service**.

    c. Dans le portail classique Azure, copiez l' **URL de connexion à distance**et collez-le dans la zone de texte **URL du portail de connexion** .

    d. Dans le portail classique Azure, copiez l' **URL du Service Sign-Out unique**et collez-le dans la zone de texte **URL Sign-Out** .

    e. Dans le portail classique Azure, copiez l' **URL de mot de passe de modification**et collez-le dans la zone de texte **URL de mot de passe de modification** .

    f. Cliquez sur **Enregistrer**.

6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Qu’est Azure AD Connect][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-an-task-test-user"></a>Création d’un @Task utilisateur test

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon @Task.


**Pour créer un utilisateur appelé Britta Simon dans @Task, effectuez les opérations suivantes :**

1. Connectez-vous à votre @Task site d’entreprise en tant qu’administrateur.

2. Dans le menu dans la partie supérieure, cliquez sur **personnes**.

3. Cliquez sur **nouvelle personne**. 

4. Dans la boîte de dialogue nouvelle personne, procédez comme suit :

    ![Créer un @Task utilisateur test][21] 

    un. Dans la zone de texte **nom** , tapez « Brian ».

    b. Dans la zone de texte **Nom** , tapez « Simon ».

    c. Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de Britta Simon dans Azure Active Directory.

    d. Cliquez sur **Ajouter une personne**.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en accorder l’accès à @Task.

![Affecter utilisateur][200] 

**Pour affecter Britta Simon à @Task, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **@Task**.

    ![Affecter utilisateur][202] 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur le @Task vignette dans le panneau d’accès, vous devez obtenir automatiquement connecté sur votre @Task application.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






