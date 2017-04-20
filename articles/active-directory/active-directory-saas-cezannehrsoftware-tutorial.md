<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec un logiciel de ressources humaines Cezanne | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cezanne HR logiciel."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Didacticiel : Intégration d’Azure Active Directory avec un logiciel de ressources humaines Cezanne

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Cezanne HR logiciel Azure Active Directory (AD Azure).

Intégration du logiciel de HR Cezanne avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui ont accès au logiciel de ressources humaines Cezanne
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Cezanne HR logiciel (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Cezanne HR logiciel, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Cezanne HR logiciel lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel consiste à vous permettent de tester Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout d’un logiciel de ressources humaines Cezanne à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Ajout d’un logiciel de ressources humaines Cezanne à partir de la galerie
Pour configurer l’intégration du logiciel de HR Cezanne dans Azure Active Directory, vous devez ajouter Cezanne HR logiciel à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Cezanne HR logiciel à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Cezanne HR logiciel**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. Dans le volet résultats, sélectionnez **Cezanne HR logiciel**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sélection de l’application dans la galerie](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
L’objectif de cette section consiste à vous montrer comment configurer et tester Azure AD SSO avec un logiciel de ressources humaines Cezanne basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui est l’utilisateur homologue Cezanne HR logiciel à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe Cezanne HR logiciel doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** Cezanne HR logiciel.

Pour configurer et tester Azure AD SSO avec un logiciel de ressources humaines Cezanne, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Création d’un logiciel de HR Cezanne tester l’utilisateur](#creating-a-cezanne-hr-software-test-user)** - d’avoir un homologue de Britta Simon Cezanne HR logiciel lié à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application Cezanne HR logicielle.

**Pour configurer Azure AD SSO avec un logiciel de ressources humaines Cezanne, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page de l’intégration **Cezanne HR logiciel** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Cezanne HR logiciel** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    un. Dans la zone de texte **URL de connexion** , tapez une URL à l’aide du modèle suivant : `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant le modèle suivant : `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Cliquez sur **suivant**

    > [AZURE.NOTE] Veuillez noter que vous devez mettre à jour ces valeurs avec le signe sur URL réelle et l’URL de réponse. Pour obtenir ces valeurs, contacter l’équipe de support Cezanne HR logiciel via <mailto:info@cezannehr.com>.

4. Dans la page **configuration de l’authentification unique chez Cezanne HR Software** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    un. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Dans une fenêtre de navigateur web différente, authentification à votre client Cezanne HR logiciel en tant qu’administrateur.

6. Dans le volet de navigation gauche, cliquez sur **Configuration du système**. Accédez aux **paramètres de sécurité**. Ensuite, accédez à la **Configuration de l’authentification unique**.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. Dans **Autoriser les utilisateurs à se connecter au moyen du Service d’authentification unique (SSO) suivant** panneau Cochez la case **SAML 2.0** et sélectionnez l’option **Configuration avancée** en regard.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Cliquez sur **Ajouter un nouveau** bouton.

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Effectuez les opérations suivantes dans la section **Fournisseurs d’identité SAML 2.0** .

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    un. Entrez le nom de votre fournisseur d’identité en tant que le **Nom d’affichage**.

    b. Dans **l’Identificateur** zone de texte placer la valeur **d’ID de l’entité** à partir de l’Assistant configuration d’application Azure AD.

    c. Modifier la **liaison SAML** à « Publication ».

    d. Dans le **Point de terminaison Service jeton de sécurité** zone de texte placer la valeur de **Unique authentification URL du Service** à partir de l’Assistant configuration d’application Azure AD.

    e. Entrez « http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name » dans le **nom de l’attribut ID utilisateur**.

    f. Cliquez sur **Télécharger** pour télécharger le certificat téléchargé à partir d’Azure AD.

    g. Cliquez sur le bouton **Ok** . 

10. Cliquez sur le bouton **Enregistrer** .

    ![Configurer côté Single authentification sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

12. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
    
    ![Authentification Azure AD unique][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section consiste à créer un utilisateur de test dans le portail classique appelé Britta Simon.

![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , tapez **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Création d’un utilisateur de test Cezanne HR logiciel

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Cezanne HR logiciel, ils doivent être configurées dans Cezanne HR logiciel. Dans le cas Cezanne HR logiciel, mise en service est une tâche manuelle.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise Cezanne HR logiciel en tant qu’administrateur.

2.  Dans le volet de navigation gauche, cliquez sur **Configuration du système**. Accédez à **Gérer les utilisateurs**. Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nouvel utilisateur")

3.  Dans la section **Détails de la personne** , suivez les étapes ci-dessous :

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nouvel utilisateur")

    un. Définir **utilisateur interne** comme désactivé.

    b. Dans la zone de texte **nom** , tapez **Brian**.  

    c. Dans la zone de texte **Nom** , tapez **Simon**.

    d. Dans la zone de texte **courrier électronique** , tapez l’adresse de messagerie du compte Britta Simon.

4.  Dans la section **Informations de compte** , suivez les étapes ci-dessous :

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nouvel utilisateur")

    un. Dans la zone de texte **nom d’utilisateur** , tapez l’adresse de messagerie de Britta Simon.

    b. Dans la zone de texte **mot de passe** , tapez le mot de passe du compte Britta Simon.

    c. Sélectionner **HR Professionnel** comme **rôle de sécurité**.

    d. Cliquez sur **OK**.

5. Accédez à **L’ouverture de session unique** onglet, puis sélectionnez **Ajouter un nouveau** dans la zone **SAML 2.0 identificateurs** .

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilisateur")

6. Choisissez votre fournisseur d’identité pour le **Fournisseur d’identité** et dans la zone de texte de **l’Identificateur de l’utilisateur**, entrez l’adresse de messagerie du compte Britta Simon.

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilisateur")
    
7. Cliquez sur le bouton **Enregistrer** .

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilisateur")


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

L’objectif de cette section est à l’activation Britta Simon utiliser Azure de l’authentification unique en accordant son accès aux Cezanne HR logiciel.
    
![Affecter utilisateur][200]

**Pour attribuer Britta Simon Cezanne HR logiciel, procédez comme suit :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter utilisateur][201]

2. Dans la liste des applications, sélectionnez **Cezanne HR logiciel**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.
    
    ![Affecter utilisateur][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section consiste à tester votre Azure AD unique authentification configuration à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la vignette Cezanne HR logiciel dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Cezanne HR logiciel.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
