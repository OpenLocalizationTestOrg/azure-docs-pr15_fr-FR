<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory portail personnel Mimecast | Microsoft Azure" 
    description="Découvrez comment utiliser portail personnel Mimecast avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Didacticiel : Intégration d’Azure Active Directory portail Mimecast personnel
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et portail personnel Mimecast.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un portail personnel Mimecast de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez attribué au portail personnel Mimecast sera en mesure d’authentification unique dans l’application sur votre site de société portail personnel Mimecast (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration de l’application portail personnel Mimecast
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scénario")
##<a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>L’activation de l’intégration de l’application portail personnel Mimecast
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration de l’application portail personnel Mimecast.

###<a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>Pour activer l’intégration de l’application portail personnel Mimecast, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Portail personnel Mimecast**.

    ![Galerie d’applications] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Portail personnel Mimecast**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Portail Mimecast personnel] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Portail Mimecast personnel")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier au portail personnel Mimecast avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Portail personnel Mimecast** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de vous connecter au portail personnel Mimecast** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Mimecast personnel portail se d’URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application portail personnel Mimecast (par exemple : « https://webmail-uk.mimecast.com » ou « https://webmail-us.mimecast.com »), puis cliquez sur **suivant**.

    >[AZURE.NOTE] Le signe sur l’URL est région spécifique.

    ![Configurer des URL de l’application] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique sur le portail personnel Mimecast** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre portail personnel Mimecast en tant qu’administrateur.

6.  Accédez à **Services \> Application**.

    ![Applications] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")

7.  Cliquez sur **profils d’authentification**.

    ![Profils d’authentification] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Profils d’authentification")

8.  Cliquez sur **nouveau profil d’authentification**.

    ![Nouveau Profil de l’authentification] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "Nouveau Profil de l’authentification")

9.  Dans la section **Authentification de profil** , procédez comme suit :

    ![Profil de l’authentification] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Profil de l’authentification")

    1.  Dans la zone de texte **Description** , tapez un nom pour votre configuration.
    2.  Sélectionnez **appliquer l’authentification SAML portail Mimecast personnel**.
    3.  En tant que **fournisseur**, sélectionnez **Azure Active Directory**.
    4.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique sur le portail personnel Mimecast** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte **URL de l’émetteur** .
    5.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique sur le portail personnel Mimecast** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion** .
    6.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique sur le portail personnel Mimecast** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de déconnexion** .  

        >[AZURE.NOTE] La valeur de l’URL de connexion et la valeur d’URL de déconnexion correspondent à la - au portail personnel Mimecast identiques.

    7.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    8.  Ouvrir votre certificat codé base 64 dans le bloc-notes, supprimez la première ligne («*--*») et la dernière ligne («*--*»), copiez le contenu restant de celle-ci dans votre Presse-papiers et collez-le dans la zone de texte **Certificat fournisseur d’identité (métadonnées)** .
    9.  Sélectionnez **Autoriser l’authentification unique sur**.
    10. Cliquez sur **Enregistrer**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter au portail personnel Mimecast, ils doivent être configurées dans le portail personnel Mimecast.  
Dans le cas Mimecast portail personnel, mise en service est une tâche manuelle.
  
Vous devez enregistrer un domaine que vous puissiez créer des utilisateurs.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre **Portail personnel Mimecast** en tant qu’administrateur.

2.  Accédez à **répertoires \> interne**.

    ![Répertoires] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Répertoires")

3.  Cliquez sur **nouveau domaine enregistré**.

    ![Nouveau domaine enregistré] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Nouveau domaine enregistré")

4.  Une fois votre domaine a été créé, cliquez sur **Nouvelle adresse**.

    ![Nouvelle adresse] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "Nouvelle adresse")

5.  Dans la boîte de dialogue nouvelle adresse, effectuez les opérations suivantes :

    ![Enregistrer] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Enregistrer")

    1.  Tapez les attributs **d’Adresse de messagerie**, **Nom globale**, **mot de passe** et **Confirmer le mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser d’autres outils de création de compte utilisateur portail personnel Mimecast ou API fournies par portail personnel Mimecast aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>Pour attribuer aux utilisateurs au portail personnel Mimecast, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration application **Portail personnel Mimecast **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).