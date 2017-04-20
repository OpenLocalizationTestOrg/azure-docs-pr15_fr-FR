<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lynda.com | Microsoft Azure" 
    description="Découvrez comment utiliser Lynda.com avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Didacticiel : Intégration d’Azure Active Directory avec Lynda.com
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Lynda.com.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Lynda.com
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Lynda.com sera en mesure d’authentification unique dans l’application sur votre site de société Lynda.com (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Lynda.com
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Scénario")
##<a name="enabling-the-application-integration-for-lyndacom"></a>L’activation de l’intégration des applications pour Lynda.com
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Lynda.com.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Lynda.com, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Lynda.com**.

    ![Galerie d’applications] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Lynda.com**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Lynda.com avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

>[AZURE.IMPORTANT]Afin de pouvoir configurer l’authentification unique sur votre client Lynda.com, vous devez contacter le support technique Lynda.com pour accéder à cette fonctionnalité est activée en premier.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Lynda.com** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777526.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Lynda.com** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777527.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Lynda.com URL de connexion** , tapez l’URL de votre client Lynda.com (par exemple : *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Lynda.com** , cliquez sur **télécharger les métadonnées**pour télécharger les métadonnées de votre et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777529.png "De configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à l’équipe de support Lynda.com. L’équipe de support Lynda.com effectue la configuration d’authentification pour vous.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777530.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Lynda.com de l’utilisateur.  
Lorsqu’un utilisateur affecté tente de vous connecter à Lynda.com à l’aide du panneau d’accès, Lynda.com vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Lynda.com.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Lynda.com utilisateur compte outils de création ou API fournies par Lynda.com aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lynda.com, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Lynda.com **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).