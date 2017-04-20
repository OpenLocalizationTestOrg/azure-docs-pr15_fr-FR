<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Benefitsolver | Microsoft Azure"
    description="Découvrez comment utiliser Benefitsolver avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Didacticiel : Intégration d’Azure Active Directory avec Benefitsolver

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Benefitsolver.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Benefitsolver d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Benefitsolver sera en mesure d’authentification unique dans l’application à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Benefitsolver
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scénario")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>L’activation de l’intégration des applications pour Benefitsolver

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Benefitsolver.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Benefitsolver, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Benefitsolver**.

    ![Galerie d’applications] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Benefitsolver**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Benefitsolver avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Votre application Benefitsolver attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Benefitsolver** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Benefitsolver** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres de l’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurer les paramètres de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez **http://azure.benefitsolver.com**.
    2.  Dans la zone de texte **URL de réponse** , tapez **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Cliquez sur **suivant**.

4.  Dans la page **configuration de l’authentification unique en Benefitsolver** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre équipe de support Benefitsolver.

    >[AZURE.NOTE] L’équipe de support Benefitsolver doit effectuer la configuration de l’authentification unique réelle.
Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurer l’authentification unique")

7.  Dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributs")

8.  Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|Identifiant du client|Vous devez obtenir cette valeur à partir de votre équipe de support technique Benefitsolver.|
  	|ClientKey|Vous devez obtenir cette valeur à partir de votre équipe de support technique Benefitsolver.|
  	|LogoutURL|Vous devez obtenir cette valeur à partir de votre équipe de support technique Benefitsolver.|
  	|EmployeeID|Vous devez obtenir cette valeur à partir de votre équipe de support technique Benefitsolver.|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    3.  Dans la zone de texte **Valeur de l’attribut** , sélectionnez la valeur de l’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

9.  Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Benefitsolver, ils doivent être configurées dans Benefitsolver.  
Dans le cas Benefitsolver, les données des employés sont dans votre application remplie via un fichier recensement depuis votre système de ressources humaines (généralement nuit).  

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Benefitsolver utilisateur compte outils de création ou API fournies par Benefitsolver aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Pour affecter des utilisateurs à Benefitsolver, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Benefitsolver **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
