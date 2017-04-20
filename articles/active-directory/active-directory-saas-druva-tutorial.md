<properties 
    pageTitle="Didacticiel : Azure Active Directory intégration intégrant Druva | Microsoft Azure" 
    description="Découvrez comment utiliser Druva avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Didacticiel : Azure Active Directory intégration intégrant Druva

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Druva.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Druva d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Druva sera en mesure d’authentification unique dans l’application sur votre site de société Druva (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Druva
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-druva-tutorial/IC795084.png "Scénario")
##<a name="enabling-the-application-integration-for-druva"></a>L’activation de l’intégration des applications pour Druva

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Druva.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Druva, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-druva-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Druva**.

    ![Galerie d’applications] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Druva**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Druva avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Votre application Druva attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs jetons SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Attributs jetons SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Druva** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Druva** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Druva d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Druva (par exemple : «*https://cloud.druva.com/home/*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Druva** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise Druva en tant qu’administrateur.

6.  Accédez à **gérer \> paramètres**.

    ![Paramètres] (./media/active-directory-saas-druva-tutorial/IC795091.png "Paramètres")

7.  Dans la boîte de dialogue Paramètres d’authentification unique, procédez comme suit :

    ![Paramètres d’authentification osaïque] (./media/active-directory-saas-druva-tutorial/IC795092.png "Paramètres d’authentification osaïque")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Druva** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **ID fournisseur l’URL de connexion** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Druva** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **ID fournisseur déconnexion URL** .
    3.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **ID fournisseur certificat**
    5.  Pour ouvrir la page **paramètres** , cliquez sur **Enregistrer**.

8.  Dans la page **paramètres** , cliquez sur **Générer le jeton de l’authentification unique**.

    ![Paramètres] (./media/active-directory-saas-druva-tutorial/IC795093.png "Paramètres")

9.  Dans la boîte de dialogue **Unique authentification jeton d’authentification** , procédez comme suit :

    ![Jeton de l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795094.png "Jeton de l’authentification unique")

    1.  Cliquez sur **Copier**.
    2.  Cliquez sur **Fermer**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurer l’authentification unique")

11. Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-druva-tutorial/IC795096.png "Attributs")

12. Pour ajouter les mappages de l’attribut requis, procédez comme suit :

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|synchronisée\_auth\_jetons|<*valeur de Presse-papiers*>|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    3.  Dans la zone de texte **Valeur de l’attribut** , tapez la valeur de l’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

13. Cliquez sur **appliquer les modifications**.
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Druva, ils doivent être configurées dans Druva.  
Dans le cas Druva, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **Druva** en tant qu’administrateur.

2.  Accédez à **gérer \> utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795097.png "Gérer les utilisateurs")

3.  Cliquez sur **Créer nouveau**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795098.png "Gérer les utilisateurs")

4.  Dans la boîte de dialogue Créer un nouvel utilisateur, procédez comme suit :

    ![Créer NewUser] (./media/active-directory-saas-druva-tutorial/IC795099.png "Créer NewUser")

    1.  Tapez l’adresse de messagerie et le nom d’un compte d’utilisateur Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **créer un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Druva utilisateur compte outils de création ou API fournies par Druva aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Pour affecter des utilisateurs à Druva, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Druva **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795100.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-druva-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
