<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec BetterWorks | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et BetterWorks."
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


# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Didacticiel : Intégration d’Azure Active Directory avec BetterWorks

L’objectif de ce didacticiel consiste à vous montrer comment intégrer BetterWorks Azure Active Directory (AD Azure).

Intégration de BetterWorks avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à BetterWorks
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to BetterWorks (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec BetterWorks, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique BetterWorks lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de BetterWorks à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-betterworks-from-the-gallery"></a>Ajout de BetterWorks à partir de la galerie
Pour configurer l’intégration des BetterWorks dans Azure AD, vous devez ajouter BetterWorks à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter BetterWorks à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **BetterWorks**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)

7. Dans le volet résultats, sélectionnez **BetterWorks**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec BetterWorks basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans BetterWorks à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans BetterWorks doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans BetterWorks.

Pour configurer et tester Azure AD SSO avec BetterWorks, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un BetterWorks tester l’utilisateur](#creating-a-betterworks-test-user)** - d’avoir un homologue de Britta Simon dans BetterWorks est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application BetterWorks.

Application BetterWorks attend les assertions SAML dans un format spécifique. Configurez les affirmations suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet «**Atrribute**» de l’application. La capture d’écran suivante montre un exemple de ce. 

![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Pour configurer Azure AD SSO avec BetterWorks, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **BetterWorks** , dans le menu dans la partie supérieure, cliquez sur **attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)

2. Dans la boîte de dialogue **attributs jetons SAML** , pour chaque ligne indiqué dans le tableau ci-dessous, effectuez les opérations suivantes :
    

  	| Nom de l’attribut | Valeur de l’attribut |
  	| --- | --- |    
  	| saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    un. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter utilisateur Attribure** .

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    
    c. Dans la liste **Valeur de l’attribut** , tapez l’ID de jeton saml affichée pour cette ligne.
    
    d. Cliquez sur **terminé**

3. Dans le menu dans la partie supérieure, cliquez sur **Quick Start**.

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 

4. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à BetterWorks** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)

5. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , si vous voulez configurer l’application en **mode initialisé par l’IDP**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)


    un. Dans la zone de texte **identificateur** , tapez l’URL dans le modèle suivant :`https://app.betterworks.com/saml2/metadata/`


    b. Dans la zone de texte **URL de réponse** , tapez l’URL dans le modèle suivant :`https://app.betterworks.com/saml2/acs/`


    c. Cliquez sur **suivant**

6. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , si vous voulez configurer l’application en **mode initialisé par le SP**, effectuer la sur les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)

    un.  Sélectionnez **Afficher les paramètres (facultatifs) avancés**.



    b. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application BetterWorks selon le modèle suivant :`https://app.betterworks.com`

    b. Cliquez sur **suivant**.

7. Dans la page **configuration de l’authentification unique en BetterWorks** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

8. Pour obtenir de l’authentification unique configuré pour votre application, contactez votre équipe de support technique BetterWorks via <mailto:support@betterworks.com>. Joindre le fichier de métadonnées téléchargé et partagez-le avec BetterWorks d’équipe pour configurer l’authentification unique sur leur côté.

9. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-betterworks-test-user"></a>Création d’un utilisateur de test BetterWorks

Dans cette section, vous créez un utilisateur appelé Britta Simon dans BetterWorks. 

Contactez l’équipe de support BetterWorks via <mailto:support@betterworks.com> pour ajouter les utilisateurs de la plate-forme BetterWorks.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux BetterWorks.
    
   ![Affecter utilisateur][200]

**Pour attribuer Britta Simon à BetterWorks, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **BetterWorks**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Affecter utilisateur][203]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette BetterWorks dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application BetterWorks.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png
