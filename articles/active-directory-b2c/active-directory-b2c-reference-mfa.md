<properties
    pageTitle="B2C Azure Active Directory : L’authentification multifacteur | Microsoft Azure"
    description="Comment activer l’authentification multifacteur dans les applications pour les consommateurs sécurisées par Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure B2C annuaire Active : Activer l’authentification multifacteur dans vos applications pour les consommateurs

Azure Active Directory (AD Azure) B2C s’intègre directement à [L’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication.md) afin que vous pouvez ajouter un second niveau de sécurité à des expériences d’abonnement et se connecter dans vos applications pour les consommateurs. Et vous pouvez le faire sans écrire une seule ligne de code. Prend en charge vérification message appel téléphonique et le texte. Si vous avez déjà créé les stratégies d’inscription et connexion, vous pouvez toujours activer l’authentification multifacteur.

> [AZURE.NOTE]
L’authentification multifacteur peut également être activée lorsque vous créez des stratégies d’inscription et connexion, pas seulement en modifiant les stratégies existantes.

Cette fonctionnalité permet aux applications de gérer des scénarios tels que les éléments suivants :

- Vous ne nécessitent pas l’authentification multifacteur pour accéder à une application, mais vous en avez besoin pour accéder à un autre. Par exemple, le consommateur pouvez vous connecter à une application d’assurance automatique avec un compte de mise en réseau ou local, mais doit vérifier le numéro de téléphone avant d’accéder à l’application d’assurance accueil enregistré dans le même répertoire.
- Vous ne nécessitent pas l’authentification multifacteur pour accéder à une application en général, mais vous en avez besoin pour accéder à des parties sensibles qu’il contient. Par exemple, le consommateur peut se connecter à une application bancaire avec un compte de mise en réseau ou local et le solde du compte à cocher, mais doit vérifier le numéro de téléphone avant d’essayer de transfert bancaire.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modifier votre stratégie d’inscription pour activer l’authentification multifacteur

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur votre stratégie d’inscription (par exemple, « B2C_1_SiUp ») pour l’ouvrir.
4. Cliquez sur **l’authentification multifacteur** , activez l' **état** sur **activé**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** en haut de la carte.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » sur la stratégie pour vérifier l’expérience grand public. Vérifiez les points suivants :

Un compte est créé dans votre annuaire avant de l’étape de l’authentification multifacteur se produit. Lors de l’étape, le client est invité à fournir son numéro de téléphone et confirmez-le. Si la vérification terminée, le numéro de téléphone est lié au compte client pour une utilisation ultérieure. Même si le consommateur annule ou disparaît, il peut invité à vérifier un numéro de téléphone à nouveau lors de la connexion suivante (avec l’authentification multifacteur activée).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modifier votre stratégie de connexion pour activer l’authentification multifacteur

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies de connexion**.
3. Cliquez sur votre stratégie connexion (par exemple, « B2C_1_SiIn ») pour l’ouvrir. Cliquez sur **Modifier** en haut de la carte.
4. Cliquez sur **l’authentification multifacteur** , activez l' **état** sur **activé**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** en haut de la carte.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » sur la stratégie pour vérifier l’expérience grand public. Vérifiez les points suivants :

Lorsque le consommateur se connecte (à l’aide d’un compte de mise en réseau ou local), si un numéro de téléphone vérifié est connecté au compte consommateur, il est invité à vérifier. Si aucun numéro de téléphone n’est connecté, le client est invité à fournir une et confirmez-le. Dans une vérification réussie, le numéro de téléphone est lié au compte client pour une utilisation ultérieure.

## <a name="multi-factor-authentication-on-other-policies"></a>Authentification multifacteur sur d’autres stratégies

Comme décrit pour les stratégies d’inscription et connexion ci-dessus, il est également possible d’activer l’authentification multifacteur sur inscription ou mot de passe et stratégies de connexion réinitialiser les stratégies. Il sera disponible dès sous stratégies de modification du profil.
