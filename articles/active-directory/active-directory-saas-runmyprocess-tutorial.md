<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec RunMyProcess | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et RunMyProcess."
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
    ms.date="10/21/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Didacticiel : Intégration d’Azure Active Directory avec RunMyProcess

L’objectif de ce didacticiel consiste à vous montrer comment intégrer RunMyProcess Azure Active Directory (AD Azure).

Intégration de RunMyProcess avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à RunMyProcess
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to RunMyProcess (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec RunMyProcess, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique RunMyProcess lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de RunMyProcess à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-runmyprocess-from-the-gallery"></a>Ajout de RunMyProcess à partir de la galerie
Pour configurer l’intégration des RunMyProcess dans Azure AD, vous devez ajouter RunMyProcess à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter RunMyProcess à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **RunMyProcess**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)

7. Dans le volet résultats, sélectionnez **RunMyProcess**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec RunMyProcess basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD il faut savoir ce que l’utilisateur homologue dans RunMyProcess est à un utilisateur dans Active Directory Azure est. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans RunMyProcess doit être établi.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans RunMyProcess.

Pour configurer et tester Azure AD SSO avec RunMyProcess, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un RunMyProcess tester l’utilisateur](#creating-a-runmyprocess-test-user)** - d’avoir un homologue de Britta Simon dans RunMyProcess est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application RunMyProcess.

**Pour configurer Azure AD SSO avec RunMyProcess, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **RunMyProcess** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à RunMyProcess** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 

    un. Dans la zone de texte **URL de connexion** , tapez une URL à l’aide du modèle suivant : `https://live.runmyprocess.com/live/<tenant id>`.
        
    b. Cliquez sur **suivant**

    > [AZURE.NOTE] Veuillez noter que vous devez mettre à jour la valeur de l’URL se réelle. Pour accéder à cette valeur, contacter l’équipe de support RunMyProcess via <mailto:support@runmyprocess.com>.
 
4. Dans la page **configuration de l’authentification unique en RunMyProcess** , cliquez sur **Télécharger le certificat** et enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)

5. Dans une fenêtre de navigateur web différente, authentification à votre client RunMyProcess en tant qu’administrateur.

6. Dans le volet de navigation gauche, cliquez sur **le compte** et sélectionnez **Configuration**.

    ![Configurer l’authentification unique sur le côté de l’application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

7. Accédez à la section **méthode d’authentification** et suivez les étapes ci-dessous :

    ![Configurer l’authentification unique sur le côté de l’application](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    un. En tant que **méthode**, sélectionnez **l’authentification unique avec Samlv2**.

    b. Dans l' **authentification unique rediriger** zone de texte entrer la valeur de **l’URL de l’authentification unique SAML** à partir de l’Assistant configuration d’application Azure AD.

    c. Dans la **déconnexion rediriger** zone de texte entrer la valeur de **l’URL du Service Sign-Out unique** à partir de l’Assistant configuration d’application Azure AD.

    d. Dans le **Format de nom de l’Id** de zone de texte placer la valeur de **Nom identificateur Format** à partir de l’Assistant configuration d’application Azure AD.

    e. Copiez le contenu du fichier de certificat téléchargé et collez-le dans la zone de texte **certificat** . 

    f. Cliquez sur **Enregistrer** une icône.
    
8. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

9. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

### <a name="creating-a-runmyprocess-test-user"></a>Création d’un utilisateur de test RunMyProcess
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à RunMyProcess, ils doivent être configurées dans RunMyProcess. Dans le cas RunMyProcess, mise en service est une tâche manuelle.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise RunMyProcess en tant qu’administrateur.

2.  Cliquez sur **le compte** et sélectionnez des **utilisateurs** dans le volet de navigation gauche, puis cliquez sur **Nouvel utilisateur**.

    ![Nouvel utilisateur] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nouvel utilisateur")

3.  Dans la section **Paramètres de l’utilisateur** , procédez comme suit :

    ![Profil] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")

    un. Tapez le **nom** et la **messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.

    b. Sélectionnez une **langue IDE**, une **langue** et un **profil**.

    c. Sélectionnez **Envoyer un message électronique de la création de compte pour moi**.

    d. Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres RunMyProcess utilisateur compte outils de création ou API fournies par RunMyProcess mise en service Azure Active Directory aux comptes d’utilisateurs.
    

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux RunMyProcess.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à RunMyProcess, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **RunMyProcess**.

    ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. barre dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette RunMyProcess dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application RunMyProcess.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png
