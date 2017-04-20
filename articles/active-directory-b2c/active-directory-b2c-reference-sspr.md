<properties
    pageTitle="Azure B2C annuaire Active : Réinitialisation de mot de passe libre-service | Microsoft Azure"
    description="Une rubrique qui expliquent comment les configurer libre-service du mot de passe permettant à vos consommateurs dans Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C annuaire Active : Configurer libre-service du mot de passe permettant à vos consommateurs

Avec la fonctionnalité de réinitialisation du mot de passe libre-service, votre consommateurs (qui se sont inscrits pour les comptes locaux) peuvent réinitialiser leur mot de passe eux-mêmes. Cela réduit considérablement la charge sur votre équipe de support, notamment si votre application comporte des millions de consommateurs en utilisant régulièrement. Pour l’instant, nous ne prennent en charge à l’aide d’une adresse de messagerie vérifié comme une méthode de récupération. Nous allons ajouter des méthodes de récupération supplémentaires (numéro de téléphone vérifié, les questions de sécurité, etc.) à l’avenir.

> [AZURE.NOTE]
Cet article s’applique au mot de passe libre-service réinitialiser utilisée dans le cadre d’une stratégie de connexion. Si vous avez besoin entièrement personnalisable mot de passe réinitialisé stratégies appelés à partir de votre application, voir [cet article](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Par défaut, votre répertoire n’ont pas de mot de passe libre-service réinitialiser activé. Procédez comme suit pour l’activer :

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) en tant que l’administrateur d’abonnement. C’est le même travail ou compte scolaire ou le même compte Microsoft que vous avez utilisé pour créer votre répertoire.
2. Accédez à l’extension de Active Directory dans la barre de navigation sur le côté gauche.
3. Recherchez votre annuaire sous l’onglet **annuaire** et cliquez dessus.
4. Cliquez sur l’onglet **configurer** .
5. Faites défiler jusqu'à la section **stratégie réinitialiser le mot de passe utilisateur** et activer ou désactiver l’option **utilisateurs invités mot de passe réinitialisé** sur **Oui**. Notez que l’option **Autre adresse de messagerie** est activée ; laisser tel quel.

    ![Réinitialisation du mot de passe libre-service](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Cliquez sur **Enregistrer** dans la partie inférieure de la page. Vous avez terminé !

Pour tester, utilisez la fonctionnalité « Exécuter maintenant » sur une stratégie de connexion qui comporte des comptes locaux comme fournisseur d’identité. Sur la connexion à compte local page (dans laquelle vous entrez une adresse de messagerie et mot de passe, ou un nom d’utilisateur et mot de passe), cliquez sur **ne peut pas accéder à votre compte ?** pour vérifier l’expérience grand public.

> [AZURE.NOTE]
Les pages de réinitialisation du mot de passe libre-service peuvent être personnalisées en utilisant la [fonctionnalité de personnalisation de société](../active-directory/active-directory-add-company-branding.md).
