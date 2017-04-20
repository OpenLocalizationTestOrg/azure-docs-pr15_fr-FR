<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Qlik sens Enterprise | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qlik sens Enterprise."
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec Qlik sens Enterprise

Dans ce didacticiel, vous découvrez comment intégrer Qlik sens entreprise Azure Active Directory (AD Azure).

Intégration Qlik sens Enterprise avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui accèdent à Qlik sens Enterprise
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir connecté-on to Qlik sens Enterprise (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration Azure AD avec Qlik sens entreprise, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une authentification unique Qlik sens entreprise lors de l’abonnement activé


> [AZURE.NOTE] Pour tester les étapes décrites dans ce didacticiel, nous ne recommandons pas à l’aide d’un environnement de production.


Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD SSO dans un environnement de test.

Scénario présenté dans ce didacticiel se compose de deux principaux blocs de construction :

1. Ajout de Qlik sens entreprise à partir de la galerie
2. Configuration et test Azure AD authentification unique


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Ajout de Qlik sens entreprise à partir de la galerie
Pour configurer l’intégration des Qlik sens entreprise dans Azure AD, vous devez ajouter Qlik sens entreprise à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Qlik sens entreprise à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Qlik sens Enterprise**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Dans le volet résultats, sélectionnez **Qlik sens Enterprise**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test Azure AD authentification unique
Dans cette section, vous configurez et testez Azure AD SSO avec Qlik sens Enterprise basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique pour l’utiliser, Azure AD doit détermine-t-il ce qui l’utilisateur homologue dans Qlik sens entreprise est à un utilisateur dans Active Directory Azure. En d’autres termes, une relation de liaison entre un utilisateur Azure AD et l’utilisateur connexe dans Qlik sens Enterprise doit être établie.

Cette relation lien est établie en affectant la valeur du **nom d’utilisateur** dans Active Directory Azure en tant que la valeur **nom d’utilisateur** dans Qlik sens Enterprise.

Pour configurer et tester Azure AD SSO avec Qlik sens Enterprise, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Azure AD SSO](#configuring-azure-ad-single-sign-on)** - pour permettre à vos utilisateurs utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - permettent de tester Azure AD SSO avec Britta Simon.
3. **[Créez une entreprise sens Qlik tester l’utilisateur](#creating-a-qliksense-enterprise-test-user)** - d’avoir un homologue de Britta Simon dans Qlik sens entreprise qui est lié à la représentation Azure AD de lui.
4. **[Attribution de l’annonce Azure tester l’utilisateur](#assigning-the-azure-ad-test-user)** - activer Britta Simon utiliser Azure AD SSO.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - afin de vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD SSO

Dans cette section, vous activez Azure AD SSO dans le portail classique et configurez l’authentification unique dans votre application d’entreprise de sens Qlik.


**Pour configurer Azure AD SSO avec Qlik sens Enterprise, procédez comme suit :**

1. Dans le portail classique, dans la page de l’intégration **Qlik sens Enterprise** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Qlik sens entreprise** , sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. Dans la page de la boîte de dialogue **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    un. Dans la zone de texte **Se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application Qlik sens entreprise en utilisant le modèle suivant : **https://\<Qlik sens donnez entièrement Hostname\>: 443 / < préfixe Proxy virtuel\>/samlauthn/**.
    
    > [AZURE.NOTE] Notez la barre oblique à la fin de cet URI.  Il est nécessaire.

    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique en Qlik sens Enterprise** , procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    un. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.  Préparez-vous à modifier ce fichier de métadonnées avant de le télécharger sur le serveur Qlik sens.

    b. Cliquez sur **suivant**.

5. Préparer le fichier XML de métadonnées de fédération afin que vous pouvez télécharger qui au serveur Qlik sens.

    > [AZURE.NOTE] Avant de télécharger les métadonnées IdP sur le serveur Qlik sens, le fichier doit être modifié pour supprimer les informations pour garantir le bon fonctionnement entre Azure AD et sens Qlik server.

    ![QlikSense][qs24]

    un. Ouvrez le fichier FederationMetaData.xml téléchargé à partir d’Azure dans un éditeur de texte.

    b. Rechercher la valeur **RoleDescriptor**.  Il y a quatre entrées (deux paires de balises élément ouvrantes et fermantes).

    c. Supprimer les balises RoleDescriptor et toutes les informations entre les deux à partir du fichier.

    d. Enregistrez le fichier et conserver à proximité pour l’utiliser ultérieurement dans ce document.

6. Accédez à la Console de gestion des Qlik sens Qlik (QMC) en tant qu’utilisateur qui peut créer des configurations de proxy virtuel.

7. Dans la QMC, cliquez sur l’élément de menu Proxy virtuel.

    ![QlikSense][qs6] 

8. Dans la partie inférieure de l’écran, cliquez sur le bouton Créer un.

    ![QlikSense][qs7]

9. L’écran Modifier le proxy virtuelle s’affiche.  Sur le côté droit de l’écran est un menu pour rendre les options de configuration visibles.

    ![QlikSense][qs9]

10. Avec l’option de menu Identification cochée, entrez les informations d’identification pour la configuration du proxy virtuel Azure.

    ![QlikSense][qs8]  
    
    un. Le champ Description est un nom convivial pour la configuration du proxy virtuel.  Pour obtenir une description, entrez une valeur.
    
    b. Le champ préfixe identifie le point de terminaison proxy virtuel pour vous connecter à sens Qlik avec Azure AD SSO.  Entrez un nom de préfixe unique pour ce proxy virtuel.

    c. Délai d’inactivité de session (en minutes) est le délai d’expiration pour les connexions via ce proxy virtuel.

    d. Le nom d’en-tête Session des cookies est le nom des cookies le stockage de l’identificateur de session pour la session Qlik pertinent pour qu'un utilisateur reçoit après une authentification réussie.  Ce nom doit être unique.

11. Cliquez sur l’option de menu d’authentification pour le rendre visible.  L’écran d’authentification s’affiche.

    ![QlikSense][qs10]

    un. La liste déroulante **mode d’accès anonyme** détermine si les utilisateurs anonymes peuvent accéder à sens Qlik via le proxy virtuel.  L’option par défaut n’est aucun utilisateur anonyme.

    b. La **méthode d’authentification** de liste déroulante détermine que le schéma d’authentification du proxy virtuel utilisera.  Sélectionnez SAML dans la liste déroulante.  Autres options seront affichent en conséquence.

    c. Dans le **champ d’URI SAML hôte**, entrez que les utilisateurs hostname insère pour accéder à sens Qlik via ce proxy virtuel SAML.  Le nom d’hôte est l’uri du serveur Qlik sens.

    d. Dans l' **ID de l’entité SAML**, entrez la même valeur entrée pour le champ SAML hôte URI.

    e. Les **métadonnées SAML IdP** est le fichier modifié plus haut dans la section **Modifier les métadonnées de fédération à partir d’Azure AD Configuration** .  **Avant de télécharger les métadonnées IdP, le fichier doit être modifié** pour supprimer les informations pour garantir le bon fonctionnement entre Azure AD et sens Qlik server.  **Consultez les instructions ci-dessus si le fichier doit être modifié.**  Si le fichier a été modifié cliquez sur le bouton Parcourir et sélectionnez le fichier de métadonnées modifié à Téléchargez-le sur la configuration du proxy virtuel.

    f. Entrez le nom de l’attribut ou référence de schéma pour l’attribut SAML représentant **nom d’utilisateur** Azure AD vous envoie au serveur Qlik sens.  Informations de référence de schéma sont disponibles dans la configuration de la publication écrans application Azure.  Pour utiliser le champ nom, **Entrez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Entrez la valeur de l' **annuaire de l’utilisateur** qui sera associé aux utilisateurs lors de l’authentification au serveur Qlik sens via Azure AD.  Valeurs codé en dur doivent être entourés de **crochets []**.  Pour utiliser un attribut envoyé dans l’assertion Azure AD SAML, entrez le nom de l’attribut dans cette zone de texte **sans** des crochets.

    h. L' **algorithme de signature SAML** définit le certificat de fournisseur (dans ce cas sens Qlik server) service vous vous connectez pour la configuration du proxy virtuel.  Si serveur sens Qlik utilise un certificat approuvé généré à l’aide de Microsoft amélioré RSA et AES Cryptographic Provider, modifiez l’algorithme de signature SAML à **ça 256**.

    J’ai. La section SAML attribut mappage permet d’autres attributs comme les groupes envoyées à sens Qlik à utiliser dans les règles de sécurité.

12. Cliquez sur l’option de menu pour le rendre visible d’équilibrage de charge.  L’écran de l’équilibrage de charge s’affiche.

    ![QlikSense][qs11]

13. Cliquez sur Ajouter nouveau serveur nœud, nœud moteur select ou nœuds Qlik pertinent pour envoyer des sessions afin d’à des fins d’équilibrage de charge et cliquez sur le bouton Ajouter.

    ![QlikSense][qs12]

14. Cliquez sur l’option de menu Avancé pour le rendre visible. L’écran Avancé s’affiche.

    ![QlikSense][qs13]

    un. La liste de livre hôte identifie les noms d’hôtes qui ont été acceptées lors de la connexion au serveur Qlik sens.  **Entrez le nom d’hôte utilisateurs spécifierez lors de la connexion au serveur Qlik sens.** Le nom d’hôte est la même valeur que l’uri hôte SAML sans la https://.

15. Cliquez sur le bouton Appliquer.

    ![QlikSense][qs14]

16. Cliquez sur OK pour accepter le message d’avertissement indiquant les proxys liés au proxy virtuel seront redémarrés.

    ![QlikSense][qs15]

17. Sur le côté droit de l’écran, le menu éléments associés s’affiche.  Cliquez sur l’option de menu proxys.

    ![QlikSense][qs16]

18. L’écran proxy s’affiche.  Cliquez sur le bouton de lien en bas pour créer un lien vers le proxy virtuel un proxy.

    ![QlikSense][qs17]

19. Sélectionnez le nœud de proxy qui prennent en charge cette connexion proxy virtuel et cliquez sur le bouton de lien.  Une fois lié, le proxy s’affichent sous proxys associés.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Après environ 5 à 10 secondes, le message QMC Actualiser s’affichent.  Cliquez sur le bouton Actualiser QMC.

    ![QlikSense][qs20]

21. Lorsque la QMC actualise, cliquez sur l’élément de menu proxys virtuelle. La nouvelle entrée proxy virtuel SAML est répertoriée dans la table à l’écran.  Clic sur l’entrée du proxy virtuel.

    ![QlikSense][qs51]

22. Dans la partie inférieure de l’écran, le bouton de métadonnées SP télécharger activera.  Cliquez sur le bouton de métadonnées SP télécharger pour enregistrer les métadonnées dans un fichier.

    ![QlikSense][qs52]

23. Ouvrez le fichier de métadonnées sp.  Observez l’entrée **entityID** et l’entrée **AssertionConsumerService** .  Ces valeurs sont équivalentes pour l' **identificateur de** l' **ouverture de session URL** dans la configuration d’application Azure AD. Si elles ne correspondent pas vous devez les remplacer dans l’Assistant de configuration de l’application Azure AD.

    ![QlikSense][qs53]

24. Dans le portail classique, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.
    
    ![Authentification Azure AD unique][10]

25. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.  
 
    ![Authentification Azure AD unique][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créez Azure AD utilisateur][20]

**Pour créer un utilisateur de test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils en bas, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. Dans la page de dialogue **dites-nous sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    un. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**du nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Dans la page de la boîte de dialogue **Profil utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    un. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Dans la page de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. Dans la page de dialogue **obtenir le mot de passe temporaire** , procédez comme suit :

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    un. Notez la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Création d’un utilisateur de test Qlik sens entreprise

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Qlik sens Enterprise. Contactez l’équipe de support Qlik sens entreprise afin d’ajouter des utilisateurs dans la plateforme Qlik sens Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon utiliser Azure de l’authentification unique en accordant son accès aux Qlik sens Enterprise.

![Affecter utilisateur][200] 

**Pour attribuer Britta Simon à Qlik sens Enterprise, procédez comme suit :**

1. Dans le portail classique, pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter utilisateur][201] 

2. Dans la liste des applications, sélectionnez **Qlik sens Enterprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. Dans le menu dans la partie supérieure, cliquez sur **utilisateurs**.

    ![Affecter utilisateur][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils en bas, cliquez sur **attribuer**.

    ![Affecter utilisateur][205]


## <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre Azure AD unique authentification configuration à l’aide du panneau d’accès.

Lorsque vous cliquez sur la vignette Qlik sens entreprise dans le panneau d’accès, vous devez obtenir automatiquement connecté-on à votre application Qlik sens Enterprise.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png