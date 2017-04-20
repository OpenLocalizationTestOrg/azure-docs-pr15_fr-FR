<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Pluralsight | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pluralsight."
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


# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Didacticiel : Intégration d’Azure Active Directory avec Pluralsight

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Pluralsight Azure Active Directory (AD Azure).

Intégration de Pluralsight avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Pluralsight
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Pluralsight (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure


Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Pluralsight, vous devez les éléments suivants :

- Un abonnement Azure
- Une authentification unique Pluralsight lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test. 

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Pluralsight à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-pluralsight-from-the-gallery"></a>Ajout de Pluralsight à partir de la galerie
Pour configurer l’intégration de Pluralsight dans Azure AD, vous devez ajouter Pluralsight à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Pluralsight à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
 
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Pluralsight**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)

7. Dans le volet résultats, sélectionnez **Pluralsight**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Pluralsight basée sur un utilisateur de test appelé « Brian Simon ».

Pour configurer et tester Azure AD SSO avec Pluralsight, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’un Pluralsight tester l’utilisateur](#creating-a-pluralsight-test-user)** - d’avoir un homologue de Britta Simon dans Pluralsight est liée à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Pluralsight.

Votre application Pluralsight attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages attribut personnalisé à votre configuration attributs jeton SAML. La capture d’écran suivante montre un exemple de ce. 

![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

Vous pouvez également ajouter l’attribut **« ID Unique »** avec la valeur appropriée comme EmployeeID ou toute autre chose qui vous convient pour votre organisation. Notez également qu’il s’agit pas l’attribut requis ; Toutefois, vous pouvez l’ajouter pour identifier l’utilisateur unique. 

**Pour configurer Azure AD SSO avec Pluralsight, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **Pluralsight** , dans le menu dans la partie supérieure, cliquez sur **attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 



1. Pour supprimer les **attributs jetons SAML**redondantes, effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/2829.png) 


    un. Pour chaque attribut utilisateur dans la zone rouge du tableau ci-dessus, pointez sur l’attribut, puis cliquez sur Supprimer. 




1. Pour ajouter les requis **attributs jetons SAML**, pour chaque ligne indiqué dans le tableau ci-dessous, effectuez les opérations suivantes :

  	| Nom de l’attribut | Valeur de l’attribut |
  	| --- | --- |    
  	| Prénom| User.GivenName |
  	| Nom de famille  | User.Surname |
  	| Messagerie | User.Mail |

    un. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter utilisateur Attribure** .

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png) 


    b. Dans la zone de texte **Nom affectent** , tapez le nom de l’attribut affiché pour cette ligne.

    c. Dans la liste **Valeur de l’attribut** , selsect la valeur de l’attribut affichée pour cette ligne.

    d. Cliquez sur **terminé**.  
    


1. Cliquez sur **appliquer les modifications**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/3232.png)  



1. Dans le menu dans la partie supérieure, cliquez sur **Quick Start**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  









1. Dans le portail classique Azure, dans la page de l’intégration **Pluralsight** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Pluralsight** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 


    un. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Pluralsight selon le modèle suivant :`https://<instance name>.pluralsight.com/sso/<comapny name>`

    b. Cliquez sur **suivant**.


4. Dans la page **configuration de l’authentification unique en Pluralsight** , effectuez les opérations suivantes :  ![configurer de l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png) 

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configuré pour votre application, contactez l’équipe Pluralsight [Services professionnels](mailTo:professionalservices@pluralsight.com) et fournissez le fichier de métadonnées téléchargé.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
  
    ![Authentification Azure AD unique][10]

7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-pluralsight-test-user"></a>Création d’un utilisateur de test Pluralsight

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans Pluralsight. Contactez l’équipe de support Pluralsight afin d’ajouter des utilisateurs dans le compte Pluralsight. 


> [AZURE.NOTE]Si vous avez besoin créer manuellement un utilisateur, vous devez contacter l’équipe de support Pluralsight.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à Pluralsight.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Pluralsight, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Pluralsight**.

    ![Configurer l’authentification unique](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Pluralsight dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Pluralsight.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png
