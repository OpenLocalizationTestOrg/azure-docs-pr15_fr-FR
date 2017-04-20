<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Cherwell | Microsoft Azure" 
    description="Découvrez comment utiliser Cherwell avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Didacticiel : Intégration d’Azure Active Directory avec Cherwell

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Cherwell. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un Cherwell de l’authentification unique activée abonnement

Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Cherwell sera en mesure d’authentification unique dans l’application sur votre site de société Cherwell (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Cherwell
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scénario")
##<a name="enabling-the-application-integration-for-cherwell"></a>L’activation de l’intégration des applications pour Cherwell

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Cherwell.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Cherwell, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Dans le volet résultats, sélectionnez **Cherwell**, puis cliquez sur **Terminer** pour ajouter l’application.
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Cherwell avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Cherwell** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Cherwell** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurer des URL de l’application")

    un.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre **Cherwell** (par exemple : *https://\<nom de la société\>.cherwellondemand.com/cherwellclient*).

    b.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique en Cherwell** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurer l’authentification unique")

    un.  Cliquez sur **Télécharger le certificat**, puis enregistrez le certificat localement sur votre ordinateur.

    b.  Copiez l' **URL du fournisseur d’identité**.

    c.  Copiez l' **URL du Service d’authentification unique**.

    d.  Cliquez sur **suivant**.

5.  Soumettre le certificat téléchargé, l' **URL du fournisseur d’identité** et l' **URL du Service unique authentification** à votre équipe de support Cherwell.

    >[AZURE.NOTE] L’équipe de support Cherwell doit effectuer la configuration de l’authentification unique réelle.
Vous recevez une notification lorsque l’authentification unique a été activé pour votre abonnement.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

Afin de permettre aux utilisateurs Azure AD pour vous connecter à Cherwell, ils doivent être configurées dans Cherwell.  
Dans le cas de Cherwell, les comptes d’utilisateurs doivent être créés par l’équipe de support Cherwell.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Cherwell utilisateur compte outils de création ou API fournies par Cherwell mise en service Azure Active Directory aux comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Pour affecter des utilisateurs à Cherwell, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Cherwell** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
