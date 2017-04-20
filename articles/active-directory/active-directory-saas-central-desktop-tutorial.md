<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le bureau Central | Microsoft Azure" 
    description="Apprenez à utiliser le bureau Central avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Didacticiel : Intégration d’Azure Active Directory avec le bureau Central

L’objectif de ce didacticiel consiste à afficher l’intégration de bureau Central et Azure. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Une authentification unique Bureau Central abonnement activé / bureau Central du client

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications de bureau Central
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scénario")
##<a name="enabling-the-application-integration-for-central-desktop"></a>L’activation de l’intégration des applications de bureau Central

L’objectif de cette section consiste à créer un plan comment activer l’intégration de l’application de bureau Central.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Pour activer l’intégration de l’application de bureau centrale, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Bureau Central**.

    ![Galerie d’applications] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Bureau centrale**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bureau central] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Bureau central")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier au bureau Central avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 à votre client de bureau Central.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration application de **Bureau centrale** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à un bureau Central** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**: 

    -   Dans la zone de texte **Central bureau URL de connexion** , tapez l’URL de votre client de bureau Central (par exemple : *http://contoso.centraldesktop.com*).
    -   Dans la zone de texte URL de réponse bureau centrale, tapez l’URL AssertionConsumerService Central de votre bureau (par exemple : https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Vous pouvez obtenir la valeur à partir des métadonnées bureau centrale (par exemple : *http://contoso.centraldesktop.com*).

    ![Configurer des URL de l’application] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au bureau Central** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "De configurer l’authentification unique")

5.  Connectez-vous à votre client de **Bureau Central** .

6.  Accédez à **paramètres**, cliquez sur **Avancé**, puis sur **Authentification**.

    ![Configuration - configuration avancée] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Configuration - configuration avancée")

7.  Dans la page **Authentification unique sur paramètres** , procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Paramètres d’authentification unique")

    1.  Sélectionnez **Activer SAML v2 authentification unique**.
    2.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **l’URL de l’émetteur** et collez-le dans la zone de texte **URL de l’authentification unique** .
    3.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion de l’authentification unique** .
    4.  Dans le portail classique Azure, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **l’URL du Service Sign-Out unique** et collez-le dans la zone de texte **URL de déconnexion de l’authentification unique** .

8.  Dans la section **Mode de vérification de Signature de Message** , procédez comme suit :

    ![Méthode de vérification de Signature de message] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Méthode de vérification de Signature de message")

    1.  Sélectionnez le **certificat**.
    2.  Dans la liste des **Certificats de l’authentification unique** , sélectionnez **RSH SHA256**.
    3.  Créer un fichier texte à partir du certificat téléchargé, copiez le contenu du fichier texte et collez-le dans le champ du **Certificat de l’authentification unique** .  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Sélectionnez **Afficher un lien vers votre page de connexion SAMLv2**.

9.  Cliquez sur **mettre à jour**.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Pour les utilisateurs AAD peut se connecter, ils doivent être configurées à l’application de bureau Central. Cette section décrit comment créer des comptes d’utilisateur AAD dans bureau Central.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Mise en service des comptes d’utilisateurs au bureau Central :

1.  Connectez-vous à votre client de bureau Central.

2.  Accédez à **personnes \> membres internes**.

3.  Cliquez sur **Ajouter des membres internes**.

    ![Personnes] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personnes")

4.  Dans la zone de texte **Adresse de messagerie de nouveaux membres** , tapez un compte DAS que vous souhaitez mettre en service, puis cliquez sur **suivant**.

    ![Adresses de messagerie des nouveaux membres] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Adresses de messagerie des nouveaux membres")

5.  Cliquez sur **Ajouter interne ou les membres**.

    ![Ajouter des membres internes] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Ajouter des membres internes")

    >[AZURE.NOTE] Utilisateurs que vous avez ajoutés recevront un message électronique contenant un lien de confirmation que dont elles ont besoin sur lequel cliquer pour activer votre compte.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres bureau Central utilisateur compte outils de création ou API fournies par le bureau Central aux comptes d’utilisateurs AAD mise en service

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Pour attribuer aux utilisateurs au bureau Central, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration application de **Bureau centrale** , cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
