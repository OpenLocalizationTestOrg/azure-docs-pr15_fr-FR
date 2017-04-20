<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SAP Business ByDesign | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Business ByDesign."
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
    ms.date="09/09/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Didacticiel : Intégration d’Azure Active Directory avec SAP Business ByDesign

Dans ce didacticiel, vous allez apprendre à intégrer SAP Business ByDesign Azure Active Directory (AD Azure).

Intégration de SAP Business ByDesign avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à SAP Business ByDesign
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on à SAP Business ByDesign (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec SAP Business ByDesign, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique SAP Business ByDesign lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de SAP Business ByDesign à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Ajout de SAP Business ByDesign à partir de la galerie
Pour configurer l’intégration de SAP Business ByDesign dans Azure AD, vous devez ajouter SAP Business ByDesign à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Business ByDesign à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.


3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **SAP Business ByDesign**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. Dans le volet résultats, sélectionnez **SAP Business ByDesign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec SAP Business ByDesign basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit savoir ce que l’utilisateur homologue dans SAP Business ByDesign est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans SAP Business ByDesign doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans SAP Business ByDesign.

Pour configurer et tester Azure AD SSO avec SAP Business ByDesign, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un ByDesign métier SAP tester l’utilisateur](#creating-an-sap-business-bydesign-test-user)** - d’avoir un homologue de Britta Simon dans SAP Business ByDesign lié à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application SAP Business ByDesign.

SAP Business ByDesign application attend les assertions SAML dans un format spécifique. Configurez les affirmations suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **« Atrribute »** de l’application. La capture d’écran suivante montre un exemple de ce. 


**Pour configurer Azure AD SSO avec SAP Business ByDesign, effectuez les opérations suivantes :**


1. Dans le portail classique Azure, dans la page de l’intégration d’application **SAP Business ByDesign** , dans le menu de la partie supérieure, cliquez sur **attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 


2. Dans la liste des attributs jeton SAML attributs, sélectionnez l’attribut nameidentifier, puis cliquez sur **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. Dans la boîte de dialogue Modifier l’attribut utilisateur, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 

    un. Dans la liste valeur de l’attribut, sélectionnez le fonction **ExtractMailPrefix()**

    b. Dans la liste courrier, sélectionnez l’attribut utilisateur que vous souhaitez utiliser pour votre implémentation. 
    Par exemple, si vous souhaitez utiliser EmployeeID comme identificateur d’utilisateur unique et la valeur de l’attribut stockées dans le ExtensionAttribute2, puis sélectionnez **user.extensionattribute2**. 

    c. Cliquez sur **terminé**. 
    

4. Dans le portail classique, dans la page de l’intégration **SAP Business ByDesign** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

5. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à SAP Business ByDesign** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 

    un. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application SAP Business ByDesign selon le modèle suivant :`https://<servername>.sapbydesign.com`
    
    b. Cliquez sur **suivant**
 
7. Dans la page **configuration de l’authentification unique à SAP Business ByDesign** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


8. Pour obtenir l’authentification unique configuré pour votre application, effectuez les opérations suivantes :

    un. Connectez-vous à votre portail SAP Business ByDesign avec des droits d’administrateur.

    b. Accédez à **l’Application et utilisateur gestion des tâches courantes** et cliquez sur l’onglet **Fournisseur d’identité** .

    c. Cliquez sur **Nouveau fournisseur d’identité** et sélectionnez le fichier XML de métadonnées que vous avez téléchargé à partir du portail classique Azure. En important les métadonnées, le système télécharge automatiquement le certificat de signature nécessaires et le certificat de chiffrement.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

    d. Pour inclure l' **URL du Service Assertion consommateur** dans la demande SAML, sélectionnez **URL du Service inclure Assertion grand public**.

    e. Cliquez sur **Activer l’ouverture de session unique**.

    f. Enregistrez vos modifications.

    g. Cliquez sur l’onglet **Mon système** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

    h. Copiez l' **URL de l’authentification unique** et collez-le dans la zone de texte **URL d’authentification Azure AD** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

    J’ai. Spécifiez si l’employé peut manuellement choisir entre l’ouverture de session avec ID d’utilisateur et mot de passe ou l’authentification unique en sélectionnant la **Sélection de fournisseur d’identité manuel**.

    j. Dans la section **URL de l’authentification unique** , spécifiez l’URL qui doit être utilisé par l’employé pour vous connecter au système. 
    Dans l’URL envoyée à liste déroulante employé, vous pouvez choisir parmi les options suivantes :
    
    **URL non SSO**
 
    Le système envoie uniquement l’URL normale du système à l’employé. L’employé ne peut pas se connecter à l’aide de l’authentification unique et doit utiliser de mot de passe ou de certificats à la place.

    **URL DE L’AUTHENTIFICATION UNIQUE** 

    Le système envoie uniquement l’URL de l’authentification unique à l’employé. L’employé pouvez vous connecter à l’aide de l’authentification unique. Demande d’authentification est redirigé via la IdP.

    **Sélection automatique**
 
    Si l’authentification unique n’est pas actif, le système envoie l’URL normale du système à l’employé. Si l’authentification unique est actif, le système vérifie si l’employé possède un mot de passe. Si un mot de passe est disponible, URL de l’authentification unique et l’authentification unique Non URL sont envoyées à l’employé. Toutefois, si l’employé n’a aucun mot de passe, uniquement l’URL de l’authentification unique est envoyée à l’employé.

    k. Enregistrez vos modifications.

9. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-sap-business-bydesign-test-user"></a>Création d’un utilisateur de test SAP Business ByDesign

Dans cette section, vous créez un utilisateur appelé Britta Simon dans SAP Business ByDesign. Contactez l’équipe de support SAP Business ByDesign pour ajouter les utilisateurs de la plate-forme SAP Business ByDesign. 

> [AZURE.NOTE] Assurez-vous que la valeur NameID doit correspondre avec le champ nom d’utilisateur dans la plateforme SAP Business ByDesign.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique en lui accorder son accès à SAP Business ByDesign.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à SAP Business ByDesign, effectuez les opérations suivantes :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **SAP Business ByDesign**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette SAP Business ByDesign dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application SAP Business ByDesign.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png
