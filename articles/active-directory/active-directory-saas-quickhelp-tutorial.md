<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec aide rapide | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et aide rapide."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Didacticiel : Intégration d’Azure Active Directory avec aide rapide

L’objectif de ce didacticiel consiste à vous montrer comment intégrer aide rapide Azure Active Directory (AD Azure).  
Intégration d’aide rapide avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à l’aide rapide 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté sur Aide rapide (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec aide rapide, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique aide rapide lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’aide rapide à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-quickhelp-from-the-gallery"></a>Ajout d’aide rapide à partir de la galerie
Pour configurer l’intégration des aide rapide dans Azure AD, vous devez ajouter aide rapide à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter aide rapide à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **aide rapide**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **aide rapide**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec aide rapide basée sur un utilisateur de test appelé « Brian Simon ».


Pour configurer et tester Azure AD SSO avec aide rapide, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’une aide rapide tester l’utilisateur](#creating-a-quickhelp-test-user)** - d’avoir un homologue de Britta Simon dans aide rapide qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application aide rapide.


**Pour configurer Azure AD SSO avec aide rapide, procédez comme suit :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **aide rapide** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à l’aide rapide** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][7] 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application][8] 
 
     un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre site aide rapide (par exemple :* https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Si vous ne savez pas la valeur de l’authentification sur URL, contactez votre équipe de support technique aide rapide.

     b. Cliquez sur **suivant**.

 
4. Dans la page **configuration de l’authentification unique à l’aide rapide** , effectuer la suivante étapes : cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Qu’est Azure AD Connect][9] 



1. Ouverture de session sur le site de votre entreprise aide rapide en tant qu’administrateur.

2. Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Configurer l’authentification unique][21]


1. Dans le menu **Aide rapide d’administration** , cliquez sur **paramètres**.

    ![Configurer l’authentification unique][22]

1. Cliquez sur **paramètres d’authentification**.

1. Dans la page **Paramètres d’authentification** , procédez comme suit

    ![Configurer l’authentification unique][23]

    un. En tant que **Type de l’authentification unique**, sélectionnez **WSFederation**.

    b. Pour télécharger votre fichier de métadonnées Azure téléchargé, cliquez sur **Parcourir**, accédez au fichier, terminer, puis cliquez sur **Télécharger les métadonnées**.

    c. Dans la zone de texte de **messagerie** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Dans la première zone de texte **nom** , **tapez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Dans la zone de texte **Nom** , **tapez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    f. Dans la **Barre d’Action**, cliquez sur **Enregistrer**.







6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Qu’est Azure AD Connect][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  
Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-quickhelp-test-user"></a>Création d’un utilisateur de test d’aide rapide

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans aide rapide.
Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans aide rapide à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans aide rapide doit être établie.

Aide rapide prend en charge à temps de mise en service. Cela signifie, si nécessaire, un compte d’utilisateur est créé automatiquement dans aide rapide et le compte est lié au compte Azure AD.

Il n’existe aucun élément d’action pour vous dans cette section.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon utiliser Azure SSO par l’octroi d’accès à l’aide rapide.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à aide rapide, procédez comme suit :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **aide rapide**.

    ![Affecter utilisateur][202] 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette aide rapide dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application aide rapide.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png




