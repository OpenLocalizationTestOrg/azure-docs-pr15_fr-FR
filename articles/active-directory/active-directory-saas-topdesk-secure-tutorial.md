<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - sécurisé | Microsoft Azure"
    description="Découvrez comment utiliser TOPdesk - Secure avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - sécurisé
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et TOPdesk - sécurisé.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un TOPdesk - sécurisée de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à TOPdesk - sécurisé sera en mesure d’authentification unique dans l’application auprès de votre TOPdesk - site société sécurisés (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour TOPdesk - sécurisé
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scénario")

##<a name="enabling-the-application-integration-for-topdesk---secure"></a>L’activation de l’intégration des applications pour TOPdesk - sécurisé
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour TOPdesk - sécurisé.

###<a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Pour activer l’intégration des applications pour TOPdesk - banque d’informations sécurisé, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **TOPdesk - sécurisé**.

    ![Galerie d’applications] (./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **TOPdesk - sécurisé**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TOPdesk - sécurisé] (./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - sécurisé")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section est hiérarchique comment permettre aux utilisateurs de s’authentifier sur TOPdesk - Secure avec leur propre compte dans Azure Active Directory à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour TOPdesk - sécurisé, vous devez télécharger un fichier icône logo. Pour obtenir le fichier d’icône, contactez l’équipe de support TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - sécurisé** en tant qu’administrateur.

2.  Dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

3.  Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

4.  Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

5.  Dans la section **banque d’informations** de la section configuration **login SAML** , effectuez les opérations suivantes :

    ![Paramètres techniques] (./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Paramètres techniques")

    1.  Cliquez sur **Télécharger** pour télécharger le fichier de métadonnées public et enregistrez-le localement sur votre ordinateur.
    2.  Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService** .
        ![Service clientèle assertion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Service clientèle assertion")
    3.  Copiez la valeur **AssertionConsumerService** .  

        >[AZURE.NOTE] Vous avez besoin de la valeur dans la section **Configurer l’URL application** plus loin dans ce didacticiel.

6.  Dans une fenêtre de navigateur web différente, connectez-vous à votre **portail classique Azure** en tant qu’administrateur.

7.  Dans la page de l’intégration d’application **TOPdesk - sécurisé** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurer l’authentification unique")

8.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à TOPdesk - sécurisé** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurer l’authentification unique")

9.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **TOPdesk - Secure se d’URL** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre TOPdesk - application sécurisé (par exemple : «*https://qssolutions.topdesk.net*»).
    2.  Dans la zone de texte **TOPdesk – URL publique de réponse** , collez le **TOPdesk - Secure AssertionConsumerService URL** (par exemple : «*https://qssolutions.topdesk.net/tas/public/login/saml*»)
    3.  Cliquez sur **suivant**.

10. Dans la page **configuration de l’authentification unique en TOPdesk - sécurisé** , cliquez sur **télécharger les métadonnées**pour télécharger votre fichier de métadonnées et puis enregistrez le fichier localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurer l’authentification unique")

11. Pour créer un fichier de certificat, effectuez les opérations suivantes :

    ![Certificat] (./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificat")

    1.  Ouvrez le fichier de métadonnées téléchargés.
    2.  Développez le nœud **RoleDescriptor** qui comporte une **xsi : type** de **fed : ApplicationServiceType**.
    3.  Copiez la valeur du nœud **X509Certificate** .
    4.  Enregistrer la valeur **X509Certificate** copiée localement sur votre ordinateur dans un fichier.

12. Dans votre TOPdesk - site société sécurisés, dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

13. Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

14. Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

15. Dans la section **Public** , cliquez sur **Ajouter**.

    ![Ajouter] (./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Ajouter")

16. Dans la page de la boîte de dialogue **assistant de configuration SAML** , effectuez les opérations suivantes :

    ![Assistant de Configuration SAML] (./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistant de Configuration SAML")

    1.  Pour télécharger votre fichier de métadonnées téléchargé, sous les **Métadonnées de fédération**, cliquez sur **Parcourir**.
    2.  Pour télécharger votre fichier de certificat, sous **Certificat (RSA)**, cliquez sur **Parcourir**.
    3.  Pour télécharger le fichier du logo que vous avez obtenu à partir de l’équipe de support TOPdesk, sous l' **icône du Logo**, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **attribut de nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Dans la zone de texte **nom complet** , tapez un nom pour votre configuration.
    6.  Cliquez sur **Enregistrer**.

17. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à TOPdesk - sécurisé, elles doivent être configurées dans TOPdesk - sécurisé.  
Dans le cas de TOPdesk - sécurisé, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - sécurisé** en tant qu’administrateur.

2.  Dans le menu dans la partie supérieure, cliquez sur **TOPdesk \> nouveau \> fichiers de prise en charge \> opérateur**.

    ![Opérateur] (./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Opérateur")

3.  Dans la boîte de dialogue **Nouvel opérateur** , effectuez les opérations suivantes :

    ![Nouvel opérateur] (./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Nouvel opérateur")

    1.  Cliquez sur l’onglet Général.
    2.  Dans la zone de texte **nom** de la section **Général** , tapez le nom d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    3.  Dans la section **emplacement** , sélectionnez un **Site** pour le compte.
    4.  Dans la zone de texte **Nom de connexion** de la section **TOPdesk connexion** , tapez un nom pour votre utilisateur.
    5.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre TOPdesk - outils de création de compte utilisateur sécurisée ou API fournies par TOPdesk - sécurisée mise en service des comptes d’utilisateurs AAD.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Pour affecter des utilisateurs à TOPdesk - banque d’informations sécurisé, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **TOPdesk - sécurisé **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).