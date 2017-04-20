<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Origami | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Origami."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Didacticiel : Intégration d’Azure Active Directory avec Origami

Dans ce didacticiel, vous allez apprendre à intégrer Origami Azure Active Directory (AD Azure).

Intégration Origami avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Origami
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Origami (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Origami, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Origami lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’Origami à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-origami-from-the-gallery"></a>Ajout d’Origami à partir de la galerie
Pour configurer l’intégration des Origami dans Azure AD, vous devez ajouter Origami à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Origami à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Origami**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. Dans le volet résultats, sélectionnez **Origami**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec Origami basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans Origami est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Origami doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Origami.

Pour configurer et tester Azure AD SSO avec Origami, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un Origami tester l’utilisateur](#creating-a-origami-test-user)** - d’avoir un homologue de Britta Simon dans Origami est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Origami.


**Pour configurer Azure AD SSO avec Origami, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Origami** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Origami** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png) 

    un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Origami selon le modèle suivant : **https://live.origamirisk.com/origami/account/login?account=\<nom de la société\> **
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique en Origami** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.



1. Connectez-vous au compte Origami avec des droits d’administrateur.

1. Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
  

1. Dans la page de dialogue seul signe dans le programme d’installation, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/123.png)

    un. Sélectionnez **Activer l’authentification unique**.

    b. Dans le portail classique Azure, copiez l' **URL de l’authentification unique SAML**et collez-le dans la zone de texte **URL de fournisseur d’identité connexion à la Page** .

    c. Dans le portail classique Azure, copiez l' **Authentification unique des URL du SERVICE**et collez-le dans la zone de texte **URL de la Page Sign-out fournisseur d’identité** .

    d. Cliquez sur **Parcourir** pour télécharger le certificat que vous avez téléchargé à partir du portail classique Azure.

    e. Cliquez sur **Enregistrer les modifications**.


6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-origami-test-user"></a>Création d’un utilisateur de test Origami

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Origami. 

1. Connectez-vous au compte Origami avec des droits d’administrateur.

2. Dans le menu dans la partie supérieure, cliquez sur **administrateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. Dans la boîte de dialogue **utilisateurs et sécurité** , cliquez sur **utilisateurs**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. Dans la boîte de dialogue Ajouter un nouvel utilisateur, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    un. Dans la zone de texte **Nom d’utilisateur** , tapez le nom d’utilisateur de Britta Simon dans le portail classique Azure.

    b. Dans la zone de texte **mot de passe** , tapez un passwotd.

    c. Dans la zone de texte **Confirmez le mot de passe** , retapez le mot de passe.

    d. Dans la zone de texte **nom** , tapez **Brian**.

    e. Dans la zone de texte **Nom** , tapez **Simon**.

    f. Cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

1. Attribuer des **Rôles d’utilisateur** et **d’Accès Client** à l’utilisateur. 

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser par l’octroi d’accès aux Origami Azure de l’authentification unique.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Origami, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Origami**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Origami dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Origami.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png
