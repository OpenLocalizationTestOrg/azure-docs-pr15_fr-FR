<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Concur | Microsoft Azure" 
    description="Découvrez comment utiliser Concur avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Didacticiel : Intégration d’Azure Active Directory avec Concur  


L’objectif de ce didacticiel consiste à afficher l’intégration de Azure et Concur.  
Scénario présenté dans ce didacticiel suppose que vous avez déjà les éléments suivants :

-   Un abonnement Azure valide
-   Un client dans Concur

Scénario présenté dans ce didacticiel se compose de blocs de construction suivantes :

1.  L’activation de l’intégration des applications pour Concur
2.  Configuration de l’authentification unique
3.  Configuration de mise en service de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-concur-tutorial/IC769766.png "Scénario")

>[AZURE.NOTE] La configuration de votre abonnement Concur pour l’authentification unique fédéré via SAML est une tâche distincte, vous devez contacter Concur pour effectuer.

##<a name="enabling-the-application-integration-for-concur"></a>L’activation de l’intégration des applications pour Concur

L’objectif de cette section consiste à créer un plan comment activer l’intégration des applications pour Concur.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Concur, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire dont à activer l’intégration d’annuaire.

3.  Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-concur-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Galerie d’applications**, cliquez sur **Ajouter une application**, puis cliquez sur **Ajouter une application pour mon organisation à utiliser**.

    ![Que voulez-vous faire ?] (./media/active-directory-saas-concur-tutorial/IC700995.png "Que voulez-vous faire ?")

5.  Dans la **zone de recherche**, tapez **Concur**.

    ![Galerie d’applications] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galerie d’applications")

6.  Dans le volet résultats, sélectionnez **Concur**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Approuvent] (./media/active-directory-saas-concur-tutorial/IC721728.png "Approuvent")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section consiste à créer un plan comment permettre aux utilisateurs de s’authentifier à Concur avec leur propre compte dans Azure AD à l’aide de la fédération basée sur le protocole SAML.

>[AZURE.NOTE] La configuration de votre abonnement Concur pour l’authentification unique fédéré via SAML est une tâche distincte, vous devez contacter Concur pour effectuer.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail classique Azure, dans la page de l’intégration **Concur **application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-concur-tutorial/IC769767.png "De configurer l’authentification unique")

2.  Dans la page **Comment souhaitez-vous aux utilisateurs de se connecter à Concur** , sélectionnez **Microsoft Azure AD SSO**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-concur-tutorial/IC769768.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les URL de l’application** , dans la zone de texte **Approuvent URL de connexion** , tapez votre client concur connexion URL, puis cliquez sur **suivant**: 

    ![Configurer connectez-vous URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurer connectez-vous URL")

4.  Dans la page **configuration de l’authentification unique en Concur** , procédez comme suit.

    ![Configurer connectez-vous URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurer connectez-vous URL")

    1.  Cliquez sur Télécharger les métadonnées et safe puis le fichier de données sur votre ordinateur.
    2.  Contactez l’équipe de support Concur pour configurer l’authentification unique pour votre client.
    3.  Sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer session unique** .  

    >[AZURE.NOTE] La configuration de votre abonnement Concur pour l’authentification unique fédéré via SAML est une tâche distincte, vous devez contacter Concur pour effectuer.

##<a name="configuring-user-provisioning"></a>Configuration de mise en service de l’utilisateur

L’objectif de cette section consiste à créer un plan comment activer la mise en service des comptes d’utilisateurs Active Directory Concur.

Pour activer les applications dans le Service de note de frais, il doit être la configuration correcte et l’utilisation d’un profil administrateur de Service Web. Ne pas ajouter simplement le rôle d’administrateur Web pour votre profil administrateur existant que vous utilisez pour les fonctions administratives T & E.

Approuvent Consultants ou l’administrateur client doit créer un profil administrateur du Service Web distinct et l’administrateur Client doit utiliser ce profil pour les fonctions de l’administrateur des Services Web (par exemple, ce qui permet d’applications). Ces profils doivent être séparés à partir quotidienne T & E administrateur profil de l’administrateur de clients (le profil administrateur T & E ne devez pas dotés du rôle de WSAdmin).

Lorsque vous créez le profil à utiliser pour l’activation de l’application, entrez le nom de l’administrateur de clients dans les champs de profil utilisateur. La propriété seront attribués au profil. Une fois que les profils est créé, le client doit se connecter à l’aide de ce profil à cliquer sur le bouton «*Activer*» pour une application partenaire dans le menu de Services Web.

Pour les raisons suivantes, cette action ne doit pas être effectuée avec le profil qu'ils utilisent pour l’administration T & E normale.

1.  Le client doit être celle qui clique sur «*Oui*» dans la fenêtre de la boîte de dialogue qui s’affiche après l’activation d’une application. Cliquez sur reconnaît que le client est prêt pour l’application de partenaire accéder à leurs données, afin que vous ou le partenaire Impossible de cliquer sur ce bouton Oui.
2.  Si un administrateur client qui a activé une application à l’aide de l’administrateur T & E profil quitte l’entreprise (résultant dans le profil été désactivé), toutes les applications activées à l’aide que profil ne fonctionnera pas jusqu'à ce que l’application est activée avec un autre profil administrateur Web actif. C’est pourquoi vous êtes supposé pour créer des profils distincts Web d’administration.
3.  Si un administrateur quitte l’entreprise, le nom correspondant au profil administrateur Web sont modifiables à l’administrateur de remplacement si vous le souhaitez sans ayant un impact sur que l’application activée, car ce profil n’a pas besoin désactivé

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, procédez comme suit :

1.  Connectez-vous à votre client **Concur** .

2.  Dans le menu **Administration** , sélectionnez **Services Web**.

    ![Concur client] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur client")

3.  Sur le côté gauche, dans le volet **Services Web** , sélectionnez **Activer une Application de partenaire**.

    ![Activer l’Application partenaire] (./media/active-directory-saas-concur-tutorial/IC721730.png "Activer l’Application partenaire")

4.  Dans la liste des **Applications activer** , sélectionnez **Azure Active Directory**, puis cliquez sur **Activer**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Cliquez sur **Oui** pour fermer la boîte de dialogue **Confirmer Action** .

    ![Confirmer l’Action] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmer l’Action")

6.  Dans le portail classique Azure, sélectionnez **Concur** dans la liste des applications pour ouvrir la page de la boîte de dialogue **Concur** .

7.  Pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** , cliquez sur **configurer approvisionnement de l’utilisateur**.

8.  Entrez le nom d’utilisateur et le mot de passe de votre administrateur Concur, puis cliquez sur **suivant**.

9.  Pour terminer la configuration, dans la page de **Confirmation** , cliquez sur le bouton **achevé** .

Vous pouvez maintenant créer un compte de test, attendez 10 minutes et vérifiez que le compte a été synchronisé avec Concur.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder aux utilisateurs Azure AD que vous souhaitez autoriser à l’aide de votre accès application en les affectant.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Pour affecter des utilisateurs à Concur, effectuez les opérations suivantes :

1.  Dans le portail classique Azure, créez un compte de test.

2.  Dans la page de l’intégration **Concur **application, cliquez sur **attribuer aux utilisateurs**.

    ![Attribuer aux utilisateurs] (./media/active-directory-saas-concur-tutorial/IC769771.png "Attribuer aux utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre devoir.

    ![Oui] (./media/active-directory-saas-concur-tutorial/IC767830.png "Oui")

Vous devez maintenant attendez 10 minutes et vérifiez que le compte a été synchronisé avec Concur.

Si vous souhaitez tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, voir [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).
