<properties
    pageTitle="Azure B2C annuaire Active : Configuration de LinkedIn | Microsoft Azure"
    description="Fournir d’abonnement et se connecter aux consommateurs avec des comptes LinkedIn dans vos applications qui sont protégées par Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure B2C annuaire Active : Fournir d’abonnement et se connecter aux consommateurs avec des comptes LinkedIn

## <a name="create-a-linkedin-application"></a>Créer une application LinkedIn

Pour utiliser un fournisseur d’identité dans Azure Active Directory (AD Azure) B2C LinkedIn, vous devez créer une application LinkedIn et fournir avec les paramètres de droite. Vous avez besoin d’un compte LinkedIn pour effectuer cette action. Si vous n’en avez pas, vous pouvez l’accéder en [https://www.linkedin.com/](https://www.linkedin.com/).

1. Accédez au [site Web de développeurs LinkedIn](https://www.developer.linkedin.com/) et connectez-vous avec vos informations d’identification du compte LinkedIn.
2. Cliquez sur **Mes applications** dans la barre de menus supérieure, puis sur **Créer une Application**.

    ![LinkedIn - nouvelle application](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Dans l’écran **créer une Application** , renseignez les informations pertinentes (**Nom de la société**, **nom**, **Description**, **URL de Logo de l’Application**, **Utilisation de l’Application**, **URL de site Web**, **Adresse de messagerie professionnelles** et **Téléphone (bureau)**).
4. Acceptez les **Conditions d’utilisation de LinkedIn API** , cliquez sur **Envoyer**.

    ![LinkedIn - application Registre](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copiez les valeurs **d’ID de Client** et le **Code Secret Client**. (Vous pouvez les retrouver sous **Clés d’authentification**.) Vous avez besoin de chacun d’eux pour configurer LinkedIn comme fournisseur d’identité dans votre client.

    >[AZURE.NOTE] **Secret Client** est une information d’identification de sécurité importants.

6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de rediriger autorisés** (sous **OAuth 2.0**). Remplacez **{client}** par nom de votre client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Ajouter**, puis cliquez sur **mettre à jour**. La valeur **{client}** respecte la casse.

    ![LinkedIn - mise en application](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurer LinkedIn en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans la carte de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Fournir un **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « liste » d’horizontale.
5. Cliquez sur **type de fournisseur d’identité**, sélectionnez **LinkedIn**, puis cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** et entrez le code secret de client de l’application LinkedIn que vous avez créé précédemment et Réf client.
7. Cliquez sur **OK** , puis sur **créer** pour enregistrer votre configuration LinkedIn.
