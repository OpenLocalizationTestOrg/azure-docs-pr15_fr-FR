<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ImageRelay | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et ImageRelay."
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


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Didacticiel : Intégration d’Azure Active Directory avec ImageRelay

L’objectif de ce didacticiel consiste à vous montrer comment intégrer ImageRelay Azure Active Directory (AD Azure).  
Intégration de ImageRelay avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à ImageRelay
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to ImageRelay (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec ImageRelay, vous devez les éléments suivants :

- Un abonnement Azure AD
- Un abonnement ImageRelay d’unique authentification activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de ImageRelay à partir de la galerie

2. Configuration et test Azure AD authentification unique


## <a name="adding-imagerelay-from-the-gallery"></a>Ajout de ImageRelay à partir de la galerie
Pour configurer l’intégration des ImageRelay dans Azure AD, vous devez ajouter ImageRelay à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ImageRelay à partir de la galerie, procédez comme suit :**

1. Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **ImageRelay**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. Dans le volet résultats, sélectionnez **ImageRelay**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec ImageRelay basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit un compte d’utilisateur qui représente l’utilisateur connexe dans ImageRelay.  En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans ImageRelay doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans ImageRelay.

Pour configurer et tester Azure AD SSO avec ImageRelay, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un ImageRelay tester l’utilisateur](#creating-a-userecho-test-user)** - d’avoir un homologue de Britta Simon dans ImageRelay est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application ImageRelay.


**Pour configurer Azure AD SSO avec ImageRelay, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **ImageRelay** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

     ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à ImageRelay** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

     ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application ImageRelay (par exemple : *https://fabrikam.ImageRelay.com/*).

    b. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique en ImageRelay** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise ImageRelay en tant qu’administrateur.

1. Dans la barre d’outils dans la partie supérieure, cliquez sur la charge de travail **utilisateurs et autorisations** .

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Cliquez sur **créer d’autorisation**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. Dans la charge de travail **Se paramètres unique** , activez la case à cocher de **ce groupe peuvent uniquement connexion via l’authentification unique** , puis cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Accédez à **paramètres du compte**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Accédez à la charge de travail **Se paramètres unique** .

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Dans la boîte de dialogue **Paramètres SAML** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    un. Dans le portail classique Azure, copiez l' **URL du Service unique authentification**et collez-le dans la zone de texte **URL de connexion** .


    b. Dans le portail classique Azure, copiez l' **URL du Service Sign-Out unique**et collez-le dans la zone de texte **URL de déconnexion** .

    c. En tant que **Format d’Id de nom**, sélectionnez **urn : oasis : noms : tc : SAML:1.1:nameid-format : emailAddress**.

    
    d. Sous **Options de liaison pour les requêtes à partir du fournisseur de Service (relais Image)**, sélectionnez **Liaison billet**.
   

    e. Sous **certificat x.509**, cliquez sur **Mettre à jour le certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Ouvrez le certificat téléchargé dans le bloc-notes, copiez le contenu et collez-le dans la zone de texte de certificat x.509.
  
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Dans la section **Just approvisionnement de l’utilisateur** , sélectionnez l' **Utilisateur Just activer la mise en service**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Sélectionnez le groupe d’autorisations (par exemple, **L’authentification unique base**) qui est autorisé à se connecter uniquement par le biais de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    J’ai. Cliquez sur **Enregistrer**.

6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.

    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-imagerelay-test-user"></a>Création d’un utilisateur de test ImageRelay

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans ImageRelay.

**Pour créer un utilisateur appelé Britta Simon dans ImageRelay, effectuez les opérations suivantes :**

1. Ouverture de session sur le site de votre entreprise ImageRelay en tant qu’administrateur.

1. Accédez à **utilisateurs et autorisations** , puis sélectionnez **Créer un utilisateur de l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Entrez le **courrier électronique**, **prénom**, **Nom** et **société** de l’utilisateur que vous voulez mettre en service et sélectionnez le groupe d’autorisations (par exemple, l’authentification unique de base) qui correspond au groupe de se connecter uniquement par le biais de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Cliquez sur **créer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux ImageRelay.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à ImageRelay, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **ImageRelay**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette ImageRelay dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application ImageRelay.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png
