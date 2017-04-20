<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Veracode | Microsoft Azure" 
    description="Découvrez comment utiliser Veracode avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Didacticiel : Intégration d’Azure Active Directory avec Veracode
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Veracode. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Veracode d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Veracode sera en mesure d’authentification unique dans l’application à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Veracode
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Scénario")

##<a name="enabling-the-application-integration-for-veracode"></a>L’activation de l’intégration des applications pour Veracode
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Veracode.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Veracode, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Veracode**.

    ![Galerie d’applications] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Veracode**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Veracode avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Votre application Veracode attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Veracode** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Veracode** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , cliquez sur **suivant**.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique en Veracode** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Veracode en tant qu’administrateur.

6.  Dans le menu dans la partie supérieure, cliquez sur **paramètres**, puis cliquez sur **administrateur**.

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  Cliquez sur l’onglet **SAML** .

8.  Dans la section **Paramètres de SAML l’organisation** , procédez comme suit :

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Veracode** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte de **l’émetteur**
    2.  Pour télécharger votre certificat téléchargé, cliquez sur **Choisir un fichier**.
    3.  Sélectionnez **Activer l’inscription automatique**.

9.  Dans la section **Paramètres d’enregistrement automatique** , effectuez les opérations suivantes, puis cliquez sur **Enregistrer**:

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  En tant **Nouveau l’Activation d’utilisateur**, sélectionnez **Aucune Activation requise**.
    2.  En tant **Les données mises à jour**, sélectionnez **Préférences Veracode User Data**.
    3.  Pour **Plus d’informations SAML attribut**, sélectionnez les options suivantes :
        -   **Rôles d’utilisateur**
        -   **Administrateur de stratégie**
        -   **Relecteur**
        -   **Sécurité prospect**
        -   **Membre de la direction**
        -   **Expéditeur**
        -   **Créateur de blocs-notes**
        -   **Tous les analyser Types**
        -   **Membres de l’équipe**
        -   **Équipe par défaut**

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurer l’authentification unique")

11. Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributs")

12. Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

  	| Nom de l’attribut | Valeur de l’attribut |
  	|:---------------|:----------------|
  	| Prénom      | User.GivenName  |
  	| nom       | User.Surname    |
  	| Messagerie          | User.Mail       |

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.

    3.  Dans la zone de texte **Valeur de l’attribut** , sélectionnez la valeur de l’attribut affichée pour cette ligne.

    4.  Cliquez sur **terminé**.

13. Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Veracode, ils doivent être configurées dans Veracode.  
Dans le cas Veracode, mise en service est une tâche automatique.  
Il n’existe aucun élément action pour vous...
  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous authentification unique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Veracode utilisateur compte outils de création ou API fournies par Veracode aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Pour affecter des utilisateurs à Veracode, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Veracode **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).