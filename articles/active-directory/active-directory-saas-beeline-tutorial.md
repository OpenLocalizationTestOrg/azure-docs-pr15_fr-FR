<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Beeline | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Beeline."
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
    ms.date="09/19/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Didacticiel : Intégration d’Azure Active Directory avec Beeline

Dans ce didacticiel, vous allez apprendre à intégrer Beeline Azure Active Directory (AD Azure).

Intégration de Beeline avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Beeline
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Beeline (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Beeline, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Beeline lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test. Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Beeline à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-beeline-from-the-gallery"></a>Ajout de Beeline à partir de la galerie
Pour configurer l’intégration des Beeline dans Azure AD, vous devez ajouter Beeline à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Beeline à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Beeline**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_01.png)

7. Dans le volet résultats, sélectionnez **Beeline**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec Beeline basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans Beeline est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Beeline doit être établie.
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Beeline.

Pour configurer et tester Azure AD SSO avec Beeline, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Beeline tester l’utilisateur](#creating-an-beeline-test-user)** - d’avoir un homologue de Britta Simon dans Beeline est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Beeline.

Votre application Beeline attend les assertions SAML dans un format spécifique. Contactez équipe Beeline tout d’abord à identifier l’identificateur utilisateur correctes qui est mappée dans l’application. Prenez également les instructions de l’équipe Beeline à propos de l’attribut qui qu’ils souhaitent utiliser pour ce mappage. Microsoft recommande à utiliser l’attribut **« NameIdentifier »** comme identificateur de l’utilisateur. Vous pouvez gérer la valeur de cet attribut à partir de l’onglet **« Atrribute »** de l’application. La capture d’écran suivante montre un exemple de ce. Ici, nous avons mappés la réclamation nameidentifier avec l’attribut **userprincipalname** , qui fournit des ID d’utilisateur unique, qui sera envoyé à l’application Beeline dans chaque réponse SAML réussie.

![Configurer l’authentification unique](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_07.png) 


**Pour configurer Azure AD SSO avec Beeline, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Beeline** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

     ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Beeline** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :.

    ![Configurer l’authentification unique](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_04.png) 


    un. Dans la zone de texte **identificateur** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Beeline selon le modèle suivant :`https://projects.beeline.net/<instance name>`

    b. Dans l’URL de réponse, tapez l’URL dans le modèle suivant : `https://projects.beeline.net/<instance name>/SSO_External.ashx` ou`https://projects.beeline.net/<company name>/SSO_External.ashx`


4. Dans la page **configuration de l’authentification unique en Beeline** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_05.png) 

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5.  Pour obtenir l’authentification unique configuré pour votre application, équipe de support technique Beeline contacts et ils aideront à configurer l’authentification unique. Veuillez noter que vous devez envoyer des messages électroniques et joindre le fichier de métadonnées téléchargé et également fournir les ID de l’entité et l’authentification unique des URL du Service.
  
6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-beeline-test-user"></a>Création d’un utilisateur de test Beeline

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Beeline. Application beeline besoin de tous les utilisateurs à mettre en service dans l’application avant de procéder à l’authentification. Veuillez travail avec le client Beeline prise en charge associer pour la mise en service de tous ces utilisateurs dans l’application. 


> [AZURE.NOTE] Si vous avez besoin créer un utilisateur manuellement ou par lots d’utilisateurs, vous devez contacter l’équipe de support Beeline.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Beeline.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Beeline, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Beeline**.

    ![Configurer l’authentification unique](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
Lorsque vous cliquez sur la vignette Beeline dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application Beeline.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_205.png
