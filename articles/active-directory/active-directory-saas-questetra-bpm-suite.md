<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec la Suite de gestion des processus métiers Questetra | Microsoft Aure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de la Suite de gestion des processus métiers Questetra."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Didacticiel : Intégration d’Azure Active Directory avec la Suite de gestion des processus métiers Questetra

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Suite de gestion des processus métiers Questetra Azure Active Directory (AD Azure).  
Intégration de Suite de gestion des processus métiers Questetra avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à la Suite de gestion des processus métiers Questetra 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on dans la Suite de gestion des processus métiers Questetra (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec Questetra gestion des processus métiers Suite, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique [Suite de gestion des processus métiers Questetra](https://senbon-imadegawa-988.questetra.net/) lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.  
Scénario présenté dans ce didacticiel se compose de trois grands blocs de construction :

1. Ajout de Questetra gestion des processus métiers Suite à partir de la galerie 
2. Configuration et test Azure AD authentification unique


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Ajout de Questetra gestion des processus métiers Suite à partir de la galerie
Pour configurer l’intégration de la Suite de gestion des processus métiers Questetra dans Azure AD, vous devez ajouter Questetra gestion des processus métiers Suite à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Questetra gestion des processus métiers Suite à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Questetra gestion des processus métiers Suite**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Suite de gestion des processus métiers Questetra**, puis cliquez sur **Terminer** pour ajouter l’application.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec Suite de gestion des processus métiers Questetra basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue dans Questetra gestion des processus métiers Suite à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans la Suite de gestion des processus métiers Questetra doit être établie.  
Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans la Suite de gestion des processus métiers Questetra.
 
Pour configurer et tester Azure AD SSO avec Questetra gestion des processus métiers Suite, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
4. **[Création d’une Suite de gestion des processus métiers Questetra tester l’utilisateur](#creating-a-questetra-bpm-suite-test-user)** - d’avoir un homologue de Britta Simon dans Suite de gestion des processus métiers Questetra qui est lié à la représentation Azure AD de lui.
5. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

L’objectif de cette section est pour activer Azure AD SSO dans le portail classique Azure et configurer l’authentification unique dans votre application Suite de gestion des processus métiers Questetra.

**Pour configurer Azure AD SSO avec gamme de gestion des processus métiers Questetra, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, dans la page de l’intégration **Suite de gestion des processus métiers Questetra** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][8]

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à la Suite de gestion des processus métiers Questetra** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Authentification Azure AD unique][9]


3. Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise **Suite de gestion des processus métiers Questetra** en tant qu’administrateur.

4. Dans le menu dans la partie supérieure, cliquez sur **Paramètres système**. 

    ![Authentification Azure AD unique][10]

5. Pour ouvrir la page **SingleSignOnSAML** , cliquez sur **L’authentification unique (SAML)**. 

    ![Authentification Azure AD unique][11]


6. Dans le portail classique Azure, dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes : 

    ![Configurer les paramètres de l’application][13]
 
    un. Sur votre **Suite de gestion des processus métiers Questetra** société site, dans la section informations SP, copiez l' **URL ACS**et collez-le dans la zone de texte **URL de connexion** .

    b. Sur votre **Suite de gestion des processus métiers Questetra** société site, dans la section informations SP, copiez l' **ID de l’entité**et collez-le dans la zone de texte **URL de l’émetteur** .

    c. Sur votre **Suite de gestion des processus métiers Questetra** société site, dans la section informations SP, copiez l' **URL ACS**et collez-le dans la zone de texte **URL de réponse** .

    d. Cliquez sur **suivant**.

 
7. Dans la page **configuration de l’authentification unique en Questetra gestion des processus métiers Suite** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique][14]


8. Sur votre site d’entreprise **Suite de gestion des processus métiers Questetra** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique][15]

    un. Sélectionnez **Activer l’authentification unique**.
     
    b. Dans le portail classique Azure, copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte **ID de l’entité** .

    c. Dans le portail classique Azure, copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **URL de la page se connecter** .

    d. Dans le portail classique Azure, copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL de la page de déconnexion** .

    e. Dans la zone de texte **format NameID** , tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : emailAddress**.


    f. Créer un fichier au format base 64 à partir de votre certificat téléchargé. 

    >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    g. Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et collez-le dans la zone de texte **certificat de Validation** . 

    h. Cliquez sur **Enregistrer**.


9. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**. 

    ![Qu’est Azure AD Connect][17]


10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  

    ![Qu’est Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique Azure appelé Britta Simon.

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Créer des utilisateurs de test Azure AD][100] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Créer des utilisateurs de test Azure AD][101] 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**. 

    ![Créer des utilisateurs de test Azure AD][102] 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Créer des utilisateurs de test Azure AD][103]
 
    un. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.
  
    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur Suivant.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit : 

    ![Créer des utilisateurs de test Azure AD][104] 
  
    un. Dans la zone de texte **nom** , tapez **Brian**. 
 
    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Créer des utilisateurs de test Azure AD][105]  

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Créer des utilisateurs de test Azure AD][106]   
  
    un. Notez la valeur de **Nouveau mot de passe**.
  
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Création d’un utilisateur de test Suite de gestion des processus métiers Questetra

L’objectif de cette section consiste à créer un utilisateur appelé Britta Simon dans la Suite de gestion des processus métiers Questetra.

**Pour créer un utilisateur appelé Britta Simon dans la Suite de gestion des processus métiers Questetra, effectuez les opérations suivantes :**

1.  Ouverture de session sur le site de votre société Suite de gestion des processus métiers Questetra en tant qu’administrateur.
2.  Accédez à **paramètres système > liste des utilisateurs > nouvel utilisateur**. 
3.  Dans la boîte de dialogue Nouvel utilisateur, procédez comme suit : 

    ![Créer des utilisateurs de test][300] 

    un. Dans la zone de texte **nom** , tapez le nom d’utilisateur de Brian dans Azure AD.

    b. Dans la zone de texte de **messagerie** , tapez le nom d’utilisateur de Brian dans Azure AD.

    c. Dans la zone de texte **mot de passe** , tapez un mot de passe.

4.  Cliquez sur **Ajouter un nouvel utilisateur**.



### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon à utiliser par l’octroi d’accès dans la Suite de gestion des processus métiers Questetra Azure de l’authentification unique.

![Qu’est Azure AD Connect][200]

**Pour attribuer Britta Simon dans la Suite de gestion des processus métiers Questetra, effectuez les opérations suivantes :**

1. Dans le portail classique Azure, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Qu’est Azure AD Connect][201]

2. Dans la liste des applications, sélectionnez **Suite de gestion des processus métiers Questetra**.

    ![Qu’est Azure AD Connect][205]

1. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Qu’est Azure AD Connect][202]

1. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

    ![Qu’est Azure AD Connect][203]

2. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Qu’est Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la vignette Suite de gestion des processus métiers Questetra dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Questetra gestion des processus métiers Suite.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 