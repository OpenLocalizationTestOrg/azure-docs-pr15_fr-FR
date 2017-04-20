<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Learningpool | Microsoft Azure" 
    description="Découvrez comment utiliser Learningpool avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Didacticiel : Intégration d’Azure Active Directory avec Learningpool
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Learningpool.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Learningpool d’unique authentification activé
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Learningpool sera en mesure d’authentification unique dans l’application sur votre site de société Learningpool (service fournisseur initialisé par l’ouverture de session), ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Learningpool
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scénario")
##<a name="enabling-the-application-integration-for-learningpool"></a>L’activation de l’intégration des applications pour Learningpool
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Learningpool.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Learningpool, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Learningpool**.

    ![Galerie d’applications] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Learningpool**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Learningpool avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.
  
Votre application Learningpool attend les assertions SAML dans un format spécifique, qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **attributs jeton saml** .  
La capture d’écran suivante montre un exemple de ce.

![Attributs jetons SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Attributs jetons SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Learningpool** , dans le menu dans la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs jeton SAML** .

    ![Attributs] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributs")

2.  Pour ajouter les mappages de l’attribut requis, procédez comme suit :

    ###  

  	|Nom de l’attribut                |Valeur de l’attribut            |
  	|------------------------------|---------------------------|

     urn : oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn : oid:2.5.4.42|User.GivenName   
  	|urn : oid:0.9.2342.19200300.100.1.3|User.Mail
  	|urn : oid:2.5.4.4|User.Surname

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut affiché pour cette ligne.
    3.  Dans la liste **Valeur de l’attribut** , sélectionnez la valeur de l’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

3.  Cliquez sur **appliquer les modifications**.

4.  Dans votre navigateur, cliquez sur **précédent** pour ouvrir la boîte de dialogue de **Démarrage rapide** de nouveau.

5.  Cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![Configurer Singel authentification] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurer Singel authentification")

6.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Learningpool** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurer l’authentification unique")

7.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Se Learningpool d’adresse URL** , tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Learningpool (par exemple : https://parliament.preview.learningpool.com/auth/shibboleth/index.php), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurer des URL de l’application")

8.  Dans la page **configuration de l’authentification unique en Learningpool** , pour télécharger les métadonnées de votre, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurer l’authentification unique")

9.  Transférer ce fichier de métadonnées à votre équipe de Learningpool Support.

    >[AZURE.NOTE]Authentification unique doit être activée par l’équipe de support Learningpool.

10. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
Afin de permettre aux utilisateurs Azure AD pour vous connecter à Learningpool, ils doivent être configurées dans Learningpool.
  
Il n’existe aucun élément action pour vous permettent de configurer approvisionnement à Learningpool de l’utilisateur.  
Les utilisateurs doivent être créés par l’équipe de support Learningpool.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Learningpool utilisateur compte outils de création ou API fournies par Learningpool aux comptes d’utilisateurs AAD mise en service.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Pour affecter des utilisateurs à Learningpool, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Learningpool **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).