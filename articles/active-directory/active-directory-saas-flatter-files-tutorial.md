<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory fichiers plat | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et les fichiers plat."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Didacticiel : Intégration d’Azure Active Directory fichiers plat

L’objectif de ce didacticiel consiste à vous montrer comment intégrer fichiers plat Azure Active Directory (AD Azure).  
Intégration des fichiers plat avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès aux fichiers plat 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on aux fichiers plat (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure Active Directory

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD fichiers plat, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique plat fichiers lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de fichiers plat à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-flatter-files-from-the-gallery"></a>Ajout de fichiers plat à partir de la galerie
Pour configurer l’intégration des fichiers plat dans Azure AD, vous devez ajouter des fichiers plat à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter des fichiers plat à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Fichiers plat**.


    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. Dans le volet résultats, sélectionnez **Fichiers plat**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO fichiers plat basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans les fichiers plat à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans les fichiers plat doit être établi.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans les fichiers plat.
 
Pour configurer et tester Azure AD SSO fichiers plat, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un fichier plat tester l’utilisateur](#creating-a-halogen-software-test-user)** - à un homologue de Britta Simon plat fichiers comportent qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure AD et configurer l’authentification unique dans votre application de fichiers plat. Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64. Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l’authentification unique pour les fichiers plat, vous avez besoin d’un domaine enregistré. Si vous n’avez pas un domaine enregistré encore, contact vos fichiers plat prend en charge d’équipe via [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Pour configurer Azure AD SSO fichiers plat, effectuez les opérations suivantes :**

1. Dans le portail classique Azure AD, dans la page de l’intégration des **Fichiers plat** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à des fichiers plat** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] Fichiers plat utilise la même URL de connexion de l’authentification unique pour tous les clients : [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. Dans la page **configuration de l’authentification unique en fichiers plat** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


1. Authentification à votre application de fichiers plat en tant qu’administrateur.

2. Cliquez sur tableau de bord. 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Cliquez sur **paramètres**, puis puis effectuez les opérations suivantes sous l’onglet de la **société** : 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    un. Sélectionnez **utiliser SAML 2.0 pour l’authentification**.

    b. Cliquez sur **configurer SAML**.



2. Dans la boîte de dialogue **Configuration SAML** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    un. Dans la zone de texte domaine, tapez votre domaine enregistré.

    > [AZURE.NOTE] Si vous n’avez pas un domaine enregistré encore, contact vos fichiers plat prend en charge d’équipe via [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. Dans la Azure portail classique, sur la configurer unique authentification en boîte de dialogue fichiers plat, copt l’URL du Service authentification unique et collez-le dans la zone de texte URL du fournisseur d’identité.

    c.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

    >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    d.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et collez-le dans la zone de texte **Certificat fournisseur d’identité FlatterFiles** .

    e. Cliquez sur **mettre à jour**.

6. Dans le portail classique Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Authentification Azure AD unique][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Création d’un utilisateur de test fichiers plat

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans les fichiers plat.

**Pour créer un utilisateur appelé Britta Simon dans les fichiers plat, effectuez les opérations suivantes :**

1. Connectez-vous à votre site d’entreprise **Plat fichiers** en tant qu’administrateur.

2. Dans le volet de navigation gauche, cliquez sur **paramètres**, puis cliquez sur **l’onglet**d' utilisateurs.

    ![Un utilisateur de fichiers plat Cfreate](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Cliquez sur **Ajouter un utilisateur**. 

4. Dans la boîte de dialogue **Ajouter un utilisateur** , procédez comme suit :

    ![Un utilisateur de fichiers plat Cfreate](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.

    b. Dans la zone de texte **Nom** , tapez **Simon**. 

    c. Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de Brian dans le portail classique Azure.

    d. Cliquez sur **Envoyer**.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux fichiers plat.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon aux fichiers plat, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Fichiers plat**.

    ![Affecter utilisateur](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette de fichiers plat dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application de fichiers plat.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






