<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS) | Microsoft Azure"
    description="Découvrez comment utiliser Amazon Web Services (AWS) avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Service (AWS)

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Amazon Web Service (AWS) Azure Active Directory (AD Azure).  
Intégration d’Amazon Web Service (AWS) avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à Amazon Web Service (AWS) 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on à Amazon Web Service (AWS) (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec Amazon Web Service (AWS), vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Amazon Web Service (AWS) lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout d’Amazon Web Service (AWS) à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Ajout d’Amazon Web Service (AWS) à partir de la galerie
Pour configurer l’intégration d’Amazon Web Service (AWS) dans Azure AD, vous devez ajouter Amazon Web Service (AWS) à partir de la galerie à votre liste d’applications SaaS gérées.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Pour ajouter Amazon Web Service (AWS) à partir de la galerie, procédez comme suit :

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur. 
   
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page. 
   
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**. 
   
    ![Applications][4]

6. Dans la zone Rechercher, tapez **Amazon Web Service (AWS)**.
   
    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Amazon Web Service (AWS)**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Amazon Web Service (AWS) basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Amazon Web Service (AWS) à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Amazon Web Service (AWS) doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur du **nom d’utilisateur** dans Amazon Web Service (AWS).
 
Pour configurer et tester Azure AD SSO avec Amazon Web Service (AWS), vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD unique SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Service Web de Amazon (AWS) tester l’utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un homologue de Britta Simon dans Amazon Web Service (AWS) qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuration Azure AD unique de l’authentification unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Amazon Web Service (AWS).  
Votre application Amazon Web Service (AWS) attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** . La capture d’écran suivante montre un exemple de ce.


![Configurer l’authentification unique][27]

**Pour configurer Azure AD SSO avec Amazon Web Service (AWS), procédez comme suit :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **Amazon Web Service (AWS)** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7]

2. Dans la page **Comment souhaitez-vous aux utilisateurs d’ouverture de session à Amazon Web Service (AWS)** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique][8]

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , cliquez sur Suivant. 

    ![Configurer les paramètres de l’application][9]
 
4. Dans la page **configuration de l’authentification unique en Amazon Web Service (AWS)** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique][10]

5. Dans une fenêtre de navigateur différent, ouverture de session sur le site de votre société Amazon Web Service (AWS) en tant qu’administrateur.

6. Cliquez sur **Accueil de la Console**.

    ![Configurer l’authentification unique][11]

7. Cliquez sur **Gestion des identités et Access**. 

    ![Configurer l’authentification unique][12]

8. Cliquez sur **Fournisseurs d’identité**, puis cliquez sur **Créer un fournisseur**. 

    ![Configurer l’authentification unique][13]

9. Dans la page de la boîte de dialogue **Configurer un fournisseur** , procédez comme suit : 

    ![Configurer l’authentification unique][14]

     un. En tant que **Type de fournisseur**, sélectionnez **SAML**.

     b. Dans la zone de texte **Nom du fournisseur** , tapez un nom de fournisseur (par exemple : *bois non traités*).

     c. Pour télécharger votre fichier de métadonnées téléchargé, cliquez sur **Choisir un fichier**.

     d. Cliquez sur **l’étape suivante**.


10. Dans la page de dialogue **Vérifiez les informations de fournisseur** , cliquez sur **créer**. 

    ![Configurer l’authentification unique][15]

11. Cliquez sur **les rôles**, puis cliquez sur **Créer un nouveau rôle**. 

    ![Configurer l’authentification unique][16]

12. Dans la boîte de dialogue **Définir un nom de rôle** , procédez comme suit : 

    ![Configurer l’authentification unique][17]

    un. Dans la zone de texte **Nom de rôle** , tapez un nom de rôle (par exemple : *TestUser*).

    b. Cliquez sur **l’étape suivante**.

13. Dans la boîte de dialogue **Sélectionner un Type de rôle** , procédez comme suit : 

    ![Configurer l’authentification unique][18]

    un. Sélectionnez le **rôle pour l’accès de fournisseur d’identité**.

    b. Dans la section **accès Grant Web de l’authentification unique (WebSSO) pour les fournisseurs de SAML** , cliquez sur **Sélectionner**.


14. Dans la boîte de dialogue **Établir gestion de la confidentialité** , procédez comme suit :  

    ![Configurer l’authentification unique][19]
     
     un. En tant que fournisseur SAML, sélectionnez le fournisseur SAML que vous avez créé previousley (par exemple : *bois non traités*) 

     b. Cliquez sur **l’étape suivante**.


15. Dans la boîte de dialogue **Vérifier le rôle d’approbation** , cliquez sur **Étape suivante**. 

    ![Configurer l’authentification unique][32]


16. Dans la boîte de dialogue **Joindre la stratégie** , cliquez sur **Étape suivante**.  

    ![Configurer l’authentification unique][33]


17. Dans la boîte de dialogue **révision** , effectuez les opérations suivantes :   

    ![Configurer l’authentification unique][34]

     un. Copiez la valeur de **Rôle Infos** .

     b. Copiez la valeur Infos **Entités approuvés** .

     c. Cliquez sur **créer un rôle**. 

18. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Qu’est Azure AD Connect][20]

19. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer** pour fermer la boîte de dialogue **de configurer l’authentification unique** .

    ![Qu’est Azure AD Connect][22]


20. Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** . 

    ![Configurer l’authentification unique][21]

21. Cliquez sur **Ajouter un attribut de l’utilisateur**. 

    ![Configurer l’authentification unique][23]

22. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit. 

    ![Configurer l’authentification unique][24] 

    un. Dans la zone de texte **Nom de l’attribut** , tapez **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Dans la zone de texte **Valeur de l’attribut** , tapez **[la valeur rôle Infos], [valeur approuvés entité Infos]**.

    >[AZURE.TIP] Voici les valeurs que vous avez copiée à partir de la boîte de dialogue révision lorsque vous avez créé votre rôle. 

    c. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur** .

23. Cliquez sur **Ajouter un attribut de l’utilisateur**. 

    ![Configurer l’authentification unique][23]


24. Dans la boîte de dialogue Ajouter un attribut utilisateur, procédez comme suit. 

    ![Configurer l’authentification unique][25]


     un. Dans la zone de texte **Nom de l’attribut** , tapez **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Dans la zone de texte **Valeur de l’attribut** , tapez ou sélectionnez **user.userprincipalname** dans la liste déroulante.
     
    ![Configurer l’authentification unique][35]
    

     c. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur** .


25. Cliquez sur **appliquer les modifications**. 

    ![Configurer l’authentification unique][26]





### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.
  2. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans le **Nom de famille** txtbox, type, **Simon**.
  
    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
  
    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.
  
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Création d’un utilisateur de test Amazon Web Service (AWS)

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Pour créer un utilisateur appelé Britta Simon dans Amazon Web Service (AWS), procédez comme suit :

1. Connectez-vous au site de votre société **Amazon Web Service (AWS)** en tant qu’administrateur.

2. Cliquez sur l’icône **Accueil de la Console** . 

    ![Configurer l’authentification unique][11]

3. Cliquez sur identité et accéder à la gestion. 

    ![Configurer l’authentification unique][28]

4. Dans le tableau de bord, cliquez sur utilisateurs, puis cliquez sur créer de nouveaux utilisateurs. 

    ![Configurer l’authentification unique][29]

5. Dans la boîte de dialogue Créer un utilisateur, procédez comme suit : 

    ![Configurer l’authentification unique][30]

     un. Dans les zones de texte **Entrez les noms d’utilisateur** , tapez le nom d’utilisateur de Brita Simon (userprincipalname) Azure AD.

     b. Cliquez sur **créer**.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon utiliser Azure SSO par l’octroi d’accès à Amazon Web Service (AWS).

![Affecter utilisateur][31]

**Pour attribuer Britta Simon à CloudPassage, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][26]

2. Dans la liste des applications, sélectionnez **Amazon Web Service (AWS)**.

    ![Affecter utilisateur][27]

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][25]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][29]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette Amazon Web Service (AWS) dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application de Service de Web d’Amazon (AWS).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















