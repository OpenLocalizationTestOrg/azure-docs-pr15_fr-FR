<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Chromeriver | Microsoft Azure" 
    description="Découvrez comment utiliser Chromeriver avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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


#<a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Didacticiel : Intégration d’Azure Active Directory avec Chromeriver

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Chromeriver.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Chromeriver d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Chromeriver sera en mesure d’authentification unique dans l’application sur votre site de société Chromeriver (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Chromeriver
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scénario")
##<a name="enabling-the-application-integration-for-chromeriver"></a>L’activation de l’intégration des applications pour Chromeriver

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Chromeriver.

###<a name="to-enable-the-application-integration-for-chromeriver-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Chromeriver, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Chromeriver**.

    ![Galerie d’applications] (./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Chromeriver**, puis cliquez sur **Terminer** pour ajouter l’application.
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Chromeriver avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Chromeriver** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Chromeriver** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configurer les paramètres de l’application")

    1.  Dans la zone de texte **URL de réponse** , tapez votre Chromeriver **AssertionConsumerService URL** (par exemple : *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  

        >[AZURE.NOTE] Vous pouvez obtenir cette valeur à partir de votre équipe de support technique Chromeriver.

    2.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en Chromeriver** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre équipe de support Chromeriver.

    >[AZURE.NOTE]L’équipe de support Chromeriver doit effectuer la configuration de l’authentification unique réelle.  
    Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Chromeriver, ils doivent être configurées dans Chromeriver.  
Dans le cas de Chromeriver, les comptes d’utilisateurs doivent être créés par l’équipe de support Chromeriver.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Chromeriver utilisateur compte outils de création ou API fournies par Chromeriver mise en service Azure Active Directory aux comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-chromeriver-perform-the-following-steps"></a>Pour affecter des utilisateurs à Chromeriver, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Chromeriver **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
