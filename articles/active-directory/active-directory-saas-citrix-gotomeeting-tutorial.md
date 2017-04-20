<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Citrix GoToMeeting | Microsoft Azure" 
    description="Découvrez comment utiliser Citrix GoToMeeting avec Azure Active Directory pour activer l’authentification unique, automatisé mise en service et bien plus encore !." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Didacticiel : Intégration d’Azure Active Directory avec Citrix GoToMeeting  
S’applique à : Azure

>[AZURE.TIP]Pour obtenir des commentaires, cliquez sur [ici](http://go.microsoft.com/fwlink/?LinkId=522412).

L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Citrix GoToMeeting. Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client dans Citrix GoToMeeting

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Citrix GoToMeeting
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Configuration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>L’activation de l’intégration des applications pour Citrix GoToMeeting

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Citrix GoToMeeting.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Citrix GoToMeeting, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone de recherche**, tapez **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Dans le volet résultats, sélectionnez **Citrix GoToMeeting**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Citrix GoToMeeting avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 à votre client Citrix GoToMeeting.  
Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans la page de l’intégration **Citrix GoToMeeting** application, cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer unique se activé** .

    ![Activer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Activer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Citrix GoToMeeting** , sélectionnez **Microsoft Azure AD SSO**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "De configurer l’authentification unique")


3. Dans la page **Configurer les paramètres de l’application** , cliquez sur **suivant**. 

    ![Activer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Activer l’authentification unique")

4.  Dans la page **configuration de l’authentification unique en Citrix GoToMeeting** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "De configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, connectez-vous à votre centre d’organisation Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Cliquez sur l’onglet **Fournisseur d’identité** et puis effectuez les opérations suivantes :  

    ![Programme d’installation SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Programme d’installation SAML")

    un. Sélectionnez **manuel**

    
    b. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Citrix GoToMeeting** , copiez la valeur de **l’URL de la Page se connecter** et puis collez-les dans la zone de texte **URL de la page se connecter** . 

    
    c. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Citrix GoToMeeting** , copiez la valeur de **l’URL de la Page Sign-Out** et collez-le dans la zone de texte **URL de la page de déconnexion** .

    
    d. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Citrix GoToMeeting** , copiez la valeur de **l’ID de l’entité** et collez-le dans la zone de texte **ID de l’entité identité fournisseur** .

   
    e. Pour télécharger votre certificat téléchargé, cliquez sur **Télécharger le certificat**.

    
    f. Cliquez sur **Enregistrer**.

6.  Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "De configurer l’authentification unique")


7. Dans la page de **confirmation d’authentification unique** , cliquez sur **Terminer**.

    ![Programme d’installation SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Programme d’installation SAML")





##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

L’objectif de cette section consiste à créer un plan comment activer la mise en service des comptes d’utilisateurs Active Directory Citrix GoToMeeting.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Citrix GoToMeeting** application, cliquez sur la **Configuration des utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

    ![Configuration des utilisateurs de configurer] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configuration des utilisateurs de configurer")

2.  Dans la page **paramètres et informations d’identification d’administration** , procédez comme suit :

    ![Configuration des utilisateurs de configurer] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configuration des utilisateurs de configurer")

    un. Dans la zone de texte **Nom d’utilisateur Citrix GoToMeeting** , tapez le nom d’utilisateur d’un administrateur.

    
    b. Dans la zone **Mot de passe administrateur Citrix GoToMeeting** , le mot de passe administrateur.

    
    c. Cliquez sur **suivant**.

3.  Dans la page de **Confirmation** , cliquez sur la coche pour enregistrer votre configuration.

4.  Cliquez sur le bouton **Valider** , pour vérifier la configuration.


##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Pour affecter des utilisateurs à Citrix GoToMeeting, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Citrix GoToMeeting** application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Oui")

Vous devez maintenant attendez 10 minutes et vérifier que le compte a été synchronisé dans Dropbox pour les entreprises.

Vérification d’abord, vous pouvez vérifier l’état de mise à disponibilité, en cliquant sur tableau de bord dans le D sur la page de l’intégration d’application **Citrix GoToMeeting** dans le portail classique Azure.

![Tableau de bord] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Tableau de bord")

Un utilisateur terminé cycle de mise en service est indiqué par un état associé :

![Statut d’intégration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Statut d’intégration")

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès.

Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).
