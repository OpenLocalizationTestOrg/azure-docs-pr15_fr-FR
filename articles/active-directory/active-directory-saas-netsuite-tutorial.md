<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec NetSuite | Microsoft Azure"
    description="Découvrez comment utiliser NetSuite avec Azure Active Directory pour activer l’authentification unique, la mise en service automatisé et bien plus encore !"
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

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Didacticiel : Comment intégrer NetSuite Azure Active Directory

Ce didacticiel montre comment connecter votre environnement NetSuite à votre Azure Active Directory (AD Azure). Vous allez apprendre comment configurer l’authentification unique pour NetSuite, comment activer la mise en service automatisé utilisateur et comment affecter des utilisateurs à accéder à NetSuite. 

##<a name="prerequisites"></a>Conditions préalables

1. Pour accéder à Azure Active Directory via le [portail classique Azure](https://manage.windowsazure.com), vous devez tout d’abord un abonnement Azure valide.

2. Vous devez avoir accès administrateur à un abonnement [NetSuite](http://www.netsuite.com/portal/home.shtml) . Vous pouvez utiliser un compte d’essai gratuit pour chaque service.

##<a name="step-1-add-netsuite-to-your-directory"></a>Étape 1 : Ajouter NetSuite à votre répertoire

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Sélectionnez Active Directory dans le volet de navigation gauche.][0]

2. Dans la liste **répertoire** , sélectionnez le répertoire que vous voulez ajouter NetSuite à.

3. Cliquez sur **Applications** dans le menu supérieur.

    ![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** en bas de la page.

    ![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la **zone de recherche**, tapez **NetSuite**. Sélectionnez **NetSuite** dans les résultats, puis cliquez sur **terminé** pour ajouter l’application.

    ![Ajouter NetSuite.][4]

7. Vous devez maintenant voir la page de démarrage rapide pour NetSuite :

    ![Page de démarrage rapide de NetSuite dans Azure Active Directory][5]

##<a name="step-2-enable-single-sign-on"></a>Étape 2 : Activer l’ouverture de session unique

1. Dans Azure Active Directory, dans la page de démarrage rapide de NetSuite, cliquez sur le bouton **de configurer l’authentification unique** .

    ![Configurer un seul authentification bouton][6]

2. Une boîte de dialogue s’ouvre et vous verrez un écran vous demandant « Comment souhaitez-vous aux utilisateurs de se connecter à NetSuite ? » Sélectionnez **Azure AD SSO**, puis cliquez sur **suivant**.

    ![Sélectionnez authentification Azure AD unique][7]

    > [AZURE.NOTE] En savoir plus sur les différentes unique authentification options, [Cliquez ici](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Dans la page **Configurer les paramètres de l’application** , pour le champ **URL de réponse** , tapez dans l’URL de votre client NetSuite en utilisant l’un des formats suivants :
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Tapez l’URL de votre client][8]

4. Dans la page **configuration de l’authentification unique en NetSuite** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Télécharger les métadonnées.][9]

5. Ouvrez un nouvel onglet dans votre navigateur, puis connectez-vous à votre site d’entreprise Netsuite en tant qu’administrateur.

6. Dans la barre d’outils en haut de la page, cliquez sur **le programme d’installation**, puis cliquez sur **Gestionnaire de configuration**.

    ![Accédez au Gestionnaire de configuration][10]

7. Dans la liste de **Tâches de configuration** , sélectionnez **l’intégration**.

    ![Accédez à l’intégration][11]

8. Dans la section **Authentification de gérer** , cliquez sur **authentification unique SAML**.

    ![Accédez à de SAML l’authentification unique][12]

9. Dans la page **Configuration de SAML** , effectuez les opérations suivantes :

    - Dans Azure Active Directory, copiez la valeur de **l’URL de connexion à distance** et collez-le dans le champ de **Page de connexion de fournisseur identité** dans NetSuite.

        ![La page Configuration de SAML.][13]

    - Dans NetSuite, sélectionnez la **Méthode d’authentification principale**.

    - Pour le champ intitulé **Métadonnées de fournisseur d’identité SAMLV2**, sélectionnez **Télécharger un fichier de métadonnées IDP**. Cliquez sur **Parcourir** pour télécharger le fichier de métadonnées que vous avez téléchargé à l’étape 4 #.

        ![Télécharger les métadonnées][16]

    - Cliquez sur **Envoyer**.

9. Dans Azure Active Directory, activez la case à cocher confirmation de configuration de l’authentification unique pour activer le certificat que vous avez téléchargées sur NetSuite. Cliquez sur **suivant**.

    ![Activez la case à cocher confirmation][14]

10. Sur la dernière page de la boîte de dialogue, tapez une adresse de messagerie si vous souhaitez recevoir des notifications par courrier électronique pour les erreurs et les avertissements liés à la maintenance de cette configuration de l’authentification unique. 

    ![Tapez votre adresse de messagerie.][15]

11. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Cliquez ensuite sur **attributs** en haut de la page.

    ![Cliquez sur attributs.][17]

12. Cliquez sur **Ajouter attribut utilisateur**.

    ![Cliquez sur Ajouter un attribut de l’utilisateur.][18]

13. Pour le champ **Nom de l’attribut** , tapez dans `account`. Pour le champ de **Valeur de l’attribut** , tapez votre ID de compte NetSuite. Vous trouverez ci-dessous des instructions sur la façon de trouver votre ID de compte :

    ![Ajouter votre ID de compte NetSuite.][19]

    - Dans NetSuite, cliquez sur **le programme d’installation** à partir du menu de navigation supérieure.
    - Puis cliquez sur sous la section **Tâches de configuration** du menu de navigation de gauche, sélectionnez la section **intégration** et cliquez sur **Préférences des Services Web**.
    - Copiez votre ID de compte NetSuite et collez-le dans le champ de **Valeur de l’attribut** dans Azure Active Directory.

        ![Obtenir votre ID de compte][20]

14. Dans Azure Active Directory, cliquez sur **terminé** pour terminer l’ajout de l’attribut SAML. Puis cliquez sur **Appliquer les modifications** dans le menu en bas.

    ![Enregistrez vos modifications.][21]

15. Avant que les utilisateurs peuvent effectuer de l’authentification unique dans NetSuite, ils doivent tout d’abord être affectées les autorisations appropriées dans NetSuite. Suivez les instructions ci-dessous pour affecter les autorisations suivantes.

    - Dans le menu de navigation supérieure, cliquez sur **le programme d’installation**, puis cliquez sur **Gestionnaire de configuration**.

        ![Accédez au Gestionnaire de configuration][10]

    - Dans le menu de navigation de gauche, sélectionnez **Les utilisateurs/rôles**, puis cliquez sur **Gérer les rôles**.

        ![Accédez à gérer les rôles][22]

    - Cliquez sur **nouveau rôle**.

    - Tapez un **nom** pour le nouveau rôle, puis sélectionnez la case à cocher **Unique authentification uniquement** .

        ![Nom du nouveau rôle.][23]

    - Cliquez sur **Enregistrer**.

    - Dans le menu dans la partie supérieure, cliquez sur **autorisations**. Cliquez ensuite sur **le programme d’installation**.

        ![Accédez à autorisations][24]

    - Sélectionnez **définir la SAM de l’authentification unique**, puis cliquez sur **Ajouter**.

    - Cliquez sur **Enregistrer**.

    - Dans le menu de navigation supérieure, cliquez sur **le programme d’installation**, puis cliquez sur **Gestionnaire de configuration**.

        ![Accédez au Gestionnaire de configuration][10]

    - Dans le menu de navigation de gauche, sélectionnez **Les utilisateurs/rôles**, puis cliquez sur **Gérer les utilisateurs**.

        ![Accédez à gérer les utilisateurs][25]

    - Sélectionnez un utilisateur de test. Cliquez sur **Modifier**.

        ![Accédez à gérer les utilisateurs][26]

    - Dans la boîte de dialogue rôles, sélectionnez le rôle que vous avez créé et cliquez sur **Ajouter**.

        ![Accédez à gérer les utilisateurs][27]

    - Cliquez sur **Enregistrer**.

19. Pour tester votre configuration, consultez la section intitulée [Affecter des utilisateurs à NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Étape 3 : Activer la mise en service automatisé utilisateur

> [AZURE.NOTE] Par défaut, vos utilisateurs générés sont ajoutés à la filiale racine de votre environnement NetSuite.

1. Dans Azure Active Directory, dans la page de démarrage rapide pour NetSuite, cliquez sur **configurer approvisionnement de l’utilisateur**.

    ![Configurer la configuration des utilisateurs][28]

2. Dans la boîte de dialogue qui s’ouvre, renseignez vos informations d’identification d’administration pour NetSuite, puis cliquez sur **suivant**.

    ![Tapez vos informations d’identification d’administration de NetSuite.][29]

3. Dans la page de confirmation, tapez votre adresse de messagerie pour recevoir des notifications de mise en service des échecs.

    ![Confirmer.][30]

4. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

##<a name="step-4-assign-users-to-netsuite"></a>Étape 4 : Assigner des utilisateurs à NetSuite

1. Pour tester votre configuration, commencer à créer un nouveau compte de test dans l’annuaire.

2. Dans la page de démarrage rapide NetSuite, cliquez sur le bouton **Affecter des utilisateurs** .

    ![Cliquez sur affecter des utilisateurs][31]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **affecter** dans la partie inférieure de l’écran :

 - Si vous n’avez pas activer mise en service des utilisateurs automatisé, vous verrez le message de confirmation suivant :

        ![Confirm the assignment.][32]

 - Si vous avez activé approvisionnement automatisé de l’utilisateur, vous verrez une invite pour définir quel type de rôle de l’utilisateur doit avoir dans NetSuite. Les utilisateurs nouvellement générés doivent apparaître dans votre environnement NetSuite après quelques minutes.

4. Pour tester vos paramètres de l’authentification unique, ouvrez le panneau d’accès au [https://myapps.microsoft.com](https://myapps.microsoft.com/), connectez-vous au compte de test et cliquez sur **NetSuite**.

##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
