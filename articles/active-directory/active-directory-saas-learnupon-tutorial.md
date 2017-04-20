<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Novatus | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et LearnUpon."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Didacticiel : Intégration d’Azure Active Directory avec LearnUpon

L’objectif de ce didacticiel consiste à vous montrer comment intégrer LearnUpon Azure Active Directory (AD Azure).  
Intégration de LearnUpon avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à LearnUpon
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to LearnUpon (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central - Azure Active Directory classique 


Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec LearnUpon, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique LearnUpon lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de LearnUpon à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-learnupon-from-the-gallery"></a>Ajout de LearnUpon à partir de la galerie
Pour configurer l’intégration des LearnUpon dans Azure AD, vous devez ajouter LearnUpon à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter LearnUpon à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **LearnUpon**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)

7. Dans le volet résultats, sélectionnez **LearnUpon**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec LearnUpon basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans LearnUpon à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans LearnUpon doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans LearnUpon.

Pour configurer et tester Azure AD SSO avec LearnUpon, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un LearnUpon tester l’utilisateur](#creating-a-learnupon-test-user)** - d’avoir un homologue de Britta Simon dans LearnUpon est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application LearnUpon.



**Pour configurer Azure AD SSO avec LearnUpon, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **LearnUpon** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à LearnUpon** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :.

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 


    un. Dans la zone de texte **URL de réponse** , tapez l’URL du Service consommateur Assertion selon le modèle suivant :`https://\<companyname\>.learnupon.com/saml/consumer`

    b. Cliquez sur **suivant**. 


4. Dans la page **configuration de l’authentification unique en LearnUpon** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) 

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. Nous devons ce certificat et l’URL de métadonnées (ID de l’entité, l’authentification unique URL de connexion et se déconnecter URL) pour configurer l’authentification unique sur LearnUpon côté.

    b. Cliquez sur **suivant**.




1. Ouvrir une autre instance de navigateur et connectez-vous à LearnUpon avec un compte d’administrateur. 

1. Cliquez sur l’onglet **paramètres** .

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 


1. Cliquez sur **Session unique - SAML**, puis cliquez sur **Paramètres généraux** pour configurer des paramètres SAML.

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 


5. Dans la section **Paramètres généraux** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) 

    un. Sélectionnez **activé**.

    b. En tant que **Version**, sélectionnez **2.0**.

    c. En tant que **conditions sauter**, sélectionnez **non**.

    d. Dans la zone de texte **nom du paramètre publier jeton SAML** , type le nom du paramètre de publication de demande à l’URL de consommateur SAML indiqué ci-dessus qui contient l’Assertion SAML à être vérifié et authentifiés - par exemple **SAMLResponse**.

    e. Dans la zone de texte **Format identificateur de nom** , tapez la valeur qui indique l’endroit où votre Assertion SAML l’identificateur utilisateurs (adresse de messagerie) réside - par exemple **urn : oasis : noms : tc : SAML:1.1:nameid-format : emailAddress**.

    f. Dans la zone **Emplacement du fournisseur identifier** , tapez la valeur qui indique où les utilisateurs sont envoyés à si ils cliquent sur votre icône téléchargé à partir de votre écran de connexion au portail classique Azure.

    g.in le portail classique Azure, copiez l' **URL du Service Sign-Out unique**et collez-le dans la textbos **se déconnecter URL** .

    h. Cliquez sur **Gérer doigt des photos**, puis téléchargez l’impression doigt de votre certificat téléchargé. 


1. Cliquez sur **Paramètres de l’utilisateur**, puis puis effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) 

    un. Dans la zone de texte **Prénom identificateur de Format** , tapez la valeur qui indique l’endroit où votre Assertion SAML prénom utilisateurs réside - par exemple : **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ prénom**.

    b. Dans la zone de texte **Dernier Format identificateur nom** , tapez la valeur qui indique l’endroit où votre Assertion SAML le nom des utilisateurs réside - par exemple : **nom de famille http://schemas.xmlsoap.org/ws/2005/05/identity/claims/**.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Authentification Azure AD unique][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-learnupon-test-user"></a>Création d’un utilisateur de test LearnUpon

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans LearnUpon. LearnUpon prend en charge à temps de mise en service, qui est par défaut activé.

Il n’existe aucun élément d’action pour vous dans cette section. Un nouvel utilisateur est créé au cours d’une tentative d’accès LearnUpon s’il n’existe pas encore. [Configuration d’authentification Azure AD unique](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support LearnUpon.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux LearnUpon.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à LearnUpon, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **LearnUpon**.

    ![Configurer l’authentification unique](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette LearnUpon dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application LearnUpon.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png
