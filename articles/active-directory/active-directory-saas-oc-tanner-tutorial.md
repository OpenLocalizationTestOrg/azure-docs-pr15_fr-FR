<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec O.C. Duprez - AppreciateHub | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et O.C. Duprez - AppreciateHub."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Didacticiel : Intégration d’Azure Active Directory avec O.C. Duprez - AppreciateHub

L’objectif de ce didacticiel consiste à vous montrer comment intégrer O.C. Duprez - AppreciateHub avec Azure Active Directory (AD Azure).  
Intégration O.C. Duprez - AppreciateHub avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à O.C. Duprez - AppreciateHub 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to O.C. Duprez - AppreciateHub (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec O.C. Duprez - AppreciateHub, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- UN O.C. Duprez - authentification unique AppreciateHub lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout de O.C. Duprez - AppreciateHub à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Ajout de O.C. Duprez - AppreciateHub à partir de la galerie
Pour configurer l’intégration de O.C. Duprez - AppreciateHub dans Azure Active Directory, vous devez ajouter O.C. Duprez - AppreciateHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter O.C. Duprez - AppreciateHub à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2] 

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3] 

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4] 

6. Dans la zone Rechercher, tapez **Duprez O.C. - AppreciateHub**.

    ![Applications][5] 

7. Dans le volet résultats, sélectionnez **Duprez O.C. - AppreciateHub**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][25] 




##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique

L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec O.C. Duprez - AppreciateHub basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD a besoin de savoir ce que l’utilisateur homologue dans O.C. Duprez - AppreciateHub à un utilisateur dans Active Directory Azure est. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans O.C. Duprez - AppreciateHub doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans O.C. Duprez - AppreciateHub.
 
Pour configurer et tester Azure AD SSO avec O.C. Duprez - AppreciateHub, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Duprez O.C. - utilisateur test AppreciateHub](#creating-a-halogen-software-test-user)** - d’avoir un homologue de Britta Simon dans O.C. Duprez - AppreciateHub est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre O.C. Duprez - AppreciateHub application.


**Pour configurer Azure AD SSO avec Duprez O.C. - AppreciateHub, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **O.C. Duprez - AppreciateHub** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6]

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à O.C. Duprez - AppreciateHub** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][7]

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application][8]
 
     un. Ouvrez le fichier de métadonnées en utilisant le lien suivant : [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Recherchez le nœud **md:AssertionConsumerService** . 

     c. Copiez la valeur de l’attribut **emplacement** . 

     ![Configurer les paramètres de l’application][12]
     
     d. Dans la zone de texte **URL de connexion** , au-delà de la valeur que vous avez obtenu à l’étape précédente.

     > [AZURE.NOTE] Si vous êtes expiriencing problèmes obtention de l’URL de réponse à partir du fichier de métadonnées, contactez le O.C. Duprez - équipe de support technique AppreciateHub via [sso@octanner.com](mailto:sso@octanner.com).

     e. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique en Duprez O.C. - AppreciateHub** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Qu’est Azure AD Connect][9]

5. Contactez le O.C. Duprez - équipe de support technique AppreciateHub via xyz, lui fournir le fichier de métadonnées, et les informer qu’ils doivent activer l’authentification unique pour vous.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Qu’est Azure AD Connect][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.  

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
  
    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Création d’un O.C. Duprez - AppreciateHub test utilisateur

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans O.C. Duprez - AppreciateHub.

**Pour créer un utilisateur appelé Britta Simon dans Duprez O.C. - AppreciateHub, effectuez les opérations suivantes :**

1. Demandez à votre équipe de support technique Duprez OC pour créer un utilisateur avec la même valeur que le nom d’utilisateur de Britta Simon l’attribut nameID dans Azure AD.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser par l’octroi d’accès aux O.C. Azure de l’authentification unique Duprez - AppreciateHub.

![Affecter utilisateur][200]

**Pour affecter Britta Simon à Duprez O.C. - AppreciateHub, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **Duprez O.C. - AppreciateHub**.

    ![Affecter utilisateur][202]

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur le O.C. Duprez - AppreciateHub vignette dans le panneau d’accès, vous devez obtenir automatiquement connecté sur votre O.C. Duprez - AppreciateHub application.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png






