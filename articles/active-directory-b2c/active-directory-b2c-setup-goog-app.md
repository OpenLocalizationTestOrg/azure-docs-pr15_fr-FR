<properties
    pageTitle="Azure B2C annuaire Active : Configuration de Google + | Microsoft Azure"
    description="Fournir d’abonnement et se connecter aux consommateurs avec les comptes Google + dans vos applications qui sont protégées par Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure B2C annuaire Active : Fournir d’abonnement et se connecter aux consommateurs avec les comptes Google +

## <a name="create-a-google-application"></a>Créer une application Google +

Pour utiliser Google + comme fournisseur d’identité dans B2C Azure Active Directory (AD Azure), vous devez créer une application Google + et fournir avec les paramètres de droite. Vous avez besoin d’un compte Google + pour effectuer cette action. Si vous n’en avez pas, vous pouvez l’accéder en [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accédez à la [Console de développeurs Google](https://console.developers.google.com/) et connectez-vous avec vos informations d’identification compte Google +.
2. Cliquez sur **créer un projet**, entrez un **nom de projet**, puis cliquez sur **créer**.

    ![Google + - prise en main](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - nouveau projet](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Cliquez sur **Gestionnaire de l’API** , puis sur **informations d’identification** dans le volet de navigation gauche.
4. Cliquez sur l’onglet **OAuth consentement écran** en haut.

    ![Google + - informations d’identification](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Sélectionnez ou spécifiez une **adresse de messagerie**valide, indiquez un **nom de produit**, puis cliquez sur **Enregistrer**.

    ![Google + - OAuth consentement écran](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Cliquez sur les **nouvelles informations d’identification** , puis sélectionnez **ID client OAuth**.

    ![Google + - OAuth consentement écran](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Sous **type d’Application**, sélectionnez **une application Web**.

    ![Google + - OAuth consentement écran](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Indiquez un **nom** pour votre application, entrez `https://login.microsoftonline.com` dans le champ **origines JavaScript autorisés** , et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **autorisés rediriger URI** . Remplacez **{client}** par nom de votre client (par exemple, contosob2c.onmicrosoft.com). La valeur **{client}** respecte la casse. Cliquez sur **créer**.

    ![Google + - créer ID client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copiez les valeurs **d’ID de Client** et le **code secret Client**. Vous avez besoin de configurer Google + en tant que fournisseur d’identité dans votre client. **Secret client** est une information d’identification de sécurité importants.

    ![Google + - secret Client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurer Google + en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans la carte de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Fournir un **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « G + ».
5. Cliquez sur **type de fournisseur d’identité**, sélectionnez **Google**, puis cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** et entrez le code secret de client de l’application Google + que vous avez créé précédemment et Réf client.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration Google +.
