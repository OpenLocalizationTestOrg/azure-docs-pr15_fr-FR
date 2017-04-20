<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SanSan | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SanSan."
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


# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Didacticiel : Intégration d’Azure Active Directory avec SanSan

Dans ce didacticiel, vous allez apprendre à intégrer SanSan Azure Active Directory (AD Azure).

Intégration de SanSan avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à SanSan
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to SanSan (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec SanSan, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique SanSan lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Microsoft Microsoft Azure AD SSO dans un environnement de test. Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de SanSan à partir de la galerie
2. Configuration et test de Microsoft Azure AD l’authentification unique


## <a name="adding-sansan-from-the-gallery"></a>Ajout de SanSan à partir de la galerie
Pour configurer l’intégration des SanSan dans Azure AD, vous devez ajouter SanSan à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SanSan à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **SanSan**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)

7. Dans le volet résultats, sélectionnez **SanSan**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configuration et test de Microsoft Azure AD l’authentification unique
Dans cette section, vous configurez et testez Microsoft Azure AD SSO avec SanSan basée sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans SanSan est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans SanSan doit être établie.
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans SanSan.

Pour configurer et tester Microsoft Azure AD SSO avec SanSan, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Microsoft Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - pour tester Microsoft Azure AD SSO avec Britta Simon.
4. **[Création d’un SanSan tester l’utilisateur](#creating-an-sansan-test-user)** - d’avoir un homologue de Britta Simon dans SanSan est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon à utiliser Microsoft Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuration de Microsoft Azure AD unique authentification

Dans cette section, vous activez Microsoft Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application SanSan.


**Pour configurer Microsoft Azure AD SSO avec SanSan, effectuez les opérations suivantes :**


1. Dans le portail classique Azure, dans la page de l’intégration **SanSan** application, cliquez sur Configurer authentification unique pour ouvrir la boîte de dialogue Configuration de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png) 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SanSan** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png) 

    un. Dans la zone de texte **Se d’URL** , tapez l’URL dans le modèle suivant :

  	| Environnement             | URL |
  	| :--                     | :-- |
  	| Web PC                  | `https://ap.sansan.com/v/saml2/<company name>/acs`|
  	| Application Mobile native       | `https://internal.api.sansan.com/saml2/<company name>/acs` |
  	| Paramètres de navigateur mobile | `https://ap.sansan.com/s/saml2/<company name>/acs` |


    b. Dans la zone de texte **identificateur** tapez l’URL dans le modèle suivant : 

  	| Environnement             | URL |
  	| :--                     | :-- |
  	| Web PC                  | `https://ap.sansan.com/v/saml2/<company name>`|
  	| Application Mobile native       | `https://internal.api.sansan.com/saml2/<company name>` |
  	| Paramètres de navigateur mobile | `https://ap.sansan.com/s/saml2/<company name>` |


    c. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique en SanSan** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png) 

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5.  Pour obtenir l’authentification unique configuré pour votre application, équipe de support technique SanSan contacts et ils aideront à configurer l’authentification unique. Indiquez les avec les informations suivantes :

    • Le **certificat** téléchargé

    • L' **ID de fournisseur d’identité**

    • L' **URL de l’authentification unique SAML**

    • Le **seul se déconnecter URL du Service**

> [AZURE.NOTE] Paramètre de navigateur PC fonctionne également pour l’application Mobile et de navigateur Mobile ainsi que web PC. 


6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.
Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-sansan-test-user"></a>Création d’un utilisateur de test SanSan

Dans cette section, vous créez un utilisateur appelé Britta Simon dans SanSan. Application SanSan besoin de l’utilisateur à mettre en service dans l’application avant de procéder à l’authentification unique. 


> [AZURE.NOTE] Si vous avez besoin créer un utilisateur manuellement ou par lots d’utilisateurs, vous devez contacter l’équipe de support SanSan.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux SanSan.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à SanSan, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **SanSan**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Microsoft Azure AD SSO configuration à l’aide du panneau d’accès.
Lorsque vous cliquez sur la vignette SanSan dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application SanSan.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png