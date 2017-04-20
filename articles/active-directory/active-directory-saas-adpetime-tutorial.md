<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ADP eTime | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et eTime ADP."
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
    ms.date="10/17/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Didacticiel : Intégration d’Azure Active Directory avec eTime ADP

L’objectif de ce didacticiel consiste à vous montrer comment intégrer ADP eTime Azure Active Directory (AD Azure).  
Intégration eTime ADP avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à eTime ADP
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to eTime ADP (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure


Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec eTime ADP, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une projet de données Access eTime authentification unique lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’eTime ADP à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-adp-etime-from-the-gallery"></a>Ajout d’eTime ADP à partir de la galerie
Pour configurer l’intégration des ADP eTime dans Azure AD, vous devez ajouter eTime ADP à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter eTime projet de données Access à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **eTime ADP**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)

7. Dans le volet résultats, sélectionnez **eTime ADP**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec eTime ADP basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans eTime ADP à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans eTime ADP doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans eTime ADP.

Pour configurer et tester Azure AD SSO avec eTime ADP, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un projet de données Access eTime tester l’utilisateur](#creating-a-adpetime-test-user)** - d’avoir un homologue de Britta Simon dans eTime ADP qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application eTime ADP.

Votre application eTime ADP attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages attribut personnalisé à votre configuration attributs jeton SAML. La capture d’écran suivante montre un exemple de ce. Le nom de réclamer sera toujours **« PersonImmutableID »** et la valeur dont nous avons mappées ExtensionAttribute2 qui contient EmployeeID de l’utilisateur. Ici le plan de mappage utilisateur Azure AD pour ADP eTime est effectué sur EmployeeID, mais vous pouvez mapper à une valeur différente également en fonction de vos paramètres d’application. Veuillez travail avec l’équipe d’eTime ADP tout d’abord à utiliser l’identificateur correct d’un utilisateur et mapper cette valeur avec la demande **« PersonImmutableID »** .  

![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

Avant de pouvoir configurer l’assertion SAML, vous devez contacter le support technique de ADP eTime et demandez à la valeur de l’attribut identificateur unique pour votre client. Vous avez besoin de cette valeur pour configurer la déclaration personnalisée pour votre application.


**Pour configurer Azure AD SSO avec eTime ADP, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **ADP eTime** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à eTime ADP** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 


    un. Dans la zone de texte **URL de réponse** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application eTime ADP selon le modèle suivant : `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique en eTime ADP** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configuré pour votre application, contactez votre équipe de support eTime ADP et envoyez le fichier de métadonnées joindre téléchargé, afin qu’elles peuvent être configurées pour l’intégration de l’authentification unique.

    > [AZURE.NOTE] Une fois que l’équipe **ADP eTime** configurer l’instance, obtenez la valeur **RelayState** d’eux. Suivez la ci-dessous mentionnées étapes pour le configurer. Après cette configuration, vous pouvez tester l’intégration. Donc Veuillez noter qu’il s’agit d’une configuration importantes pour cette intégration d’application pour l’utiliser.

6. Pour configurer la valeur RelayState dans Azure AD, effectuez les opérations suivantes : 
    
    un. Connectez-vous au [Portail de gestion Azure](https://portal.azure.com) en tant qu’administrateur.

    b. Dans le volet de navigation gauche, cliquez sur **Autres Services**. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)

    c. Dans la zone de texte **Rechercher** , tapez **Azure Active Directory**, puis cliquez sur le lien associé.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)

    d. Cliquez sur **Applications d’entreprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)

    e. Dans la section **Gérer** , cliquez sur **Toutes les Applications**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)

    f. Dans la zone de texte **Rechercher** , tapez **eTime ADP**, puis cliquez sur le lien associé. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)

    g. Dans la section **Gérer** , cliquez sur **authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)

    h. Sélectionnez **Afficher les URL paramètres avancés**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
    
    J’ai. Dans la zone de texte **État du relais** , tapez une valeur à l’aide de modèles suivants :
    
    - Environnement de production :`https://fed.adp.com/saml/fedlanding.html?<id>` 
    - Environnement intermédiaire :`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)

    j. Enregistrer les paramètres.

7. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

8. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  
Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 

    un. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la zone de texte **Nom d’utilisateur** , tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-adp-etime-test-user"></a>Création d’un utilisateur de test eTime ADP

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans eTime ADP. Contactez l’équipe de support ADP eTime afin d’ajouter des utilisateurs dans le compte eTime ADP. 


> [AZURE.NOTE]Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support eTime ADP.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser par l’octroi d’accéder au ADP eTime Azure de l’authentification unique.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à eTime ADP, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **eTime ADP**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette eTime ADP dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application eTime ADP.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png
