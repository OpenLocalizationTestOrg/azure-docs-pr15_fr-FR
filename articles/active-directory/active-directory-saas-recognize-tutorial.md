<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec reconnaître | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et reconnaître."
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Didacticiel : Intégration d’Azure Active Directory avec reconnaître

L’objectif de ce didacticiel consiste à vous montrer comment intégrer reconnaître Azure Active Directory (AD Azure).

Intégration de reconnaître avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à reconnaître
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to reconnaître (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec reconnaître, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique reconnaître lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de reconnaître à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-recognize-from-the-gallery"></a>Ajout de reconnaître à partir de la galerie
Pour configurer l’intégration de reconnaître dans Azure AD, vous devez ajouter reconnaître à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter reconnaître à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **reconnaître**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)

7. Dans le volet résultats, sélectionnez **reconnaître**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec reconnaître basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans reconnaître à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans reconnaître doit être établi.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans reconnaître.

Pour configurer et tester Azure AD SSO avec reconnaître, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un reconnaître tester l’utilisateur](#creating-a-recognize-test-user)** - d’avoir un homologue de Britta Simon dans reconnaître est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application reconnaître.

**Pour configurer Azure AD SSO avec reconnaître, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **reconnaître** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à reconnaître** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)

    un. Dans la zone de texte **URL de connexion** , tapez une URL à l’aide du modèle suivant : `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. Dans la zone de texte **identificateur** , tapez une URL en utilisant le modèle suivant : `https://recognizeapp.com/<your-domain>/saml/metadata`.

    c. Cliquez sur **suivant**

    > [AZURE.NOTE] Si vous ne savez pas sur ces URL, tapez l’URL qu’exemple avec modèle d’exemple. Pour obtenir ces valeurs, vous pouvez pour obtenir plus d’informations, voir étape 9 ou contacter l’équipe de support reconnaître via <mailto:support@recognizeapp.com>.

4. Dans la page **configuration de l’authentification unique en reconnaître** , cliquez sur **Télécharger le certificat** , puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)

5. Dans une fenêtre de navigateur web différente, authentification à votre client reconnaître en tant qu’administrateur.

6. Dans le coin supérieur droit, cliquez sur **Menu**. Accédez à **administrateur de société**.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

7. Dans le volet de navigation gauche, cliquez sur **paramètres**.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

8. Effectuez les opérations suivantes dans la section **Paramètres de l’authentification unique** .

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)

    un. **Activer l’authentification unique**, sélectionnez **activé**.

    b. Dans l' **ID de l’entité IDP** zone de texte entrer la valeur de **l’URL de l’émetteur** à partir de l’Assistant configuration d’application Azure AD.

    c. Dans **l’url cible de l’authentification unique** zone de texte entrer la valeur de **Unique authentification URL du Service** à partir de l’Assistant configuration d’application Azure AD.

    d. Dans l' **url cible ralenti** zone de texte entrer la valeur de **l’URL du Service Sign-Out unique** à partir de l’Assistant configuration d’application Azure AD.

    e. Ouvrez votre fichier de certificat téléchargé dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **certificat** . 

    f. Cliquez sur le bouton **Enregistrer les paramètres** . 

9. En regard de la section **Paramètres de l’authentification unique** , copiez l’URL sous **url de métadonnées de fournisseur de Service**.
    
    ![Configurer côté Single authentification sur App](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

10. Ouvrez le **lien de l’URL de métadonnées** sous un navigateur vide pour télécharger le document de métadonnées. Puis utilisez la valeur de EntityDescriptor que reconnaître vous fournies pour **identificateur de** la boîte de dialogue **Configurer les paramètres de l’application** .

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

11. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

12. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , tapez **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-recognize-test-user"></a>Création d’un utilisateur de test reconnaître

Afin de permettre aux utilisateurs Azure AD pour vous connecter à reconnaître, ils doivent être configurées dans reconnaître. Dans le cas de reconnaître, mise en service est une tâche manuelle.

Cette application ne prend pas en charge SCIM mise en service, mais elle a une synchronisation autre utilisateur qui met les utilisateurs. 

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise reconnaître en tant qu’administrateur.

2.  Dans le coin supérieur droit, cliquez sur **Menu**. Accédez à **administrateur de société**.

3.  Dans le volet de navigation gauche, cliquez sur **paramètres**.

4.  Effectuez les opérations suivantes dans la section **Synchronisation de l’utilisateur** .
    
    ![Nouvel utilisateur] (./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Nouvel utilisateur")

    un. En tant **La synchronisation activée**, sélectionnez **activé**.

    b. En tant que **fournisseur de synchronisation de choisir**, sélectionnez **Microsoft / Office 365**.

    c. Cliquez sur **exécuter la synchronisation de l’utilisateur**

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à reconnaître.
    
![Affecter utilisateur][200]

**Pour attribuer Britta Simon à reconnaître, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **reconnaître**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Affecter utilisateur][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette reconnaître dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application reconnaître.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png
