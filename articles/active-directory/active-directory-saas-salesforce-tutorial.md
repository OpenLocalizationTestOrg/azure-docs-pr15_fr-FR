<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Salesforce | Microsoft Azure"
    description="Découvrez comment utiliser Salesforce avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Didacticiel : Comment intégrer Salesforce Azure Active Directory

Ce didacticiel montre comment connecter votre environnement Salesforce à votre Azure Active Directory. Vous allez apprendre comment configurer l’authentification unique à force de vente, comment activer la mise en service automatisé utilisateur et comment affecter des utilisateurs à accéder à force de vente.

##<a name="prerequisites"></a>Conditions préalables

1. Pour accéder à Azure Active Directory via le [portail classique Azure](https://manage.windowsazure.com), vous devez tout d’abord un abonnement Azure valide.

2. Vous devez disposer de client valide dans [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Si vous utilisez un compte **d’évaluation** Salesforce.com, vous serez Impossible de configurer la mise en service automatisé utilisateur. Comptes d’évaluation n’ont pas l’accès à l’API nécessaires activé jusqu'à ce qu’ils sont achetées.
> 
> Vous pouvez contourner cette limitation en utilisant un [compte de développeur gratuit](https://developer.salesforce.com/signup) pour effectuer ce didacticiel.

Si vous utilisez un environnement Salesforce Sandbox, consultez le [didacticiel d’intégration Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Didacticiels vidéo

Vous pouvez suivre ce didacticiel à l’aide de la vidéo ci-dessous.

**Didacticiel vidéo première partie : procédure d’activation de l’authentification unique**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Didacticiel vidéo 2è partie : Comment automatiser la configuration des utilisateurs**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Étape 1 : Ajouter Salesforce à votre répertoire

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Sélectionnez Active Directory dans le volet de navigation gauche.][0]

2. Dans la liste **répertoire** , sélectionnez le répertoire que vous voulez ajouter Salesforce à.

3. Cliquez sur **Applications** dans le menu supérieur.

    ![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la **zone de recherche**, tapez **Salesforce**. Puis sélectionnez **Salesforce** dans les résultats, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Ajoutez Salesforce.][4]

7. Vous devez maintenant voir la page de démarrage rapide pour Salesforce :

    ![Page de démarrage rapide de Salesforce dans Azure Active Directory][5]

##<a name="step-2-enable-single-sign-on"></a>Étape 2 : Activer l’ouverture de session unique

1. Avant de pouvoir configurer l’authentification unique, vous devez configurer et déployer un domaine personnalisé pour votre environnement Salesforce. Pour obtenir des instructions sur la façon de procéder, voir [Définir l’un nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Dans Quick Start page de Salesforce Azure AD, cliquez sur le bouton **de configurer l’authentification unique** .

    ![Configurer un seul authentification bouton][6]

3. Une boîte de dialogue s’ouvre et vous verrez un écran vous demandant « Comment souhaitez-vous aux utilisateurs de se connecter à Salesforce ? » Sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Sélectionnez authentification Azure AD unique][7]

    > [AZURE.NOTE] En savoir plus sur les différentes unique authentification options, [Cliquez ici](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Dans la page **Configurer les paramètres de l’application** , remplissez **Signe d’URL** en tapant votre URL de domaine Salesforce au format suivant :
 - Compte d’entreprise :`https://<domain>.my.salesforce.com`
 - Compte développeur :`https://<domain>-dev-ed.my.salesforce.com` 

    ![Type d’authentification votre URL][8]

5. Dans la page **configuration de l’authentification unique à force de vente** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Télécharger le certificat][9]

6. Ouvrir un nouvel onglet dans votre navigateur et connectez-vous à votre compte d’administrateur Salesforce.

7. Sous le volet de navigation **administrateur** , cliquez sur les **Contrôles de sécurité** pour développer la section connexe. Cliquez ensuite sur **Paramètres d’authentification unique**.

    ![Cliquez sur paramètres d’authentification unique sous contrôles de sécurité][10]

8. Dans la page **Paramètres d’authentification unique** , cliquez sur le bouton **Modifier** .

    ![Cliquez sur le bouton Modifier][11]

    > [AZURE.NOTE] Si vous ne parvenez pas à activer les paramètres de l’authentification unique pour votre compte Salesforce, vous devrez peut-être contacter le support technique de Salesforce afin que la fonction activée pour vous.

9. Sélectionnez **SAML activé**, puis cliquez sur **Enregistrer**.

    ![Sélectionnez SAML activé][12]

10. Pour configurer votre SAML unique authentification des paramètres, cliquez sur **Nouveau**.

    ![Sélectionnez SAML activé][13]

11. Dans la page **SAML unique authentification paramètre modifier** , vérifiez les configurations suivantes :

    ![Capture d’écran des configurations que vous devez prendre][14]

 - Pour le champ **nom** , tapez un nom convivial pour cette configuration. Fournir une valeur pour **nom** remplir automatiquement la zone de texte **Nom de l’API** .

 - Dans Azure AD, copiez la valeur de **l’URL de l’émetteur** et collez-le dans le champ de **l’émetteur** dans Salesforce.

 - Dans la **zone de texte Id de l’entité**, tapez votre nom de domaine Salesforce selon le modèle suivant :
     - Compte d’entreprise :`https://<domain>.my.salesforce.com`
     - Compte développeur :`https://<domain>-dev-ed.my.salesforce.com`

 - Cliquez sur **Parcourir** ou **Choisir un fichier** pour ouvrir la boîte de dialogue **Choisir un fichier à télécharger** , sélectionnez votre certificat Salesforce, puis cliquez sur **Ouvrir** pour télécharger le certificat.

 - Pour **Le Type d’identité SAML**, sélectionnez **Assertion contient salesforce.com de l’utilisateur**.

 - Pour l' **Emplacement d’identité SAML**, sélectionnez **identité est dans l’élément NameIdentifier de l’instruction objet**

 - Dans Azure AD, copiez la valeur de **l’URL de connexion à distance** et collez-le dans le champ **URL Login du fournisseur d’identité** dans Salesforce.

 - Pour le **Service fournisseur initialisé par demande de liaison**, sélectionnez **Redirection HTTP**.

 - Pour finir, cliquez sur **Enregistrer** pour appliquer votre SAML authentification paramètres unique.

12. Dans le volet de navigation gauche dans Salesforce, cliquez sur **Gestion des domaines** pour développer la section, puis cliquez sur **Mon domaine**.

    ![Cliquez sur mon domaine][15]

13. Faites défiler jusqu'à la section **Configuration de l’authentification** , puis cliquez sur le bouton **Modifier** .

    ![Cliquez sur le bouton Modifier][16]

14. Dans la section **Service d’authentification** , sélectionnez le nom convivial de votre configuration SAML SSO, puis cliquez sur **Enregistrer**.

    ![Sélectionnez votre configuration de l’authentification unique][17]

    > [AZURE.NOTE] Si plusieurs service d’authentification est sélectionné, puis lorsque les utilisateurs tentent de démarrer une conversation de l’authentification unique à votre environnement Salesforce, il devra pour sélectionner le service d’authentification ils souhaiteraient pour vous connecter à l’aide. Si vous ne voulez pas cela se produise, vous devez **Quitter tous les autres services d’authentification désactivées**.

15. Dans Azure Active Directory, activez la case à cocher confirmation de configuration de l’authentification unique pour activer le certificat que vous avez téléchargée à force de vente. Cliquez sur **suivant**.

    ![Activez la case à cocher confirmation][18]

16. Sur la dernière page de la boîte de dialogue, tapez une adresse de messagerie si vous souhaitez recevoir des notifications par courrier électronique pour les erreurs et les avertissements liés à la maintenance de cette configuration de l’authentification unique. 

    ![Tapez votre adresse de messagerie.][19]

17. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Pour tester votre configuration, consultez la section intitulée [Affecter des utilisateurs à force de vente](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Étape 3 : Activer la mise en service automatisé utilisateur

1. Dans la page de démarrage rapide Azure AD pour Salesforce, cliquez sur le bouton **configurer approvisionnement de l’utilisateur** .

    ![Cliquez sur le bouton Configurer la mise en service des utilisateurs][20]

2. Dans la boîte de dialogue **configurer approvisionnement de l’utilisateur** , tapez votre nom d’utilisateur d’administration de force de vente et le mot de passe.

    ![Tapez votre nom d’utilisateur admin et le mot de passe][21]

    > [AZURE.NOTE] Si vous configurez un environnement de production, la meilleure solution consiste à créer un nouveau compte d’administrateur dans Salesforce spécifiquement pour cette étape. Ce compte doit avoir le profil **Administrateur système** attribué dans Salesforce.

3. Pour obtenir votre sécurité Salesforce jeton, ouvrez un nouvel onglet et connectez-vous sur le même compte d’administrateur Salesforce. Dans le coin supérieur droit de la page, cliquez sur votre nom, puis sur **Paramètres du site Mon**.

    ![Cliquez sur votre nom, puis cliquez sur Paramètres du site Mon][22]

4. Dans le volet de navigation gauche, cliquez sur **personnel** pour développer la section associée et puis cliquez sur **Réinitialiser mon jeton de sécurité**.

    ![Cliquez sur votre nom, puis cliquez sur Paramètres du site Mon][23]

5. Dans la page **Réinitialiser mon jeton de sécurité** , cliquez sur le bouton **Réinitialiser le jeton de sécurité** .

    ![Lisez les avertissements.][24]

6. Vérifier la boîte de réception associé à ce compte d’administrateur. Recherchez un message électronique à partir de Salesforce.com qui contient le nouveau jeton de sécurité.

7. Copier le jeton, accédez à la fenêtre Azure AD et collez-le dans le champ **Jeton de sécurité utilisateur** . Cliquez sur **suivant**.

    ![Coller du jeton de sécurité][25]

8. Dans la page de confirmation, vous pouvez choisir de recevoir des notifications par courrier électronique pour cas de panne mise en service. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

    ![Tapez votre adresse de messagerie pour recevoir des notifications][26]

##<a name="step-4-assign-users-to-salesforce"></a>Étape 4 : Assigner des utilisateurs à force de vente

1. Pour tester votre configuration, commencez par créer un nouveau compte de test dans l’annuaire.

2. Dans la page de démarrage rapide Salesforce, cliquez sur le bouton **Affecter des utilisateurs** .

    ![Cliquez sur affecter des utilisateurs][27]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **affecter** dans la partie inférieure de l’écran :

 - Si vous n’avez pas activer mise en service des utilisateurs automatisé, vous verrez le message de confirmation suivant :

        ![Confirm the assignment.][28]

 - Si vous avez activé approvisionnement automatisé de l’utilisateur, vous verrez une invite pour définir le type de profil Salesforce l’utilisateur doit avoir. Les utilisateurs nouvellement générés doivent apparaître dans votre environnement Salesforce après quelques minutes.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Si vous soyez mise en service pour un environnement de **développeur** Salesforce, vous devrez un nombre de licences disponibles pour chaque profil très limité. Par conséquent, il est préférable de mise en service des utilisateurs vers le profil **Utilisateur libre Chatter** , c'est-à-dire 4,999 licences disponibles.

4. Pour tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès à [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis connectez-vous au compte de test et cliquez sur **Salesforce**.

##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
