<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lifesize Cloud | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Lifesize Cloud."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Didacticiel : Intégration d’Azure Active Directory avec Lifesize Cloud

Dans ce didacticiel, vous découvrez comment intégrer Lifesize Cloud Azure Active Directory (AD Azure).

Intégration de Lifesize Cloud avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès au Lifesize Cloud
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Lifesize Cloud (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Lifesize Cloud, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Lifesize Cloud lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Lifesize Cloud à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Ajout de Lifesize Cloud à partir de la galerie
Pour configurer l’intégration des Lifesize Cloud dans Azure AD, vous devez ajouter Lifesize Cloud à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Lifesize Cloud à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Lifesize Cloud**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. Dans le volet résultats, sélectionnez **Lifesize Cloud**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et test Azure AD SSO avec Lifesize Cloud basé sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui l’utilisateur homologue dans le nuage Lifesize est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Lifesize Cloud doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans le Lifesize Cloud.

Pour configurer et tester Azure AD SSO avec Lifesize Cloud, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un nuage Lifesize tester l’utilisateur](#creating-a-lifesize-cloud-test-user)** - d’avoir un homologue de Britta Simon dans le nuage Lifesize qui est lié à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Lifesize Cloud.


**Pour configurer Azure AD SSO avec Lifesize Cloud, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Lifesize Cloud** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de vous connecter au Lifesize Cloud** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Lifesize Cloud à l’aide du modèle suivant : **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique en nuage Lifesize** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configuré pour votre application, connexion dans l’application Lifesize Cloud avec privilèges d’administrateur.

6. Dans le coin supérieur droit, cliquez sur votre nom et puis cliquez sur les **Paramètres avancés**

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. Dans la paramètres avancés maintenant, cliquez sur le lien **Configuration de l’authentification unique** . La page Configuration de l’authentification unique pour votre instance s’ouvre.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Configurer maintenant les valeurs suivantes dans l’interface utilisateur de configuration de l’authentification unique.    

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copiez la valeur de l’URL de l’émetteur à partir d’Azure AD et coller le texte sélectionné dans la zone de texte **Émetteur de fournisseur d’identité** .

    • Copiez la valeur de l’URL de connexion à distance à partir d’Azure AD et coller le texte sélectionné dans la zone de texte **URL de connexion** .

    • Ouvrez le certificat téléchargé dans le bloc-notes et copiez le contenu du certificat, en excluant des lignes certificat de début et fin, le coller dans la zone de texte **Certificat X.509** .

    • Dans le mappage des attributs SAML pour la zone de texte **prénom** entrer la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • Dans le mappage des attributs SAML pour la zone de texte **Nom** entrer la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • Dans le mappage des attributs SAML pour la zone de texte **messagerie** entrer la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Pour vérifier la configuration, vous pouvez cliquer sur le bouton **Test** .

    > [AZURE.NOTE] Pour tester succès, vous devez exécuter l’Assistant configuration dans Azure AD permettent également d’accéder à des utilisateurs ou groupes qui peuvent effectuer le test.
    
10. Activer l’authentification unique en cochant sur le bouton **Activer l’authentification unique** .

11. Maintenant, cliquez sur le bouton de **mise à jour** afin que tous les paramètres soient enregistrées. Cela génère la valeur RelayState. Copiez la valeur RelayState qui est générée dans la zone de texte. Nous devons cette valeur dans les étapes suivantes.

12. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

13. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]

14. Ouverture de session maintenant dans le portail de gestion Azure **https://portal.azure.com** utilisant les informations d’identification d’administration

15. Cliquez sur lien **Autres Services** dans le volet de navigation gauche
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Recherche d’Azure Active Directory et cliquez sur le lien **Azure Active Directory**
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Vous trouverez toutes vos applications SaaS sous le bouton **d’Applications d’entreprise** .

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Cliquez ensuite sur le lien de **Toutes les Applications** dans la carte suivante
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Recherche de Lifesize application pour laquelle vous souhaitez configurer le RelayState. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Cliquez sur lien **de l’authentification unique** dans la carte

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Vous verrez la case à cocher **Afficher les URL paramètres avancés** . Cliquez sur la case à cocher.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Maintenant configurer le RelayState pour l’application que vous voyez dans la page de configuration de l’authentification unique Lifesize application. 

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Enregistrer les paramètres.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Création d’un utilisateur de test Lifesize Cloud

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Lifesize Cloud. Nuage Lifesize ne prend pas en charge la mise en service automatique de l’utilisateur. Après une authentification réussie en Azure AD, l’utilisateur sera automatiquement déployé dans l’application. 


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en l’octroi d’accès aux Lifesize Cloud.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Lifesize Cloud, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Lifesize Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Lifesize Cloud dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Lifesize Cloud.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
