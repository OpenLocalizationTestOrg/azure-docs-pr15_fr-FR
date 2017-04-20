<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Facebook au travail | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Facebook au travail."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Didacticiel : Intégration d’Azure Active Directory avec Facebook au travail

L’objectif de ce didacticiel consiste à vous montrer comment intégrer Facebook au travail Azure Active Directory (AD Azure).

Intégration Facebook au travail avec Azure AD vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui accèdent à Facebook au travail 
- Vous pouvez automatiquement mise en service de compte pour les utilisateurs qui ont accès à Facebook au travail
- Vous pouvez permettre à vos utilisateurs pour automatiquement obtenir connecté à Facebook au travail (SSO) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central 

Si vous souhaitez en savoir plus d’informations sur l’intégration de l’application SaaS avec Azure Active Directory, voir [qu’est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration Azure AD avec des étoiles SC, vous devez les éléments suivants :

- Un abonnement Azure AD
- Facebook au travail avec authentification unique activée

Pour tester les étapes décrites dans ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement de version d’évaluation Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Ajout de Facebook au travail à partir de la galerie
Pour configurer l’intégration de Facebook au travail dans Azure Active Directory, vous devez ajouter Facebook au travail à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Facebook au travail à partir de la galerie, procédez comme suit :**

1. Dans le **portail classique Azure**, dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue applications, dans l’affichage du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Facebook au travail**.

7. Dans le volet résultats, sélectionnez **Facebook au travail**, puis cliquez sur **Terminer** pour ajouter l’application.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’authentification Azure AD unique

Cette section explique comment permettre aux utilisateurs de s’authentifier à Facebook au travail avec leur propre compte dans Azure Active Directory, à l’aide de la fédération basée sur le protocole SAML.

**Pour configurer Azure AD SSO avec Facebook au travail, procédez comme suit :**

1.  Après avoir ajouté Facebook au travail dans le portail Azure classique, cliquez sur **Configuration de l’authentification unique**.

2.  Dans l’écran **Configurer l’application URL** , entrez l’URL où les utilisateurs seront vous connecter à votre Facebook à l’application de travail. Il s’agit de votre Facebook à l’URL de client de travail (exemple : https://example.facebook.com/). Une fois que vous avez terminé, cliquez sur **suivant**.

3.  Dans une fenêtre de navigateur web différente, connectez-vous à votre Facebook sur site de société de travail en tant qu’administrateur.

4. Suivez les instructions à l’URL suivante pour configurer Facebook au travail, à utiliser Azure AD comme fournisseur d’identité : [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Une fois terminée, retournez à fenêtres du navigateur affichant le portail Azure classique, cliquez sur la case à cocher pour confirmer que vous avez terminé la procédure, puis cliquez sur **suivant** et **achevé**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Mise en service automatiquement des utilisateurs à Facebook au travail

Azure AD prend en charge la possibilité de synchroniser automatiquement les détails du compte d’utilisateur Facebook au travail affecté. La synchronisation automatique permet de Facebook au travail pour obtenir les données que nécessaires pour autoriser les utilisateurs pour l’accès, avant leur tentative de connexion pour la première fois. Il met également en désactiver les utilisateurs à partir de Facebook au travail lors de l’accès a été révoqué dans Azure Active Directory.

Mise à disponibilité automatique peut être configuré en cliquant sur **configurer mise en service de compte** dans la fenêtre du portail classique Azure.

Pour plus d’informations sur la configuration de la mise en service automatique, voir [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Affectation d’utilisateurs à Facebook au travail

Pour les utilisateurs AAD générés puissent voir Facebook au travail sur leur panneau d’accès, ils doivent être affectées à accéder à l’intérieur du portail classique Azure.

**Pour affecter des utilisateurs à Facebook au travail :**

1.  Dans la page de démarrage de Facebook au travail dans le portail Azure classique, cliquez sur **affecter des comptes**.

2.  Dans le menu **Afficher** , indiquez si vous voulez affecter un utilisateur ou un groupe à Facebook au travail, cliquez sur le bouton de coche.

3.  Dans la liste, sélectionnez les utilisateurs ou le groupe auquel vous souhaitez affecter Facebook au travail.

4.  Dans le pied de page, cliquez sur le bouton **affecter** .


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur l’intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




