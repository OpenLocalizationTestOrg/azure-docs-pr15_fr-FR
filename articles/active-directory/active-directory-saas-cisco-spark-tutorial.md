<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Cisco explosion | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco explosion."
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


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Didacticiel : Intégration d’Azure Active Directory avec Cisco explosion

Dans ce didacticiel, vous découvrez comment intégrer Cisco explosion Azure Active Directory (AD Azure).

Intégration de Cisco explosion avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Cisco explosion
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to explosion Cisco (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Cisco explosion, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique **Cisco explosion** lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test. Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’explosion Cisco à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-cisco-spark-from-the-gallery"></a>Ajout d’explosion Cisco à partir de la galerie
Pour configurer l’intégration de Cisco explosion dans Azure AD, vous devez ajouter Cisco explosion dans la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco explosion dans la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Explosion Cisco**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. Dans le volet résultats, sélectionnez **Explosion Cisco**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec explosion Cisco basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui l’utilisateur homologue dans Cisco explosion est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Cisco explosion doit être établie.
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Cisco explosion. Pour configurer et tester Azure AD SSO avec Cisco explosion, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’une explosion Cisco tester l’utilisateur](#creating-a-cisco-spark-test-user)** - d’avoir un homologue de Britta Simon dans explosion Cisco qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Cisco explosion.

Cisco explosion application attend les assertions SAML contenant des attributs spécifiques. Configurez les attributs suivants de cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **« Atrributes »** de l’application. La capture d’écran suivante montre un exemple de ce.

![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Pour configurer Azure AD SSO avec Cisco explosion, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration d’application **Cisco explosion** , dans le menu de la partie supérieure, cliquez sur **attributs**.
     
    ![Configurer l’authentification unique][5]


2. Dans la boîte de dialogue **attributs jetons SAML** , effectuez les opérations suivantes :

    un. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter utilisateur Attribure** .

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , tapez **uid**.
    
    c. Dans la liste **Valeur de l’attribut** , sélectionnez **user.userprincipal**.
    
    d. Cliquez sur **terminé**. Ensuite, **Appliquer les modifications** dans la partie inférieure de la page.

3. Dans le menu dans la partie supérieure, cliquez sur **Quick Start**.

    ![Configurer l’authentification unique][6]

4. Dans le portail classique, dans la page de l’intégration **À explosion Cisco** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7] 

5. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Cisco explosion** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    un. Dans la zone de texte URL de connexion, tapez une URL à l’aide du modèle suivant : `https://web.ciscospark.com/#/signin`.

    b. Cliquez sur **suivant**.


7. Dans la page **configuration de l’authentification unique en explosion Cisco** , cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Connectez-vous à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) avec vos informations d’identification d’administrateur complet.
9. Sélectionnez **paramètres** , sous la section **authentification** , cliquez sur **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Sélectionnez **intégrer un fournisseur d’identité 3ème tiers. (Avancées)** et accédez à l’écran suivant.
11. Cliquez sur **Télécharger un fichier de métadonnées** et enregistrer le fichier sur votre ordinateur.
12. Dans la page **Importer les métadonnées Idp** , faites glisser et déposez le fichier de métadonnées Azure AD sur la page ou utilisez l’option de navigateur de fichiers pour localiser et téléchargez le fichier de métadonnées Azure AD. Ensuite, sélectionnez **nécessitent un certificat signé par une autorité de certification dans les métadonnées (plus sûre)** et cliquez sur **suivant**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Sélectionnez **Tester la connexion SSO**et quand un nouvel onglet de navigateur s’ouvre, vous authentifier avec Azure AD en vous connectant.
14. Revenir à l’onglet **Gestion de Collaboration de Cloud Cisco** de navigateur. Si le test a réussi, sélectionnez **ce test a réussi. Activer l’option d’authentification unique** et cliquez sur **suivant**.

7. Dans le portail classique Azure AD, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

8. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-cisco-spark-test-user"></a>Création d’un utilisateur de test Cisco explosion

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Cisco explosion. Dans cette section, vous créez un utilisateur appelé Britta Simon dans Cisco explosion.

1. Accédez à la [Gestion de Collaboration Cloud Cisco](https://admin.ciscospark.com/) avec vos informations d’identification d’administrateur complet.
2. Cliquez sur **utilisateurs** , puis sur **Gérer les utilisateurs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Dans la fenêtre **Gérer les utilisateurs** , sélectionnez **manuellement ajouter ou modifier des utilisateurs** , puis cliquez sur **suivant**.
4. Sélectionnez **noms et adresse de messagerie**. Ensuite, remplissez la zone de texte que vous suivez :

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**

    b. Dans la zone de texte **Nom** , tapez **Simon**

    c. Dans la zone de texte **adresse de messagerie** , tapez**britta.simon@contoso.com**

5. Cliquez sur le signe plus pour ajouter Britta Simon. Ensuite, cliquez sur **suivant**.
6. Dans la fenêtre **Ajouter des Services pour les utilisateurs** , cliquez sur **Enregistrer** , puis sur **Terminer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à Cisco explosion.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Cisco explosion, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Explosion Cisco**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Cisco explosion dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Cisco explosion.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
