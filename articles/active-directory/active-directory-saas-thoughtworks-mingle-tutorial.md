<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant Thoughtworks rencontrer | Microsoft Azure" 
    description="Découvrez comment utiliser Thoughtworks rencontrer avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Didacticiel : Azure Active Directory intégrant Thoughtworks rencontrer
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Thoughtworks rencontrer.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client Thoughtworks rencontrer
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Thoughtworks rencontrer
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scénario")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>L’activation de l’intégration des applications pour Thoughtworks rencontrer
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Thoughtworks rencontrer.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Thoughtworks rencontrer, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **thoughtworks rencontrer**.

    ![Galerie d’applications] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Thoughtworks rencontrer**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ThoughtWorks rencontrer] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "ThoughtWorks rencontrer")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier Thoughtworks rencontrer avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat Thoughtworks rencontrer.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page **Thoughtworks rencontrer **de l’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs d’ouverture de session Thoughtworks rencontrer** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Thoughtworks rencontrer client URL** , tapez l’URL de votre selon le modèle suivant «*http://company.mingle.thoughtworks.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en Thoughtworks rencontrer** , cliquez sur téléchargement métadonnées et enregistrez-le sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "De configurer l’authentification unique")

5.  Connectez-vous au site de votre société **Se Thoughtworks** en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin** , puis, cliquez sur **Configuration de l’authentification unique**.

    ![Configuration de l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuration de l’authentification unique")

7.  Dans la section **Configuration de l’authentification unique** , procédez comme suit :

    ![Configuration de l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuration de l’authentification unique")

    1.  Pour télécharger le fichier de métadonnées, cliquez sur **Choisir un fichier**.
    2.  Cliquez sur **Enregistrer les modifications**.

8.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Pour les utilisateurs AAD peut se connecter, ils doivent être configurées à l’application Thoughtworks rencontrer à l’aide de leur nom d’utilisateur Azure Active Directory.  
Dans le cas de Thoughtworks rencontrer, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous au site de votre société se Thoughtworks en tant qu’administrateur.

2.  Cliquez sur **le profil**.

    ![Votre premier projet] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Votre premier projet")

3.  Cliquez sur l’onglet **Admin** , puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Nouvel utilisateur] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Nouvel utilisateur")

5.  Dans la page de la boîte de dialogue **Nouvel utilisateur** , procédez comme suit :

    ![Nouvel utilisateur] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Nouvel utilisateur")

    1.  Tapez le **nom d’utilisateur**, **nom d’affichage**, **Choisir le mot de passe**, **Confirmer le mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liée.
    2.  En tant que **type d’utilisateur**, sélectionnez **utilisateur complet**.
    3.  Cliquez sur **créer ce profil**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Thoughtworks rencontrer utilisateur compte outils de création ou API fournies par Thoughtworks rencontrer aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Pour attribuer aux utilisateurs Thoughtworks rencontrer, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page **Se Thoughtworks** de l’intégration d’application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
