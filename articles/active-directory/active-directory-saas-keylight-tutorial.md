<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Keylight | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Keylight."
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


# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Didacticiel : Intégration d’Azure Active Directory avec Keylight

Dans ce didacticiel, vous allez apprendre à intégrer Keylight Azure Active Directory (AD Azure).

Intégration Keylight avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Keylight
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Keylight (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Keylight, vous devez les éléments suivants :

- Un abonnement Azure
- Une authentification unique Keylight lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test. 

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Keylight à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-keylight-from-the-gallery"></a>Ajout de Keylight à partir de la galerie
Pour configurer l’intégration des Keylight dans Azure AD, vous devez ajouter Keylight à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Keylight à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Keylight**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. Dans le volet résultats, sélectionnez **Keylight**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec Keylight basée sur un utilisateur de test appelé « Brian Simon ».

Pour configurer et tester Azure AD SSO avec Keylight, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Keylight tester l’utilisateur](#creating-a-keylight-test-user)** - d’avoir un homologue de Britta Simon dans Keylight est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

Dans cette section, vous activez Azure AD SSO dans le portail classique Azure et configurez l’authentification unique dans votre application Keylight.


**Pour configurer Azure AD SSO avec Keylight, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **Keylight** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 


2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Keylight** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :
 
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 


    un. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Keylight selon le modèle suivant : **« https://\<nom de la société\>.keylightgrc.com/Login.aspx?saml=1 »**.


4. Dans la page **configuration de l’authentification unique en Keylight** , effectuez les opérations suivantes :
 
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour activer l’authentification unique dans Keylight, effectuez les opérations suivantes :
 
    un. Authentification à votre compte Keylight en tant qu’administrateur.

    b. Dans le menu dans la partie supérieure, cliquez sur **une personne**, puis sélectionnez **Keylight le programme d’installation**.
       
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Dans l’arborescence à gauche, cliquez sur **SAML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Dans la boîte de dialogue **Paramètres SAML** , cliquez sur **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/404.png) 
  

5. Dans la page de la boîte de dialogue **Modifier les paramètres de SAML** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/405.png) 

    un. Définissez **l’authentification SAML** **actif**.


    b. Portail classique Azure AD, copiez la valeur de **l’URL de l’authentification unique SAML** et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .

    c. Portail classique Azure AD, copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL déconnexion du fournisseur d’identité** .

    d. Cliquez sur **Choisir un fichier** pour sélectionner votre certificat Keylight téléchargé, puis cliquez sur **Ouvrir** pour télécharger le certificat.


    e. Affecter **Id d’utilisateur SAML emplacement** **NameIdentifier élément de la déclaration de l’objet**.
   
    f. Fournir la **fournisseur de services Keylight selon le modèle suivant : **https://&lt;nom de la société&gt;. keylightgrc.com**.

    g. Définir **les utilisateurs de configuration automatique** à l’état **actif**.

    h. Définir le **type de compte automatique disposition** sur **Utilisateur complet**.

    J’ai. En tant que le **rôle de sécurité configuration automatique**, sélectionnez **Utilisateur Standard avec SAML**.
   
    j. En tant que **configuration de la sécurité configuration automatique**, sélectionnez **Configuration utilisateur Standard**.
   
    k. Dans la zone de texte attribut de messagerie, tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Dans la zone de texte **prénom attribut** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Dans la zone de texte **dernier attribut de nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Cliquez sur **Enregistrer**.
   
  
   
  
6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Authentification Azure AD unique][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique Azure appelé Britta Simon.

Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]



**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 


2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 


4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-keylight-test-user"></a>Création d’un utilisateur de test Keylight

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Keylight. Keylight prend en charge à temps de mise en service, qui est activé par défaut.

Il n’existe aucun élément d’action pour vous dans cette section. Un utilisateur est créé lors de l’accès Keylight si l’utilisateur n’existe pas encore. 

> [AZURE.NOTE] Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support Keylight.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser par l’octroi d’accès aux Keylight Azure de l’authentification unique.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Keylight, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Keylight**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Keylight dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Keylight.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png
