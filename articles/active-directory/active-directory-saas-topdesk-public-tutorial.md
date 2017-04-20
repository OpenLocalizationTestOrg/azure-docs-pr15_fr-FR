<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - Public | Microsoft Azure" 
    description="Découvrez comment utiliser TOPdesk - Public avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Didacticiel : Intégration d’annuaire Azure avec TOPdesk - Public

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et TOPdesk - Public.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un TOPdesk - Public de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à TOPdesk - Public seront en mesure d’authentification unique dans l’application auprès de votre TOPdesk - site Public société (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour TOPdesk - Public
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scénario")

##<a name="enabling-the-application-integration-for-topdesk---public"></a>L’activation de l’intégration des applications pour TOPdesk - Public
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour TOPdesk - Public.

###<a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour TOPdesk - Public, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **TOPdesk - Public**.

    ![Galerie d’applications] (./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **TOPdesk - Public**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TOPdesk Public] (./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier sur TOPdesk - Public avec leur propre compte dans Azure Active Directory à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour TOPdesk - Public, vous devez télécharger un fichier icône logo. Pour obtenir le fichier d’icône, contactez l’équipe de support TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - publique** en tant qu’administrateur.

2.  Dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Paramètres")

3.  Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Paramètres de connexion")

4.  Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Général")

5.  Dans la section **Public** de la section configuration **login SAML** , effectuez les opérations suivantes :

    ![Paramètres techniques] (./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Paramètres techniques")

    1.  Cliquez sur **Télécharger** pour télécharger le fichier de métadonnées public et enregistrez-le localement sur votre ordinateur.
    2.  Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService** .
        ![AssertionConsumerService] (./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copiez la valeur **AssertionConsumerService** .  

        >[AZURE.NOTE] Vous avez besoin de la valeur dans la section **Configurer l’URL application** plus loin dans ce didacticiel.

6.  Dans une fenêtre de navigateur web différente, connectez-vous à votre **portail classique Azure** en tant qu’administrateur.

7.  Dans la page de l’intégration d’application **TOPdesk - Public** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurer l’authentification unique")

8.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à TOPdesk - Public** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurer l’authentification unique")

9.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **TOPdesk - signe sur URL publique** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre TOPdesk - application Public (par exemple : «*https://qssolutions.topdesk.net*»).
    2.  Dans la zone de texte **TOPdesk – URL publique de réponse** , collez le **TOPdesk - URL AssertionConsumerService publique** (par exemple : «*https://qssolutions.topdesk.net/tas/public/login/saml*»)
    3.  Cliquez sur **suivant**.

10. Dans la page **configuration de l’authentification unique en TOPdesk - Public** , cliquez sur **télécharger les métadonnées**pour télécharger votre fichier de métadonnées et puis enregistrez le fichier localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurer l’authentification unique")

11. Pour créer un fichier de certificat, effectuez les opérations suivantes :

    ![Certificat] (./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificat")

    1.  Ouvrez le fichier téléchargé métadonnées.
    2.  Développez le nœud **RoleDescriptor** qui comporte une **xsi : type** de **fed : ApplicationServiceType**.
    3.  Copiez la valeur du nœud **X509Certificate** .
    4.  Enregistrer la valeur **X509Certificate** copiée localement sur votre ordinateur dans un fichier.

12. Dans votre TOPdesk - site Public société, dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Paramètres")

13. Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Paramètres de connexion")

14. Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Général")

15. Dans la section **Public** , cliquez sur **Ajouter**.

    ![Connexion SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "Connexion SAML")

16. Dans la page de la boîte de dialogue **assistant de configuration SAML** , effectuez les opérations suivantes :

    ![Assistant de Configuration SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "Assistant de Configuration SAML")

    1.  Pour télécharger votre fichier de métadonnées téléchargé, sous les **Métadonnées de fédération**, cliquez sur **Parcourir**.
    2.  Pour télécharger votre fichier de certificat, sous **Certificat (RSA)**, cliquez sur **Parcourir**.
    3.  Pour télécharger le fichier du logo que vous avez obtenu à partir de l’équipe de support TOPdesk, sous l' **icône du Logo**, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **attribut de nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Dans la zone de texte **nom complet** , tapez un nom pour votre configuration.
    6.  Cliquez sur **Enregistrer**.

17. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à TOPdesk - Public, ils doivent être mis en service dans TOPdesk - Public.  
Dans le cas de TOPdesk - Public, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - publique** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **TOPdesk \> nouveau \> fichiers de prise en charge \> personne**.

    ![Personne] (./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Personne")

3.  Dans la boîte de dialogue nouvelle personne, procédez comme suit :

    ![Nouvelle personne] (./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Nouvelle personne")

    1.  Cliquez sur l’onglet Général.
    2.  Dans la zone de texte Nom, tapez le nom d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    3.  Sélectionnez un **Site** pour le compte.
    4.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre TOPdesk - outils de création de compte utilisateur publiques ou API fournies par TOPdesk - Public aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Pour attribuer aux utilisateurs TOPdesk - Public, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **TOPdesk - Public **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).