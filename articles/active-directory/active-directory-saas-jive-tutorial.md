<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jive | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jive."
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


# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Didacticiel : Intégration d’Azure Active Directory avec Jive

Dans ce didacticiel, vous allez apprendre à intégrer Jive Azure Active Directory (AD Azure).

Intégration de Jive avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Jive
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Jive (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Jive, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Jive lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Jive à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-jive-from-the-gallery"></a>Ajout de Jive à partir de la galerie
Pour configurer l’intégration des Jive dans Azure Active Directory, vous devez ajouter Jive à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Jive à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Jive**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. Dans le volet résultats, sélectionnez **Jive**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec Jive basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans Jive est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Jive doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Jive.

Pour configurer et tester Azure AD SSO avec Jive, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un Jive tester l’utilisateur](#creating-a-jive-test-user)** - d’avoir un homologue de Britta Simon dans Jive est liée à la représentation Azure AD de lui.
4. **[Configuration de mise en service de l’utilisateur](#configuring-user-provisioning)** - hiérarchique comment activer la configuration des utilisateurs d’utilisateur Active Directory de comptes à Jive.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
6. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Jive.

**Pour configurer Azure AD SSO avec Jive, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Jive** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Jive** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 

    un. Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Jive selon le modèle suivant : **https://\<nom du client\>. jivecustom.com**.
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique en Jive** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Authentification à votre client Jive en tant qu’administrateur.

6. Dans le menu dans la partie supérieure, cliquez sur «**Saml**».

    ![Configurer l’authentification unique sur le côté de l’application](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    un. Sous l’onglet **Genaral** , sélectionnez **activé** .

    b. Cliquez sur le bouton «**Enregistrer tous les paramètres saml**».

7. Accédez à l’onglet «**Métadonnées Idp**».

    ![Configurer l’authentification unique sur le côté de l’application](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

    un. Copiez le contenu du fichier XML métadonnées téléchargés et collez-le dans la zone de texte **métadonnées fournisseur d’identité (IDP)** .

    b. Cliquez sur le bouton «**Enregistrer tous les paramètres saml**». 

8. Accédez à l’onglet «**Mappage d’attribut utilisateur**».

    ![Configurer l’authentification unique sur le côté de l’application](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

    un. Dans la zone de texte de **messagerie** , copiez et collez le nom de l’attribut de valeur de **courrier** .

    b. Dans la zone de texte **prénom** , copiez et collez le nom de l’attribut de valeur **prénom** .

    c. Dans la zone de texte **Nom** , copiez et collez le nom de l’attribut de valeur de **nom de famille** .
    
9. Dans le portail Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
![Authentification Azure AD unique][10]

10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
  ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



###<a name="creating-a-jive-test-user"></a>Création d’un utilisateur de test Jive

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Jive. Contactez l’équipe de support Jive pour ajouter les utilisateurs de la plate-forme Jive.


###<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
L’objectif de cette section est hiérarchique l’activation d’utilisateur mise en service des comptes d’utilisateurs Active Directory Jive.  
Dans le cadre de cette procédure, vous êtes obligé de fournir un jeton de sécurité utilisateur que vous devez demander à partir de Jive.com.
  
La capture d’écran suivante montre un exemple de la boîte de dialogue connexe dans Azure Active Directory :

![Configurer la configuration des utilisateurs] (./media/active-directory-saas-jive-tutorial/IC698794.png "Configurer la configuration des utilisateurs")

####<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans le portail de gestion Azure, dans la page de l’intégration **Jive** application, cliquez sur la **Configuration des utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

2.  Dans la page **Entrez vos informations d’identification Jive pour activer la mise en service automatique de l’utilisateur** , fournissent des paramètres de configuration suivants :

    1.  Dans la zone de texte **Nom d’utilisateur Jive** , tapez un nom de compte Jive qui contient le profil **Administrateur système** dans Jive.com affectées.

    2.  Dans la zone de texte **Mot de passe administrateur Jive** , tapez le mot de passe pour ce compte.

    3.  Dans la zone de texte **Jive client URL** , tapez l’URL de client Jive.

        >[AZURE.NOTE]L’URL de client Jive est utilisé par votre organisation pour vous connecter à Jive.  
        En règle générale, l’URL a le format suivant : **www.\< organisation\>. jive.com**.

    4.  Cliquez sur **Valider** pour vérifier votre configuration.

    5.  Cliquez sur le bouton **suivant** pour ouvrir la page de **Confirmation** .

3.  Dans la page de **Confirmation** , cliquez sur la coche pour enregistrer votre configuration.
  
Vous pouvez maintenant créer un compte de test, attendez 10 minutes et vérifiez que le compte a été synchronisé avec Jive.com.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Jive.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Jive, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Jive**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Jive dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application Jive.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png
