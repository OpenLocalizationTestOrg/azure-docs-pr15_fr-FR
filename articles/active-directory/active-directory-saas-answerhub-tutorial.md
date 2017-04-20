<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AnswerHub | Microsoft Azure" 
    description="Découvrez comment utiliser AnswerHub avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Didacticiel : Intégration d’Azure Active Directory avec AnswerHub

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) d’unique authentification activé

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à AnswerHub sera en mesure d’authentification unique dans l’application sur votre site de société AnswerHub (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour AnswerHub
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scénario")

##<a name="enabling-the-application-integration-for-answerhub"></a>L’activation de l’intégration des applications pour AnswerHub

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour AnswerHub.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Pour activer l’intégration des applications pour AnswerHub, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **AnswerHub**.

    ![Galerie d’applications] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **AnswerHub**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à AnswerHub avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat codé base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **AnswerHub** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à AnswerHub** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **AnswerHub URL de connexion** , entrez votre URL à l’aide du modèle suivant «*https://company.answerhub.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique en AnswerHub** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différente, connectez-vous à votre site d’entreprise AnswerHub en tant qu’administrateur.
    >[AZURE.NOTE] Si vous avez besoin d’aide pour configurer AnswerHub, contactez [l’équipe de support technique de AnswerHub](mailto:success@answerhub.com. ).








6.  Accédez à **Administration**.

7.  Cliquez sur l’onglet **utilisateurs et groupes** .

8.  Dans le volet de navigation sur le côté gauche, dans la section **Paramètres de mise en réseau** , cliquez sur **Le programme d’installation SAML**.

9.  Cliquez sur onglet **IDP Config** .

10. Sous l’onglet **Configuration de la IDP** , effectuez les opérations suivantes :

    ![Programme d’installation SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Programme d’installation SAML")

    1.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en AnswerHub** , copiez la valeur de **l’URL de connexion à distance** et collez-le dans la zone de texte **URL de connexion IDP** .
    2.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en AnswerHub** , copiez la valeur de **l’URL de déconnexion à distance** et collez-le dans la zone de texte **URL de déconnexion IDP** .
    3.  Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en AnswerHub** , copiez la valeur **Nom identificateur Format** et puis collez-les dans la zone de texte **IDP nom identificateur de Format** .
    4.  Cliquez sur **clés et des certificats**.

11. Sous l’onglet clés et les certificats, procédez comme suit :

    ![Clés et des certificats] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Clés et des certificats")

    1.  Créer un fichier **codé en base 64** à partir de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    2.  Ouvrez votre certificat codé base 64 dans le bloc-notes, copiez le contenu de celle-ci dans votre Presse-papiers et puis collez-le dans la zone de texte **Clé publique IDP (x 509 Format)** .
    3.  Cliquez sur **Enregistrer**.

12. Sous l’onglet **Configuration de la IDP** , cliquez sur **Enregistrer**.

13. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à AnswerHub, ils doivent être configurées dans AnswerHub.  
Dans le cas AnswerHub, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **AnswerHub** en tant qu’administrateur.

2.  Accédez à **Administration**.

3.  Cliquez sur l’onglet **utilisateurs et groupes** .

4.  Dans le volet de navigation sur le côté gauche, dans la section **Gérer les utilisateurs** , cliquez sur **créer ou importer des utilisateurs**.

    ![Utilisateurs et groupes] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Utilisateurs et groupes")

5.  Tapez **l’adresse de messagerie**, le **nom d’utilisateur** et le **mot de passe** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liée, puis cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AnswerHub utilisateur compte outils de création ou API fournies par AnswerHub aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Pour affecter des utilisateurs à AnswerHub, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **AnswerHub **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
