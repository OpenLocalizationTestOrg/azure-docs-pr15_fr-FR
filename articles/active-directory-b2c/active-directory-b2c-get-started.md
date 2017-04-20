<properties
    pageTitle="Azure B2C annuaire Active : Créez un client Azure Active Directory B2C | Microsoft Azure"
    description="Une rubrique sur la création d’un client Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure B2C annuaire Active : Créez un client Azure AD B2C

Pour commencer à utiliser Microsoft Azure Active Directory (AD Azure) B2C, suivez les trois étapes décrites dans cet article.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Étape 1 : S’inscrire à un abonnement Azure

Si vous disposez déjà d’un abonnement Azure, ignorez cette étape. Dans le cas contraire, s’inscrire à un [abonnement Azure](../active-directory/sign-up-organization.md) et accéder aux Azure AD B2C.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Étape 2 : Créer un client Azure AD B2C

Procédez comme suit pour créer un nouveau client Azure AD B2C. Actuellement B2C fonctionnalités ne peut pas être activées dans vos clients existants.

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) en tant que l’administrateur d’abonnement. C’est le même travail ou compte scolaire ou le même compte Microsoft que vous avez utilisé pour vous inscrire Azure.
2. Cliquez sur **Nouveau** > **application Services** > **Active Directory** > **répertoire** > **créer personnalisé**.

    ![Capture d’écran de démarrage créer un client](./media/active-directory-b2c-get-started/new-directory.png)

3. Sélectionnez le **nom**, le **Nom de domaine** et le **pays ou région** pour votre client.
4. Cochez l’option indiquant **qu’il s’agit d’un répertoire B2C**.
5. Activez la case à cocher pour terminer l’action.

    ![Capture d’écran de la case à cocher pour créer un répertoire B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Votre client est créé et s’affichera dans l’extension Active Directory. Un administrateur Global du client sont également apportées. Vous pouvez ajouter d’autres administrateurs globaux si nécessaire.

    > [AZURE.IMPORTANT]
    Si vous envisagez d’utiliser un client B2C pour une application de production, consultez l’article sur [échelle de production et clients Aperçu B2C](active-directory-b2c-reference-tenant-type.md). Notez qu’il existe des connus problèmes lorsque vous supprimez un client B2C existant puis recréez en utilisant le même nom de domaine. Vous devez créer un client B2C avec un nom de domaine différent.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Étape 3 : Accédez à la carte de fonctionnalités B2C sur le portail Azure

1. Accédez à l’extension de Active Directory dans la barre de navigation sur le côté gauche.
2. Recherchez votre client sous l’onglet **annuaire** et cliquez dessus.
3. Cliquez sur l’onglet **configurer** .
4. Cliquez sur le lien **Gérer B2C paramètres** dans la section **administration B2C** .

    ![Capture d’écran de configuration de l’annuaire pour B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Le portail Azure avec la carte de fonctionnalités B2C affichant s’ouvre dans une fenêtre ou un nouvel onglet du navigateur.

    > [AZURE.IMPORTANT]
    Il peut prendre jusqu'à 2-3 minutes pour votre client être accessibles dans le portail Azure. Nouvelle tentative de ces étapes après que peu de temps pouvez résoudre ce problème. Si ce n’est pas le cas, contactez le support technique.

6. Épingler cette carte à votre Startboard pour y accéder facilement. (L’outil de code confidentiel est marquée en rouge dans le coin supérieur droit de la cuillère fonctionnalités).

    ![Capture d’écran de la cuillère fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Vous pouvez gérer les utilisateurs et groupes, configuration de réinitialisation de mot de passe libre-service et fonctionnalités de personnalisation de société de votre client dans le [portail classique Azure](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Faciliter l’accès à la carte de fonctionnalités B2C sur le portail Azure

Pour améliorer la détectabilité, nous avons ajouté un raccourci pour la carte de fonctionnalités B2C sur le portail Azure.

1. Se connecter au portail Azure tant qu’administrateur Global de votre client B2C. Si vous êtes déjà connecté à un autre client, basculez clients (dans le coin supérieur droit).
2. Dans la barre de navigation gauche, cliquez sur **Parcourir** .
3. Cliquez sur **Azure AD B2C** pour accéder à la carte de fonctionnalités B2C.

    ![Capture d’écran de navigation pour la carte de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment enregistrer une application avec Azure AD B2C et pour créer une application de démarrage rapide en lisant [Azure Active Directory B2C : enregistrer votre application](active-directory-b2c-app-registration.md).
