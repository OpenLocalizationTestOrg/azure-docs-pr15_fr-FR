<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec 15Five | Microsoft Azure" 
    description="Découvrez comment utiliser 15Five avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Didacticiel : Intégration d’Azure Active Directory avec 15Five

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et 15Five. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement 15Five d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à 15Five sera en mesure d’authentification unique dans l’application sur votre site de société 15Five (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour 15Five
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-15five-tutorial/IC784667.png "Scénario")
##<a name="enabling-the-application-integration-for-15five"></a>L’activation de l’intégration des applications pour 15Five

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour 15Five.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Pour activer l’intégration des applications pour 15Five, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-15five-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **15Five**.

    ![Galerie d’applications] (./media/active-directory-saas-15five-tutorial/IC784668.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **15Five**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à 15Five avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **15Five** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784670.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à 15Five** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784671.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **15Five URL de connexion** , entrez votre URL à l’aide du modèle suivant «*https://company.15Five.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-15five-tutorial/IC784672.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en 15Five** , cliquez sur **télécharger les métadonnées**et puis transférer le fichier de métadonnées à l’équipe de support 15Five.

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784673.png "De configurer l’authentification unique")

    >[AZURE.NOTE] Authentification unique doit être activée par l’équipe de support 15Five.

5.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784674.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à 15Five, ils doivent être configurées dans 15Five.  
Dans le cas 15Five, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **15Five** en tant qu’administrateur.

2.  Accédez à **Gérer société**.

    ![Gérer la société] (./media/active-directory-saas-15five-tutorial/IC784675.png "Gérer la société")

3.  Accédez à **personnes \> ajouter des personnes**.

    ![Personnes] (./media/active-directory-saas-15five-tutorial/IC784676.png "Personnes")

4.  Dans la section Ajouter une personne, procédez comme suit :

    ![Ajouter une nouvelle personne] (./media/active-directory-saas-15five-tutorial/IC784677.png "Ajouter une nouvelle personne")

    1.  Tapez le **prénom**, **Nom**, **titre**, **adresse de messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  Cliquez sur **terminé**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD recevront un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres 15Five utilisateur compte outils de création ou API fournies par 15Five aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Pour affecter des utilisateurs à 15Five, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **15Five **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-15five-tutorial/IC784678.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-15five-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
