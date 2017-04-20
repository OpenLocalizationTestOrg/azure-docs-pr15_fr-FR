<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Intralinks | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Intralinks."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Didacticiel : Intégration d’Azure Active Directory avec Intralinks

Dans ce didacticiel, vous allez apprendre à intégrer Intralinks Azure Active Directory (AD Azure).

Intégration de Intralinks avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Intralinks
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Intralinks (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Intralinks, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Intralinks lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Intralinks à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-intralinks-from-the-gallery"></a>Ajout de Intralinks à partir de la galerie
Pour configurer l’intégration des Intralinks dans Azure AD, vous devez ajouter Intralinks à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Intralinks à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Intralinks**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)

7. Dans le volet résultats, sélectionnez **Intralinks**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique

Dans cette section, vous configurez et testez Azure AD SSO avec Intralinks basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans Intralinks est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Intralinks doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Intralinks.

Pour configurer et tester Azure AD SSO avec Intralinks, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Intralinks tester l’utilisateur](#creating-an-intralinks-test-user)** - d’avoir un homologue de Britta Simon dans Intralinks est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Intralinks.


**Pour configurer Azure AD SSO avec Intralinks, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Intralinks** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Intralinks** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :.

    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 

    un. Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Intralinks selon le modèle suivant : **https://\<nom de la société\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<Azure AD Client ID\>**.

    b. Cliquez sur **suivant**.
    
4. Dans la page **configuration de l’authentification unique en Intralinks** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configuré pour votre application, contactez l’équipe de support technique Intralinks et le métadonnées téléchargés joindre un fichier de messagerie.


6. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-intralinks-test-user"></a>Création d’un utilisateur de test Intralinks

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Intralinks. Contactez l’équipe de support Intralinks pour ajouter les utilisateurs de la plate-forme Intralinks.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Intralinks.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Intralinks, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Intralinks**.

    ![Configurer l’authentification unique](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]

### <a name="adding-intralinks-via-or-elite-application"></a>Ajout d’une application Intralinks VIA ou Elite
Intralinks utilise la même plate-forme se sur identité unique pour toutes les autres applications Intralinks à l’exclusion d’application Nexus offre. Afin que si vous envisagez d’utiliser une autre application Intralinks puis vous devez d’abord configurer la session unique pour une application Intralinks primaire à l’aide de la procédure décrite ci-dessus.

Une fois que vous pouvez suivre l’en dessous de la procédure d’ajout d’une autre application Intralinks dans votre client qui peut tirer parti de cette application principale pour l’authentification unique. 

> [AZURE.NOTE] Veuillez Notez que cette fonctionnalité est disponible uniquement aux clients de référence (SKU) Azure AD Premium et n’est pas disponible gratuitement ou clients de référence (SKU) simple.

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. À partir de la tabulation gauche, cliquez sur l’onglet **personnalisées**

    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)

7. Donnez un nom approprié à l’application, par exemple **Intralinks Elite** et cliquez sur Terminer bouton.

8. Cliquez sur bouton **Configurer l’authentification unique**

9. Sélectionnez l’option **Existantes session unique**

    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)

10. Obtenir le SP initialisé par l’authentification unique d’URL à partir de Intralinks de l’équipe de l’autre application Intralinks et tapez-le comme illustré ci-dessous. 

    ![Ajout d’une application Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)

    un. Dans la zone de texte se d’URL, tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Intralinks selon le modèle suivant : **https://\<Nom_entreprise\>.Intralinks.com/?PartnerIdpId= https://sts.windows.net/\<AzureADTenantID\> **


11. Cliquez sur **suivant**.

12. Attribuer l’application à l’utilisateur ou des groupes comme indiqué dans la section ** [affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)**


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Intralinks dans le panneau d’accès, vous devez obtenir automatiquement connecté-sur à votre application Intralinks.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png
