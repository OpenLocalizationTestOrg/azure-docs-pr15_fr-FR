<properties
    pageTitle="Azure B2C annuaire Active : Configuration de Facebook | Microsoft Azure"
    description="Fournir d’abonnement et se connecter aux consommateurs avec Facebook des comptes dans vos applications qui sont protégées par Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure B2C annuaire Active : Fournir d’abonnement et se connecter aux consommateurs avec des comptes Facebook

## <a name="create-a-facebook-application"></a>Créer une application Facebook

Pour utiliser un fournisseur d’identité dans Azure Active Directory (AD Azure) B2C Facebook, vous devez créer une application Facebook et fournir avec les paramètres de droite. Vous avez besoin d’un compte Facebook pour effectuer cette action. Si vous n’en avez pas, vous pouvez l’accéder en [https://www.facebook.com/](https://www.facebook.com/).

1. Accédez au site Web de [Facebook pour les développeurs](https://developers.facebook.com/) et connectez-vous avec vos informations d’identification du compte Facebook.
2. Si vous ne le n'avez pas déjà fait, vous devez enregistrer en tant qu’un développeur Facebook. Pour ce faire, cliquez sur **Enregistrer** (dans le coin supérieur droit de la page), accepter les stratégies de Facebook et suivez les étapes de l’enregistrement.
3. Cliquez sur **Mes applications** , puis sur **Ajouter une nouvelle application**. Sélectionnez site **Web** en tant que la plateforme, puis cliquez sur **Ignorer et créer les ID de l’application**.

    ![Facebook - ajouter une nouvelle application](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - ajouter une nouvelle application - site Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - créer des ID de l’application](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Dans le formulaire, fournir un **Nom d’affichage**, un **Contact messagerie**valide, une **catégorie**appropriée, puis cliquez sur **Créer des ID de l’application**. Cela vous oblige à accepter les stratégies de plateforme Facebook et effectuer une vérification de sécurité en ligne.

    ![Facebook - créer un identifiant application](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Dans la barre de navigation gauche, cliquez sur **paramètres** .
6. Cliquez sur **+ Ajouter plateforme** , puis sélectionnez **site Web**.

    ![Facebook - paramètres](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Site Web de Facebook - paramètres-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Entrez [https://login.microsoftonline.com/](https://login.microsoftonline.com/) dans le champ **URL du Site** , puis sur **Enregistrer les modifications**.

    ![Facebook - URL du Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copiez la valeur **d’ID de l’application**. Cliquez sur **Afficher** et copiez la valeur de **L’application code Secret**. Vous avez besoin de chacun d’eux pour configurer Facebook comme fournisseur d’identité dans votre client. **Application Secret** est une information d’identification de sécurité importants.

    ![Facebook - ID de l’application et application Secret](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Cliquez sur **+ Ajouter produit** sur la navigation à gauche, puis sur le bouton **Commencer** en regard de **Connexion Facebook**.

    ![Facebook - connexion Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **OAuth valide rediriger URI** dans la section **Paramètres jeton du Client** . Remplacez **{client}** par nom de votre client (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Enregistrer les modifications** dans la partie inférieure de la page.

    ![Facebook - URI de redirection OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Pour rendre votre application Facebook utilisables par Azure AD B2C, vous devez rendre public. Vous pouvez le faire en cliquant sur **Révision d’application** dans la barre de navigation gauche et en activant le commutateur en haut de la page sur la **valeur Oui** et en cliquant sur **Confirmer**.

    ![Facebook - application public](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurer Facebook en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans la carte de fonctionnalités B2C, cliquez sur **fournisseurs d’identité**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Fournir un **nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Toujours ».
5. Cliquez sur **type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
6. Cliquez sur **configurer ce fournisseur d’identité** et entrez le code secret d’application (de l’application Facebook que vous avez créé précédemment) et ID d’application dans **l’ID de Client** et le **Client secret** champs respectivement.
7. Cliquez sur **OK**, puis cliquez sur **créer** pour enregistrer votre configuration Facebook.
