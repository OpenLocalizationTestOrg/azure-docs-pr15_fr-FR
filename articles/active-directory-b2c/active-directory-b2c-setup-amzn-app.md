<properties
    pageTitle="Azure B2C annuaire Active : Configuration d’Amazon | Microsoft Azure"
    description="Fournir d’abonnement et se connecter aux consommateurs avec des comptes d’Amazon dans vos applications qui sont protégées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure B2C annuaire Active : Fournir d’abonnement et se connecter aux consommateurs avec des comptes d’Amazon

## <a name="create-an-amazon-application"></a>Créer une application Amazon

Pour utiliser un fournisseur d’identité dans Azure Active Directory (AD Azure) B2C Amazon, vous devez créer une application Amazon et fournir avec les paramètres de droite. Vous avez besoin d’un compte Amazon pour effectuer cette action. Si vous n’en avez pas, vous pouvez l’accéder en [http://www.amazon.com/](http://www.amazon.com/).

1. Accédez au [Centre de développement Amazon](https://login.amazon.com/) et connectez-vous avec vos informations d’identification du compte Amazon.
2. Si vous ne le n'avez pas déjà fait, cliquez sur **S’inscrire**, suivez la procédure d’inscription pour les développeurs et accepter la stratégie.
3. Cliquez sur **Enregistrer la nouvelle application**.

    ![Enregistrement d’une nouvelle application sur le site Web Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fournir des informations sur l’application (**nom**, **Description**et **URL avis de confidentialité**), puis cliquez sur **Enregistrer**.

    ![Fournir des informations sur l’application d’enregistrement d’une nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Dans la section **Paramètres du site Web** , copiez les valeurs **d’ID de Client** et le **Code Secret Client**. (Vous devez cliquer sur le bouton **Afficher le code Secret** pour voir ceci). Vous devez les deux pour configurer Amazon comme fournisseur d’identité dans votre client. Cliquez sur **Modifier** en bas de la section. **Secret Client** est une information d’identification de sécurité importants.

    ![Fournir des ID de Client et Secret Client pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Entrez `https://login.microsoftonline.com` dans le champ **Autorisés JavaScript origines** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de retour autorisée** . Remplacez **{client}** par nom de votre client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Enregistrer**. La valeur **{client}** respecte la casse.

    ![Fournir des origines JavaScript et renvoyer une URL pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurer Amazon en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans la carte de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Fournir un **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Amzn ».
5. Cliquez sur **type de fournisseur d’identité**, sélectionnez **Amazon**, puis cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** et entrez le code secret de client de l’application Amazon que vous avez créé précédemment et Réf client.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration Amazon.
