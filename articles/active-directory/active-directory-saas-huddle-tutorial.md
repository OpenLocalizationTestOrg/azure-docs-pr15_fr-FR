<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant clin de œil | Microsoft Azure" 
    description="Découvrez comment utiliser clin de œil avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Didacticiel : Azure Active Directory intégrant clin de œil
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et clin de œil.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un clin de œil de l’authentification unique activée abonnement
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à clin de œil seront en mesure d’authentification unique dans l’application sur votre site de société clin de œil (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour clin de œil
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurer l’authentification unique")
##<a name="enabling-the-application-integration-for-huddle"></a>L’activation de l’intégration des applications pour clin de œil
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour clin de œil.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Pour activer l’intégration des applications pour clin de œil, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **clin de œil**.

    ![Galerie d’applications] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **clin de œil**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clin de œil] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Clin de œil")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à clin de œil avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page **clin de œil** de l’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à clin de œil** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL application** , dans la zone de texte **Se clin de œil d’adresse URL** , tapez l’URL de votre client clin de œil à l’aide du modèle suivant «*http://company.huddle.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en clin de œil** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurer l’authentification unique")

    1.  Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
    2.  Copiez la valeur de **l’URL de l’émetteur** , la valeur de **l’URL de l’authentification unique SAML** et le certificat téléchargé et puis de les envoyer à l’équipe de support clin de œil.

    >[AZURE.NOTE] Authentification unique doit être activée par l’équipe de support clin de œil.
Vous recevez une notification lorsque la configuration est terminée.

5.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à clin de œil, ils doivent être mis en service dans clin de œil.  
Dans le cas de clin de œil, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société **clin de œil** en tant qu’administrateur.

2.  Cliquez sur **espace de travail**.

3.  Cliquez sur **personnes \> inviter des personnes**.

    ![Personnes] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Personnes")

4.  Dans la section **créer une nouvelle invitation** , effectuez les opérations suivantes :

    ![Nouvelle Invitation] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Nouvelle Invitation")

    1.  Dans la liste **Choisir une équipe pour inviter des personnes à rejoindre** , sélectionnez **équipe**.
    2.  Tapez l' **Adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans la zone de texte liée.
    3.  Cliquez sur **inviter**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD recevront un message électronique ainsi qu’un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres clin de œil utilisateur compte outils de création ou API fournies par clin de œil mise en service DAS les comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Pour affecter des utilisateurs à clin de œil, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page **clin de œil **de l’intégration d’application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).