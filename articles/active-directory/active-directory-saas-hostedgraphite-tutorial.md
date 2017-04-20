<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Graphite hébergé | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Graphite hébergé."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Didacticiel : Intégration d’Azure Active Directory avec Graphite hébergé

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Graphite hébergé Azure Active Directory (AD Azure).

Intégration de Graphite hébergé avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Graphite hébergé
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Graphite hébergé (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Graphite hébergé, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Graphite hébergé sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Graphite hébergé à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-hosted-graphite-from-the-gallery"></a>Ajout de Graphite hébergé à partir de la galerie
Pour configurer l’intégration de Graphite hébergé dans Azure AD, vous devez ajouter Graphite hébergé à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Graphite hébergé à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Graphite hébergé**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)

7. Dans le volet résultats, sélectionnez **Graphite hébergé**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Graphite hébergé basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Graphite hébergé à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Graphite hébergé doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Graphite hébergé.

Pour configurer et tester Azure AD SSO avec Graphite hébergé, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un Graphite hébergé tester l’utilisateur](#creating-a-hosted-graphite-test-user)** - d’avoir un homologue de Britta Simon dans Graphite hébergé est liée à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Graphite hébergé.

**Pour configurer Azure AD SSO avec Graphite hébergé, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Graphite hébergé** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Graphite hébergé** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , si vous souhaitez configurer l’application en **mode initialisé par l’IDP**, effectuez les opérations suivantes et cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)

    un. Dans la zone de texte **identificateur** , tapez une URL en utilisant le modèle suivant :`https://www.hostedgraphite.com/metadata/<user id>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant le modèle suivant :`https://www.hostedgraphite.com/complete/saml/<user id>`

    c. Cliquez sur **suivant**

4. Si vous souhaitez configurer l’application en **SP initié mode** dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , puis cliquez sur **« Afficher les paramètres (facultatifs) avancés »** et puis entrez l' **URL de connexion** et cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)

    un. Dans la zone de texte **URL de connexion** , tapez une URL à l’aide du modèle suivant :`https://www.hostedgraphite.com/login/saml/<user id>/`

    b. Cliquez sur **suivant**

    > [AZURE.NOTE] Veuillez noter que celles-ci ne sont pas les valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, identificateur et URL de réponse réel. Pour obtenir ces valeurs, vous pouvez accéder à Access -> configuration SAML située à votre application ou contactez Graphite hébergé.

5. Dans la page **configuration de l’authentification unique en Graphite hébergé** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

6. Authentification à votre client Graphite hébergé en tant qu’administrateur.

7. Accédez à la **page Configuration de SAML** dans la barre latérale (**Access -> configuration SAML**).

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

8. Confirmez ces URL correspondant à votre configuration à l’étape 3.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

9. Copiez **l’émetteur URL** et **L’authentification unique SAML** Azure AD pour **entité ou ID de l’émetteur** et l' **URL de connexion de l’authentification unique** dans Graphite hébergé.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)

9. Sélectionnez «**en lecture seule**» comme **rôle utilisateur par défaut**.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

10. Copiez le contenu du fichier de certificat téléchargé et collez-le dans la zone de texte **Certificat X.509** .

     ![Configurer côté Single authentification sur App](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

11. Cliquez sur le bouton **Enregistrer** .

12. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

13. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-hosted-graphite-test-user"></a>Création d’un utilisateur de test Graphite hébergé

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans Graphite hébergé. Graphite hébergé prend en charge à temps de mise en service, qui est par défaut activé.

Il n’existe aucun élément d’action pour vous dans cette section. Un nouvel utilisateur est créé au cours d’une tentative d’accès Graphite hébergé s’il n’existe pas encore.

> [AZURE.NOTE] Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support Graphite hébergé via <mailto:help@hostedgraphite.com>.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à Graphite hébergé.
    
![Affecter utilisateur][200]

**Pour attribuer Britta Simon à Graphite hébergé, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **Graphite hébergé**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Affecter utilisateur][203]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette Graphite hébergé dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Graphite hébergé.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png
