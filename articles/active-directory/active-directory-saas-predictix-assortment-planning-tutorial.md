<properties
    pageTitle="Didacticiel : Azure Active Directory intégrant planification d’assortiment Predictix | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de la planification d’assortiment Predictix."
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
    ms.date="10/14/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Didacticiel : Azure Active Directory intégrant Predictix assortiment planification

Dans ce didacticiel, vous découvrez comment intégrer planification d’assortiment Predictix Azure Active Directory (AD Azure).

Intégration de la planification d’assortiment Predictix avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à la planification d’assortiment Predictix
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté sur Planification d’assortiment Predictix (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec planification d’assortiment Predictix, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une planification d’assortiment Predictix connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Microsoft Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de planification d’assortiment Predictix à partir de la galerie
2. Configuration et test de Microsoft Azure AD l’authentification unique


## <a name="adding-predictix-assortment-planning-from-the-gallery"></a>Ajout de planification d’assortiment Predictix à partir de la galerie
Pour configurer l’intégration de la planification d’assortiment Predictix dans Azure AD, vous devez ajouter Predictix assortiment planification à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter la planification d’assortiment Predictix à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Predictix assortiment planification**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)

7. Dans le volet résultats, sélectionnez **Planification d’assortiment Predictix**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configuration et test de Microsoft Azure AD l’authentification unique
Dans cette section, vous configurez et testez Microsoft Azure AD SSO avec planification d’assortiment Predictix basée sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui l’utilisateur homologue lors de la planification d’assortiment Predictix est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans planification d’assortiment Predictix doit être établi.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans la planification d’assortiment Predictix.

Pour configurer et tester Microsoft Azure AD SSO avec planification d’assortiment Predictix, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Microsoft Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - pour tester Microsoft Azure AD SSO avec Britta Simon.
3. **[Création d’un utilisateur test planification d’assortiment Predictix](#creating-a-predictix-price-reporting-test-user)** - d’avoir un homologue de Britta Simon dans planification d’assortiment Predictix lié à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon à utiliser Microsoft Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuration de Microsoft Azure AD unique authentification

Dans cette section, vous activez Microsoft Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application de planification d’assortiment Predictix.


**Pour configurer Microsoft Azure AD SSO avec planification d’assortiment Predictix, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page **Planification d’assortiment Predictix** de l’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la planification d’assortiment Predictix** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 

    un. Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application de planification d’assortiment Predictix selon le modèle suivant : **https://\<société tarifs nom\>.ap.predictix.com/sso/request**.
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique à la planification d’assortiment Predictix** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configuré pour votre application, contacter l’équipe de support Predictix assortiment planification et fournissez avec les éléments suivants :

    • Le certificat téléchargé

    • L' **ID de l’entité**

    • L' **URL de l’authentification unique SAML**

    • Le **seul se déconnecter URL du Service**

6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-predictix-assortment-planning-test-user"></a>Création d’un utilisateur test Predictix assortiment planification

Dans cette section, vous créez un utilisateur appelé Britta Simon Predictix assortiment planification. Contactez l’équipe de support planification d’assortiment Predictix afin d’ajouter des utilisateurs dans la plateforme Predictix assortiment planification.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser par l’octroi d’accès à la planification d’assortiment Predictix Azure de l’authentification unique.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à planification d’assortiment Predictix, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Planification d’assortiment Predictix**.

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Microsoft Azure AD SSO configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Predictix assortiment planification dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Predictix assortiment planification.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png
