<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec un logiciel de halogène"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et halogène logiciel."
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


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Didacticiel : Intégration d’Azure Active Directory avec un logiciel de halogène

L’objectif de ce didacticiel consiste à vous montrer comment intégrer halogène logiciel Azure Active Directory (AD Azure).

Intégration du logiciel halogène avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui ont accès au logiciel halogène 
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir connecté au logiciel halogène (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec halogène logiciel, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique halogène logiciel lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test. 

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’un logiciel halogène à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-halogen-software-from-the-gallery"></a>Ajout d’un logiciel halogène à partir de la galerie
Pour configurer l’intégration du logiciel halogène dans Azure Active Directory, vous devez ajouter halogène logiciel à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter halogène logiciel à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page. 

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **halogène software**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Halogène logiciel**, puis cliquez sur **terminé** pour ajouter l’application.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec un logiciel halogène basé sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue halogène logiciel à un utilisateur dans Active Directory Azure. En d’autres termes, une relation entre des liens entre un utilisateur Azure AD et l’utilisateur connexe halogène logiciel doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans le logiciel halogène.
 
Pour configurer et tester Azure AD SSO avec un logiciel de HALOGÈNE, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD unique SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un logiciel halogène tester l’utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un homologue de Britta Simon dans le logiciel halogène qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuration Azure AD unique de l’authentification unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application logicielle halogène.


**Pour configurer Azure AD SSO avec halogène logiciel, procédez comme suit :**

1. Dans le portail classique Azure, dans la page de l’intégration **Halogène logiciel** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][8]

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à halogène logiciel** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][9]

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :  ![configurer les paramètres de l’application][10]
 
     un. dans la zone **URL de connexion** , entrez votre URL utilisée par vos utilisateurs pour se connecter à votre application halogène logiciel en utilisant le modèle suivant : *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique au logiciel halogène** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
    
    ![Qu’est Azure AD Connect][11]

5. Dans une fenêtre de navigateur autre authentification à votre application **Halogène logiciel** en tant qu’administrateur.

6. Cliquez sur l’onglet **Options** . 

    ![Qu’est Azure AD Connect][12]


7. Dans le volet de navigation gauche, cliquez sur **Configuration SAML**. 

    ![Qu’est Azure AD Connect][13]

8. Dans la page **Configuration de SAML** , effectuez les opérations suivantes :  ![Nouveautés Azure AD Connect][14]

    un. En tant qu' **Identificateur Unique**, sélectionnez **NameID**.

    b. Sous forme **Cartes d’identificateur Unique à**, sélectionnez le **nom d’utilisateur**.

    c. Pour télécharger votre fichier de métadonnées téléchargé, cliquez sur **Parcourir** pour sélectionner le fichier, puis sur **Télécharger un fichier**.

    d. Pour tester la configuration, cliquez sur **Exécuter le Test**. 

    > [AZURE.NOTE] Vous devez attendre que le message «*la SAML test est terminé. Fermez cette fenêtre*». Ensuite, fermez la fenêtre du navigateur ouvert. La case à cocher **Activer SAML** est activée uniquement si le test est terminé.

    e. Sélectionnez **Activer SAML**.
    
    f. Cliquez sur **Enregistrer les modifications**. 


9. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** . 

    ![Qu’est Azure AD Connect][15]

10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Qu’est Azure AD Connect][100] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Qu’est Azure AD Connect][101] 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Qu’est Azure AD Connect][102] 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Qu’est Azure AD Connect][103] 
 
    un. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur Suivant.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Qu’est Azure AD Connect][104] 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans le **Nom de famille** txtbox, type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Qu’est Azure AD Connect][105]  

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Qu’est Azure AD Connect][106]   

    un. Notez la valeur de **Nouveau mot de passe**.
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Création d’un utilisateur de test logiciel halogène

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon halogène logiciel.

**Pour créer un utilisateur appelé Britta Simon halogène logiciel, procédez comme suit :**

1. Connectez-vous à votre application **Halogène logiciel** en tant qu’administrateur.

2. Cliquez sur l’onglet **Centre de l’utilisateur** , puis cliquez sur **Créer un utilisateur**.

    ![Qu’est Azure AD Connect][300]  

3. Dans la page de la boîte de dialogue **Nouvel utilisateur** , procédez comme suit :

    ![Qu’est Azure AD Connect][301]

    un. Dans la zone de texte **nom** , tapez **Brian**. 
  
    b. Dans la zone de texte **Nom** , tapez **Simon**.
  
    c. Dans la zone de texte **nom d’utilisateur** , tapez le **nom d’utilisateur d’Emmanuel Brita dans le portail classique Azure**.
  
    d. Dans la zone de texte **mot de passe** , tapez un mot de passe pour Brian.
  
    e. Cliquez sur **Enregistrer**.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser par l’octroi d’accès au logiciel halogène Azure de l’authentification unique.

![Qu’est Azure AD Connect][200]

**Pour attribuer Britta Simon à halogène logiciel, procédez comme suit :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Qu’est Azure AD Connect][201]

2. Dans la liste des applications, sélectionnez **Halogène logiciel**.

    ![Qu’est Azure AD Connect][202]

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Qu’est Azure AD Connect][203]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

    ![Qu’est Azure AD Connect][204]

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Qu’est Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette du logiciel halogène dans le panneau d’accès, vous devez obtenir automatiquement connecté sur votre application logicielle halogène.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png