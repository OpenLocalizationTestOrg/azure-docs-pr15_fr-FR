<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Dropbox pour les entreprises | Microsoft Azure" 
    description="Découvrez comment utiliser Dropbox pour les entreprises avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Didacticiel : Intégration d’Azure Active Directory avec Dropbox pour les entreprises
  
L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Dropbox pour les entreprises.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client test dans Dropbox pour les entreprises
  
Au terme de ce didacticiel, les utilisateurs Azure AD que vous avez affecté à Dropbox pour les entreprises seront en mesure d’authentification unique dans l’application auprès de votre Dropbox entreprises société site (service fournisseur initialisé par l’ouverture de session) ou à l’aide de [présentation pour le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Dropbox pour les entreprises
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scénario")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>L’activation de l’intégration des applications pour Dropbox pour les entreprises
  
L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Dropbox pour les entreprises.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Dropbox pour les entreprises, procédez comme suit :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** en bas de la page.

    ![Application d’ajouter] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Application d’ajouter")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone de recherche**, tapez **Dropbox pour les entreprises**.

    ![Galerie d’applications] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galerie d’applications")

7.  Dans le volet résultats, sélectionnez **Dropbox pour les entreprises**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Dropbox pour les entreprises] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox pour les entreprises")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Dropbox entreprises avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

Dans le cadre de cette procédure, vous êtes obligé de télécharger un certificat codé base 64 dans votre échange de client entreprise. Si vous n’êtes pas familiarisé avec cette procédure, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration d’application **Dropbox pour les entreprises** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Dropbox pour les entreprises** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , effectuez les opérations suivantes :

    un. Ouverture de session sur votre Dropbox pour client entreprise. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "De configurer l’authentification unique")

    b. Dans le volet de navigation sur le côté gauche, cliquez sur **La Console d’administration**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "De configurer l’authentification unique")

    c. Dans la **Console d’administration**, cliquez sur **authentification** dans le volet de navigation gauche. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "De configurer l’authentification unique")

    d. Dans la section **authentification unique** , sélectionnez **Activer l’authentification unique**, puis cliquez sur **plus** pour développer cette section.  

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "De configurer l’authentification unique")

    e. Copiez l’URL en regard des **utilisateurs de se connecter en entrant leur adresse de messagerie ou qu’ils peuvent accéder directement au**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "De configurer l’authentification unique")

    f. Dans le portail classique Azure, dans la zone URL **DropBox entreprises se connecter** , collez l’URL. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "De configurer l’authentification unique")  



4. Dans la page **configuration de l’authentification unique en Dropbox pour les entreprises** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.  

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "De configurer l’authentification unique")


5. Sur votre Dropbox client entreprise, dans la section **authentification unique** de la page **d’authentification** , procédez comme suit : 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "De configurer l’authentification unique")

    un. Cliquez sur **obligatoire**.

    b. Dans le portail classique Azure, dans la page de dialogue **configuration de l’authentification unique en Dropbox pour les entreprises** , copiez la valeur de **l’URL de la page se connecter** et collez-le dans la zone de texte **se connecter URL** .


    c. Créer un fichier **codé en Base 64** à partir de votre certificat téléchargé. 

    > [AZURE.TIP] Pour plus d’informations, voir [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).


    d. Cliquez sur le bouton **« Certificat choisir »** et naviguez jusqu'à votre **fichier de certificat codé en base 64**.


    e. Cliquez sur le bouton **« Enregistrer les modifications »** pour terminer la configuration de votre DropBox client entreprise.


6. Dans le portail classique Azure, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** . 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "De configurer l’authentification unique")



##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur
  
L’objectif de cette section consiste à créer un plan l’activation d’utilisateur mise en service des comptes d’utilisateurs Active Directory dans Dropbox pour les entreprises.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1. Dans le portail classique Azure, dans la page de l’intégration d’application **Dropbox pour les entreprises** , cliquez sur la **Configuration des utilisateurs de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

2. Sous Activer utilisateur approvisionnement dans DropBox entreprise, cliquez sur Activer approvisionnement de l’utilisateur pour ouvrir la Sign in to Dropbox pour créer un lien avec la boîte de dialogue Azure AD.  

    ![Configuration des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Configuration des utilisateurs")

3. Dans la boîte de dialogue **se connecter à Dropbox pour créer un lien avec Azure Active Directory** , connectez-vous à votre Dropbox client entreprise. 

    ![Configuration des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Configuration des utilisateurs")



4. Cliquez sur **Autoriser** pour accorder Azure AD pour accéder à Dropbox. 

    ![Configuration des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Configuration des utilisateurs")



5. Pour terminer la configuration, cliquez sur le bouton **achevé** .  

    ![Configuration des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Configuration des utilisateurs")




##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Pour affecter des utilisateurs à Dropbox pour les entreprises, procédez comme suit :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration d’application **Dropbox pour les entreprises **, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Oui")
  


Vous devez maintenant attendez 10 minutes et vérifier que le compte a été synchronisé dans Dropbox pour les entreprises.

Vérification d’abord, vous pouvez vérifier l’état de mise à disponibilité, en cliquant sur **tableau de bord** dans la page de l’intégration d’application **Dropbox pour les entreprises** sur le portail classique Azure.

![Attribuer aux utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Attribuer aux utilisateurs")


Un utilisateur terminé cycle de mise en service est indiqué par un statut liés.

![Attribuer aux utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Attribuer aux utilisateurs")


Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès.
Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)