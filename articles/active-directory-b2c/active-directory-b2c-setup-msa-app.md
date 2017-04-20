<properties
    pageTitle="Azure B2C annuaire Active : Configuration du compte Microsoft | Microsoft Azure"
    description="Fournir d’abonnement et se connecter aux consommateurs avec les comptes Microsoft dans vos applications qui sont protégées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure B2C annuaire Active : Fournir d’abonnement et se connecter aux consommateurs avec les comptes Microsoft

## <a name="create-a-microsoft-account-application"></a>Créer une demande de compte Microsoft

Pour utiliser le compte Microsoft comme fournisseur d’identité dans B2C Azure Active Directory (AD Azure), vous devez créer une demande de compte Microsoft et fournis avec les paramètres de droite. Vous avez besoin d’un compte Microsoft pour effectuer cette action. Si vous n’en avez pas, vous pouvez l’accéder en [https://www.live.com/](https://www.live.com/).

1. Accédez au [Portail de l’enregistrement de Microsoft Application](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) et connectez-vous avec vos informations d’identification du compte Microsoft.
2. Cliquez sur **Ajouter une application**.

    ![Microsoft compte - Ajouter une nouvelle application](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Indiquez un **nom** pour votre application, puis cliquez sur **créer une application**.

    ![Compte Microsoft - nom de l’application](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copiez la valeur **d’Id de l’Application**. Vous aurez besoin pour configurer un compte Microsoft comme fournisseur d’identité dans votre client.

    ![Compte Microsoft - Id de l’Application](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Cliquez sur **Ajouter plateforme** et sélectionnez **Web**.

    ![Microsoft compte - Ajouter plateforme](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Compte Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URI rediriger** . Remplacez **{client}** par nom de votre client (par exemple, contosob2c.onmicrosoft.com).

    ![Compte Microsoft - URL rediriger](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Cliquez sur **Générer un nouveau mot de passe** sous la section **Secrets de l’Application** . Copiez le nouveau mot de passe affiché sur l’écran. Vous aurez besoin pour configurer un compte Microsoft comme fournisseur d’identité dans votre client. Ce mot de passe est une information d’identification de sécurité importants.

    ![Microsoft compte - générer un nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Compte Microsoft - nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Cochez la case indiquant que **Live SDK prend en charge** sous la section **Options avancées** . Cliquez sur **Enregistrer**.

    ![Compte Microsoft - prise en charge Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurer un compte Microsoft comme fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans la carte de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Fournir un **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « MSA ».
5. Cliquez sur **type de fournisseur d’identité**, sélectionnez le **compte Microsoft**, puis cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** et entrez l’Id de l’Application et le mot de passe de l’application de compte Microsoft que vous avez créé précédemment.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer la configuration de votre compte Microsoft.
