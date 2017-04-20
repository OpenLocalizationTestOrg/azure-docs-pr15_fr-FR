<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AirWatch | Microsoft Azure" 
    description="Découvrez comment utiliser AirWatch avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Didacticiel : Intégration d’Azure Active Directory avec AirWatch

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et AirWatch.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement AirWatch d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à AirWatch sera en mesure d’authentification unique dans l’application sur votre site de société AirWatch (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour AirWatch
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>L’activation de l’intégration des applications pour AirWatch

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour AirWatch.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Pour activer l’intégration des applications pour AirWatch, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **AirWatch**.

    ![Galerie d’applications] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **AirWatch**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à AirWatch avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **AirWatch** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à AirWatch** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se AirWatch d’adresse URL** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application AirWatch (par exemple : «*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en AirWatch** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise AirWatch en tant qu’administrateur.

6.  Dans le volet de navigation gauche, cliquez sur **comptes**, puis cliquez sur **administrateurs**.

    ![Administrateurs] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrateurs")

7.  Développer le menu **paramètres** , puis cliquez sur **Services d’annuaire**.

    ![Paramètres] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Paramètres")

8.  Cliquez sur l’onglet **utilisateur** , dans le champ de texte **Nom de domaine de Base** , tapez votre nom de domaine, puis cliquez sur **Enregistrer**.

    ![Utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utilisateur")

9.  Cliquez sur l’onglet **serveur** .

    ![Serveur] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Serveur")

10. Effectuez les opérations suivantes :

    ![Télécharger] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Télécharger")

    1.  **Type de l’annuaire**, sélectionnez **Aucun**.
    2.  Sélectionnez **utiliser SAML pour l’authentification**.
    3.  Pour télécharger le certificat téléchargé, cliquez sur **Télécharger**.

11. Dans la section **requête** , effectuez les opérations suivantes :

    ![Demander] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Demander")

    1.  **Demander un Type de liaison**, sélectionnez **Publier**.
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Airwatch** , copiez la valeur **Unique authentification URL du Service** et collez-le dans la zone de texte **Identité fournisseur authentification unique d’URL** .
    3.  En tant que **NameID Format**, sélectionnez **Adresse de messagerie**.
    4.  Cliquez sur **Enregistrer**.

12. Cliquez à nouveau sur l’onglet **utilisateur** .

    ![Utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utilisateur")

13. Dans la section **attributs** , effectuez les opérations suivantes :

    ![Attribut] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribut")

    1.  Dans la zone de texte **Identificateur d’objet** , tapez **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Dans la zone de texte **nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Dans la zone de texte **Nom complet** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Dans la zone de texte **nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Dans la zone de texte **Nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Dans la zone de texte de **messagerie** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Cliquez sur **Enregistrer**.

14. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à AirWatch, ils doivent être configurées dans AirWatch.  
Dans le cas AirWatch, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **AirWatch** en tant qu’administrateur.

2.  Dans le volet de navigation sur le côté gauche, cliquez sur **comptes**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utilisateurs")

3.  Dans le menu **utilisateurs** , cliquez sur **Affichage de liste**, puis sur **Ajouter \> ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Ajouter un utilisateur")

4.  Dans la boîte de dialogue **Ajouter / modifier l’utilisateur** , procédez comme suit :

    ![Ajouter un utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Ajouter un utilisateur")

    1.  Tapez le **nom d’utilisateur**, **mot de passe**, **Confirmer le mot de passe**, **prénom**, **Nom**, **Adresse de messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AirWatch utilisateur compte outils de création ou API fournies par AirWatch aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Pour affecter des utilisateurs à AirWatch, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **AirWatch **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
