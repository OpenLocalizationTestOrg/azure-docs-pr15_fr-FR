<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Workrite | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workrite."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-workrite"></a>Didacticiel : Intégration d’Azure Active Directory avec Workrite

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Workrite Azure Active Directory (AD Azure).  
Intégration de Workrite avec Azure AD vous offre les avantages suivants : 


- Vous pouvez contrôler dans Azure AD qui accèdent à Workrite 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Workrite (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec Workrite, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Workrite lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout de Workrite à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-workrite-from-the-gallery"></a>Ajout de Workrite à partir de la galerie
Pour configurer l’intégration des Workrite dans Azure AD, vous devez ajouter Workrite à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Workrite à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 
 
    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.
 
    ![Applications][4]

6. Dans la zone Rechercher, tapez **Workrite**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Workrite**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Workrite basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Workrite à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Workrite doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Workrite.
 
Pour configurer et tester Azure AD SSO avec Workrite, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Workrite tester l’utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un homologue de Britta Simon dans Workrite est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Workrite.

**Pour configurer Azure AD SSO avec Workrite, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **Workrite** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Workrite** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][7] 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :
    
    ![Authentification Azure AD unique][8] 
 
     un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre site Workrite (par exemple : *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*).

     > [AZURE.NOTE] Contactez votre équipe de support technique Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk) si vous ne savez pas la valeur de l’authentification sur URL.

     b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique en Workrite** , effectuez les opérations suivantes :

    ![Authentification Azure AD unique][9] 

    un. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.

    b. Contactez votre équipe de support Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk), peovide avec téléchargés du certificat, l' **URL de l’émetteur** (ID entité), l' **URL du Service unique authentification**, l' **URL Sign-Out unique**et leur demander à la configuration de l’authentification unique pour votre application Workrite. 

    c. Cliquez sur **suivant**.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png)  

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png)  

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png) 
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-workrite-test-user"></a>Création d’un utilisateur de test Workrite

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans Workrite.

**Pour créer un utilisateur appelé Britta Simon dans Workrite, effectuez les opérations suivantes :**

1. Connectez-vous à votre site d’entreprise workrite en tant qu’administrateur.

2. Dans le volet de navigation, cliquez sur **administrateur**.

    ![Affecter utilisateur][400]

3. Accédez aux liens rapides, puis cliquez sur **Créer un utilisateur**. 

    ![Affecter utilisateur][401]

4. Dans la boîte de dialogue **Créer un utilisateur** , procédez comme suit :

    ![Affecter utilisateur][402]

    un. Tapez la **messagerie**, le **prénom** et le **nom de famille** d’un Azure valide utilisateur AD que vous souhaitez mettre en service.

    b. Sélectionnez **Administrateur Client** comme **Choisir rôle**. 

    c. Cliquez sur **Enregistrer**.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Workrite.

    ![Assign User][200] 

**Pour attribuer Britta Simon à Workrite, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Workrite**.

    ![Affecter utilisateur][202] 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 
1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette Workrite dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application Workrite.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png




