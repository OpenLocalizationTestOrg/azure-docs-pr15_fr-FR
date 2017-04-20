<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Everbridge | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Everbridge."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Didacticiel : Intégration d’Azure Active Directory avec Everbridge

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Everbridge Azure Active Directory (AD Azure).

Intégration de Everbridge avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Everbridge
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Everbridge (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Everbridge, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Everbridge lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Everbridge à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-everbridge-from-the-gallery"></a>Ajout de Everbridge à partir de la galerie
Pour configurer l’intégration des Everbridge dans Azure AD, vous devez ajouter Everbridge à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Everbridge à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Everbridge**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_01.png)
7. Dans le volet résultats, sélectionnez **Everbridge**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Everbridge basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Everbridge à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Everbridge doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Everbridge.

Pour configurer et tester Azure AD SSO avec Everbridge, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un Everbridge tester l’utilisateur](#creating-a-everbridge-test-user)** - d’avoir un homologue de Britta Simon dans Everbridge est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Everbridge.

**Pour configurer Azure AD SSO avec Everbridge, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Everbridge** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Everbridge** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_04.png)

    un. Dans la zone de texte **identificateur** , tapez l’URL à l’aide du modèle suivant :`https://sso.everbridge.net/{<company name>}`

    b. Dans la zone de texte **URL de réponse** , tapez l’URL à l’aide du modèle suivant :`https://manager.everbridge.net/saml/SSO/{<company name>}/alias/defaultAlias`

    c. Cliquez sur **suivant**

4. Dans la page **configuration de l’authentification unique en Everbridge** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_05.png)

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Pour obtenir l’authentification unique configuré pour votre application, vous avez besoin pour vous connecter à votre client Everbridge en tant qu’administrateur.

6. Dans le menu dans la partie supérieure, cliquez sur l’onglet **paramètres** et sélectionnez **Authentification unique** sous **sécurité**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)

    un. Dans la zone de texte **nom** , tapez le nom du fournisseur identificateur (par exemple : nom de votre société).

    b. Dans la zone de texte **Nom de l’API** , tapez le nom de l’API.

    C. Cliquez sur **Choisir un fichier** pour télécharger le fichier de métadonnées que vous avez téléchargé à **l’étape 4**.

    d. En tant qu' **Emplacement d’identité SAML**, sélectionnez « Identité est dans l’élément NameIdentifier de l’instruction objet ».

    e. Copier l’URL de l’authentification unique SAML à partir d’Azure AD dans **URL Login du fournisseur d’identité** dans Everbridge.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)

    f. En tant que **Service fournisseur initialisé par demande de liaison**, sélectionnez redirection HTTP.

7. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

8. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

Dans la liste des utilisateurs, sélectionnez **Brian Simon**.
    
![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_05.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_06.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_07.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_08.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-everbridge-test-user"></a>Création d’un utilisateur de test Everbridge

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Everbridge. Contactez équipe de support technique Everbridge via <mailto:support@everbridge.com> pour ajouter les utilisateurs de la plate-forme Everbridge.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Everbridge.
    
![Affecter utilisateur][200]

**Pour attribuer Britta Simon à Everbridge, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **Everbridge**.

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_50.png)

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Everbridge dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application Everbridge.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_205.png
