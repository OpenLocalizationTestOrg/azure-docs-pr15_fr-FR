<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec IBM Kenexa enquête entreprise | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et IBM Kenexa enquête Enterprise."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec IBM Kenexa enquête Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer IBM Kenexa enquête entreprise Azure Active Directory (AD Azure).

Intégration d’IBM Kenexa enquête Enterprise avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à IBM Kenexa enquête entreprise
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to IBM Kenexa enquête entreprise (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec IBM Kenexa enquête entreprise, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique IBM Kenexa enquête entreprise lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test. Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’IBM Kenexa enquête entreprise à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Ajout d’IBM Kenexa enquête entreprise à partir de la galerie
Pour configurer l’intégration de IBM Kenexa enquête entreprise dans Azure AD, vous devez ajouter IBM Kenexa enquête entreprise à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter IBM Kenexa enquête entreprise à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **IBM Kenexa enquête entreprise**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_01.png)

7. Dans le volet résultats, sélectionnez **IBM Kenexa enquête Enterprise**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec IBM Kenexa enquête entreprise basée sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui l’utilisateur homologue dans IBM Kenexa enquête entreprise est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans IBM Kenexa enquête entreprise doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans IBM Kenexa enquête Enterprise.

Pour configurer et tester Azure AD SSO avec IBM Kenexa enquête entreprise, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’une entreprise IBM Kenexa enquête tester l’utilisateur](#creating-an-kenexasurvey-test-user)** - d’avoir un homologue de Britta Simon dans IBM Kenexa enquête entreprise qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
6. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application d’IBM Kenexa enquête entreprise.


**Pour configurer Azure AD SSO avec IBM Kenexa enquête Enterprise, procédez comme suit :**

1. Dans le portail classique, dans la page de l’intégration d’application **IBM Kenexa enquête entreprise** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à IBM Kenexa enquête entreprise** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_03.png)

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_04.png)

    un. Dans la zone de texte **identificateur** , tapez une URL en utilisant le modèle suivant :`https://surveys.kenexa.com/<company code>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant le modèle suivant :`https://surveys.kenexa.com/<company code>/tools/sso.asp`

    c. Cliquez sur **suivant**.

    > [AZURE.NOTE] Veuillez noter que celles-ci ne sont pas les valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur et l’URL de réponse réel. Contactez l’équipe de support technique IBM Kenexa enquête entreprise pour obtenir ces valeurs.

4. Dans la page **configuration de l’authentification unique en IBM Kenexa enquête entreprise** , cliquez sur **Télécharger le certificat** , puis enregistrez le fichier sur votre ordinateur :

    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_05.png) 

5. Pour obtenir l’authentification unique configuré pour votre application, contactez l’équipe de support technique IBM Kenexa et fournissez avec les éléments suivants :

    • Le fichier de certificat téléchargé

    • L' **URL de l’émetteur**

    • L' **URL de l’authentification unique SAML**

    • L' **URL du Service déconnexion unique**

    > [AZURE.NOTE] Veuillez Remarque que NameID revendiquer valeur dans la réponse doit correspondre avec l’ID de l’authentification unique configuré dans Kenexa système. Veuillez travail avec Kenexa prend en charge d’équipe pour mapper l’identificateur utilisateur appropriés dans votre organisation comme ID de l’authentification unique. Par défaut Azure AD définira la NameIdentifier en tant que valeur UPN. Vous pouvez le modifier à partir de l’onglet attribut comme le montre la capture d’écran ci-dessous. L’intégration ne fonctionnera que le mappage adéquat à la fin. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_51.png)

6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
  
    ![Authentification Azure AD unique][11]

8. Dans le portail classique Azure, dans la page de l’intégration d’application **IBM Kenexa enquête entreprise** , dans le menu de la partie supérieure, cliquez sur **attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_06.png)

9. Dans la boîte de dialogue **attributs jetons SAML** , effectuez les opérations suivantes :

    un. Sélectionnez l’attribut de **NameIdentifier** , puis cliquez sur **Modifier** .

    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_07.png)
    
    b. Dans la liste **Valeur de l’attribut** , tapez la valeur de l’attribut d’ID de l’authentification unique qui est configuré dans le système Kenexa.
    
    c. Cliquez sur **terminé**

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-ibm-kenexa-survey-enterprise-test-user"></a>Création d’un utilisateur de test IBM Kenexa enquête entreprise

Dans cette section, vous créez un utilisateur appelé Britta Simon dans IBM Kenexa enquête Enterprise. Contactez l’équipe de support IBM Kenexa pour mapper l’ID de l’authentification unique pour tous les utilisateurs. Également cette valeur d’ID de l’authentification unique doit être mappée à la valeur NameIdentifier à partir d’Azure AD. Vous pouvez modifier ce paramètre par défaut dans l’onglet attribut.


> [AZURE.NOTE] Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support IBM Kenexa enquête entreprise.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon utiliser Azure SSO par l’octroi d’accès à IBM Kenexa enquête entreprise.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à IBM Kenexa enquête entreprise, procédez comme suit :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **IBM Kenexa enquête Enterprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette IBM Kenexa enquête entreprise dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application d’IBM Kenexa enquête entreprise.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_205.png
