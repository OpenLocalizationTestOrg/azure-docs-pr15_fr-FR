<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Didacticiel : Intégration d’Azure Active Directory avec DocuSign

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et DocuSign.
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

- Un abonnement Azure valide
- Un client dans DocuSign



Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1. [L’activation de l’intégration des applications pour DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configuration de l’authentification unique](#configuring-single-sign-on) 


3. [Configuration de la mise en service de compte](#configuring-account-provisioning) 


4. [Affectation d’utilisateurs](#assigning-users) 

    ![Configuration de l’authentification unique][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>L’activation de l’intégration des applications pour DocuSign

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Pour activer l’intégration des applications pour DocuSign, effectuez les opérations suivantes :

1. Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Configuration de l’authentification unique][1]

2. Dans la liste répertoire, sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Configuration de l’authentification unique][2]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Applications][3]

5. Sur la page que vous voulez suivre la boîte de dialogue, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Configuration de l’authentification unique][4]


6. Dans la zone Rechercher, tapez **DocuSign**.

    ![Configuration de l’authentification unique][5]

7. Dans le volet résultats, sélectionnez **DocuSign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Configuration de l’authentification unique][6]


## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à DocuSign avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail classique Azure, dans la page de **l’intégration de l’application DocuSign** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue Configurer session unique.

    ![Configuration de l’authentification unique][7]

2. Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à DocuSign** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur Suivant.

    ![Configuration de l’authentification unique][8]

3. Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configuration de l’authentification unique][61]

    un. Dans la zone de texte **URL ouverture de session** , tapez `https://account.docusign.com/*`.  

    b. Dans la zone de texte **identificateur** , tapez `https://account.docusign.com/*`.  
   
    c. Cliquez sur **suivant**. 


    > [AZURE.TIP] Signe d’URL et les valeurs d’identification sont uniquement des espaces réservés. Instructions extraire les valeurs réelles pour votre environnement sont décrites plus loin dans cette rubrique.
 

4. Dans la page **configuration de l’authentification unique en DocuSign** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configuration de l’authentification unique][10]


5. Dans une fenêtre de navigateur web différente, connectez-vous à votre **portail d’administration DocuSign** en tant qu’administrateur.


6. Dans le menu de navigation gauche, cliquez sur **domaines**.

    ![Configuration de l’authentification unique][51]

7. Dans le volet droit, cliquez sur **Réclamer domaine**.

    ![Configuration de l’authentification unique][52]

8. Dans la boîte de dialogue **réclamer un domaine** , dans la zone de texte **Nom de domaine** , tapez le domaine de votre entreprise, puis cliquez sur **réclamer**. Assurez-vous que vous vérifiez le domaine et le statut est actif.

    ![Configuration de l’authentification unique][53]

9. Dans le menu situé à gauche, cliquez sur **Fournisseurs d’identité**  

    ![Configuration de l’authentification unique][54]

10. Dans le volet droit, cliquez sur **Ajouter fournisseur d’identité**. 
    
    ![Configuration de l’authentification unique][55]

11. Dans la page **Paramètres de fournisseur d’identité** , effectuez les opérations suivantes :

    ![Configuration de l’authentification unique][56]


    un. Dans la zone de texte **nom** , tapez un nom unique pour votre configuration. N’utilisez pas les espaces.

    b. Dans le portail classique Azure, copiez l’URL de l’émetteur et collez-le dans la zone de texte **Émetteur de fournisseur d’identité** .

    c. Dans le portail classique Azure, copiez l' **URL de connexion à distance**et collez-le dans la zone de texte **URL connexion du fournisseur d’identité** .

    d. Dans le portail classique Azure, copiez l' **URL de déconnexion à distance**et collez-le dans la zone de texte **URL déconnexion du fournisseur d’identité** .

    e. Sélectionnez **Se demande d’authentification**.

    f. En tant que **requête seul envoyer par**, sélectionnez **Publier**.

    g. **Envoyer la demande de déconnexion par**, sélectionnez **Publier**. 


12. Dans la section **Correspondance des attributs personnalisés** , cliquez sur le champ à mapper avec Azure AD réclamer. Dans cet exemple, la demande de remboursement **emailaddress** est mappé avec la valeur de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. C’est le nom de réclamer par défaut à partir d’Azure AD pour réclamer de messagerie. 

    > [AZURE.NOTE] Utilisez l' **identificateur de l’utilisateur** approprié pour mapper l’utilisateur à partir d’Azure AD au mappage utilisateur Docusign. Sélectionnez le champ approprié, entrez la valeur appropriée en fonction des paramètres de votre organisation.

    ![Configuration de l’authentification unique][57]

13. Dans la section **Certificat d’identité du fournisseur** , cliquez sur **Ajouter un certificat**et puis téléchargez le certificat que vous avez téléchargé à partir de portail classique Azure AD.   

    ![Configuration de l’authentification unique][58]

14. Cliquez sur **Enregistrer**.

15. Dans la section **Fournisseurs d’identité** , cliquez sur **Actions**, puis cliquez sur **points de terminaison**.   

    ![Configuration de l’authentification unique][59]



10. Dans le portail classique Azure, revenez à la page **Configurer les paramètres de l’application** . 

16. Dans le **portail d’administration DocuSign**, dans la section **Points de terminaison affichage SAML 2.0** effectuer, les étapes suivantes :

    ![Configuration de l’authentification unique][60]

    un. Copiez l' **URL émetteur du fournisseur de Service**et collez-le dans la zone de texte **identificateur** dans le portail classique Azure.

    b. Copiez l' **URL de connexion de fournisseur de Service**et collez-le dans la zone de texte **URL de connexion** dans le portail classique Azure.

    c.  Cliquez sur **Fermer**  


10. Dans le portail Azure classique, cliquez sur **suivant**. 


15. Dans le portail classique Azure, sélectionnez la **confirmation de la configuration de l’authentification unique**, puis cliquez sur **suivant**.

    ![Applications][14]

10. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.

    ![Applications][15]
 

## <a name="configuring-account-provisioning"></a>Configuration de la mise en service de compte

L’objectif de cette section est hiérarchique l’activation d’utilisateur mise en service des comptes d’utilisateurs Active Directory DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1. Dans le **portail classique Azure**, dans la page **DocuSign intégration de l’application** , cliquez sur **mise en service de compte configurer** pour ouvrir la boîte de dialogue Configurer la mise en service des utilisateurs.

    ![Configuration de la mise en service de compte][30]

2. Dans la page **paramètres et informations d’identification d’administration** , pour activer la mise en service automatique de l’utilisateur, fournir les informations d’identification d’un compte DocuSign droits nécessaires, puis cliquez sur **suivant**. 

    ![Configuration de la mise en service de compte][31]

3. Dans la boîte de dialogue **tester la connexion** , cliquez sur **Démarrer le test**et un essai réussie, cliquez sur **suivant**.

    ![Configuration de la mise en service de compte][32]

3. Dans la page de **Confirmation** , cliquez sur **Terminer**.

    ![Configuration de la mise en service de compte][33]
 

## <a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Pour affecter des utilisateurs à DocuSign, effectuez les opérations suivantes :

1. Dans le **portail classique Azure**, créez un compte de test.

2. Dans la page **DocuSign intégration de l’application** , cliquez sur **attribuer aux utilisateurs**.

    ![Affectation d’utilisateurs][40]
 

3. Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Affectation d’utilisateurs][41]


Vous devez maintenant attendez 10 minutes et vérifiez que le compte a été synchronisé avec DocuSign.

Vérification d’abord, vous pouvez vérifier l’état de mise à disponibilité, en cliquant sur tableau de bord dans le D sur la page de l’intégration d’application DocuSign dans le portail classique Azure.

![Affectation d’utilisateurs][42]

Un utilisateur terminé cycle de mise en service est indiqué par un état associé :

![Affectation d’utilisateurs][43]


Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès.

Pour plus d’informations sur le panneau d’accès, voir Présentation du panneau d’accès.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png