<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory 23 vidéo | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et 23 vidéo."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Didacticiel : Intégration d’Azure Active Directory 23 vidéo

L’objectif de ce didacticiel consiste à vous montrer comment intégrer vidéo 23 Azure Active Directory (AD Azure).  
Intégration 23 vidéo avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à 23 vidéo 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to vidéo 23 (SSO) avec leurs comptes Azure AD

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec 23 vidéo, vous devez les éléments suivants :

- Un abonnement Azure AD
- Un 23 authentification unique vidéo lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de vidéos 23 à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-23-video-from-the-gallery"></a>Ajout de vidéos 23 à partir de la galerie
Pour configurer l’intégration de vidéo 23 dans Azure AD, vous devez ajouter la vidéo 23 à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter la vidéo 23 à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **23 vidéo**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **23 vidéo**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][25]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec 23 vidéo basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans 23 vidéo à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans 23 vidéo doit être établi.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans 23 vidéo.
 
Pour configurer et tester Azure AD SSO avec 23 vidéo, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’une vidéo 23 tester l’utilisateur](#creating-a-23-video-test-user)** - d’avoir un homologue de Britta Simon dans 23 vidéo qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application vidéo 23.

**Pour configurer Azure AD SSO avec 23 vidéo, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **23 vidéo** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à signer à 23 vidéo** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][7] 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Authentification Azure AD unique][8] 
 
     un. Dans la zone de texte **URL de réponse** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre site de vidéo 23 (par exemple : *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] Intégration d’Active Directory à l’aide de SAML 2.0 est disponible pour tous les utilisateurs vidéo 23. Veuillez contacter le Support en [support@23company.com](mailto:support@23company.com) si vous avez besoin des métadonnées associées.

     b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique à 23 vidéo** , effectuez les opérations suivantes :

    ![Authentification Azure AD unique][9] 

    un. Cliquez sur Télécharger le certificat, puis enregistrez le fichier sur votre ordinateur.

    b. Contactez votre équipe de support vidéo 23 via [support@23company.com](mailto:support@23company.com), lui fournir le certificat téléchargé, l' **URL de l’émetteur**, l' **URL du Service unique authentification**, l' **URL Sign-Out unique**et demandez-lui de configurer l’authentification unique pour votre application vidéo 23. 

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

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) 
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-23-video-test-user"></a>Création d’un utilisateur de test vidéo 23

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon 23 vidéo.

**Pour créer un utilisateur appelé Britta Simon 23 vidéo, effectuez les opérations suivantes :**

1. Connectez-vous à votre site d’entreprise vidéo 23 en tant qu’administrateur.

1. Accédez à **paramètres**.


2. Dans la section **utilisateurs** , cliquez sur **configurer**.

    ![Affecter utilisateur][400]

3. Cliquez sur **Ajouter un nouvel utilisateur**. 

    ![Affecter utilisateur][401]

4. Dans la section **Inviter quelqu'un à participer à ce site** , effectuez les opérations suivantes :

    ![Affecter utilisateur][402]

    un. Dans la zone **adresses de messagerie** , tapez l’adresse de messagerie de Britta Simon dans Azure AD.

    b. Cliquez sur **Ajouter l’utilisateur**.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à 23 vidéo.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à 23 vidéo, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **vidéo 23**.

    ![Affecter utilisateur][202] 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette de vidéo 23 dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application vidéo 23.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png




