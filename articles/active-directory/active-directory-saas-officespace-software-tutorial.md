<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel OfficeSpace | Microsoft Azure" 
    description="Découvrez comment utiliser les logiciels OfficeSpace avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Didacticiel : Intégration d’Azure Active Directory avec un logiciel de OfficeSpace
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et OfficeSpace logiciel.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un logiciel de OfficeSpace de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez attribué un logiciel OfficeSpace sera en mesure d’authentification unique dans l’application sur votre site de société OfficeSpace logiciel (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour les logiciels OfficeSpace
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scénario")
##<a name="enabling-the-application-integration-for-officespace-software"></a>L’activation de l’intégration des applications pour les logiciels OfficeSpace
  
L’objectif de cette section est hiérarchique comment activer l’intégration des applications pour OfficeSpace logiciel.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Pour activer l’intégration des applications pour OfficeSpace logiciel, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **OfficeSpace logiciel**.

    ![Galerie d’applications] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **OfficeSpace logiciel**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Logiciel OfficeSpace] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "Logiciel OfficeSpace")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier au logiciel OfficeSpace avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour les logiciels OfficeSpace, vous devez récupérer une valeur de l’empreinte numérique d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Logiciel OfficeSpace** application, cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique = sur] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurer l’authentification unique = sur")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à OfficeSpace logiciel** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **OfficeSpace logiciel se d’URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application logicielle OfficeSpace (par exemple : «*https://company.officespacesoftware.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique chez OfficeSpace Software** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise OfficeSpace logiciel en tant qu’administrateur.

6.  Accédez à **administrateur \> connecteurs**.

    ![Administrateur] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrateur")

7.  Cliquez sur **autorisation SAML**.

    ![Connecteurs] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connecteurs")

8.  Dans la section **SAML d’autorisation** , procédez comme suit :

    ![Configuration SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuration SAML")

    1.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique chez OfficeSpace Software** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **url du fournisseur de déconnexion** .
    2.  Dans le portail classique Azure, dans la page de boîte de dialogue **configuration de l’authentification unique chez OfficeSpace Software** , copiez la valeur **URL déconnexion à distance** et collez-le dans la zone de texte **url de Client idp cible** .
    3.  Copiez la valeur de **l’empreinte numérique** du certificat à exporter et collez-le dans la zone de texte **empreinte de certificat Client idp** .  

        >[AZURE.TIP]
        Pour plus d’informations, voir [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    4.  Cliquez sur **Enregistrer les paramètres**.

9.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à OfficeSpace logiciel, ils doivent être configurées dans OfficeSpace logiciel. Dans le cas OfficeSpace logiciel, la mise en service est une tâche automatique.  
Il n’existe aucun élément action à votre place.  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous authentification unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres logiciel OfficeSpace utilisateur compte outils de création ou API fournies par le logiciel OfficeSpace aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Pour attribuer aux utilisateurs au logiciel OfficeSpace, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Logiciel OfficeSpace **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).