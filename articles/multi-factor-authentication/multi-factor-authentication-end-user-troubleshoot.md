<properties
    pageTitle="Résoudre les problèmes de la vérification | Microsoft Azure"
    description="Ce document fournit aux utilisateurs des informations sur la procédure à suivre si elles rencontrez un problème avec l’authentification multifacteur Azure."
    services="multi-factor-authentication"
    keywords = "client de l’authentification multifacteur, problème d’authentification, ID de corrélation"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>Vous rencontrez des problèmes avec la vérification

Cet article présente certains problèmes que vous pouvez rencontrer avec la vérification. Si le problème que vous rencontrez n’est pas inclus ici, veuillez fournir des commentaires détaillés dans la section commentaires afin que nous pouvons l’améliorer.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>J’ai perdu mon téléphone ou qu’il a été vol

Il existe deux manières de revenir à votre compte. La première consiste à se connecter à l’aide de votre numéro de téléphone d’authentification alternatives, si vous avez configuré un. Le second est demander à votre administrateur pour effacer vos paramètres.

Si votre téléphone a été perte ou de vol, nous vous recommandons également que vous avez votre administrateur de réinitialiser votre mot de passe d’application et désactivez les périphériques à retenir. Si votre administrateur ne sait pas comment effectuer cette opération, orientez-les vers cet article : [Gérer les utilisateurs et les appareils mobiles](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Utiliser un autre numéro de téléphone

Si vous avez configuré plusieurs options de vérification, y compris un numéro de téléphone secondaire ou une application authentificateur sur un autre périphérique, vous pouvez utiliser une des offres pour vous connecter.

Pour vous connecter à l’aide de l’autre numéro de téléphone, procédez comme suit :

1. Connectez-vous comme d’habitude.
2. Lorsque vous êtes invité à vérifier votre compte, choisissez **utiliser une option de vérification**.

    ![Vérification différent](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Sélectionnez le numéro de téléphone que vous avez accès.

    ![Autre numéro de téléphone](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Une fois que vous revenez dans votre compte, [gérer vos paramètres](multi-factor-authentication-end-user-manage-settings.md) pour modifier votre numéro de téléphone d’authentification.

>[AZURE.IMPORTANT]
>Il est important de configurer un numéro de téléphone d’authentification secondaire. Si votre numéro de téléphone principal et votre application mobile se trouvent sur le téléphone de même, vous avez besoin d’une troisième option si votre téléphone est perte ou de vol.

### <a name="clear-your-settings"></a>Effacer vos paramètres

Si vous n’avez pas configuré un numéro de téléphone d’authentification secondaire, vous devez contacter votre administrateur pour obtenir. Demandez-leur de vos paramètres effacez la prochaine fois que vous vous connectez, vous devrez [configurer votre compte](multi-factor-authentication-end-user-first-time.md) à nouveau.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Je ne reçois pas un texte ou d’appeler sur mon téléphone

Il existe plusieurs raisons pourquoi vous pouvez essayer à se connecter, mais pas recevoir le texte ou un appel téléphonique. Si vous avez correctement reçu des textes ou des appels téléphoniques sur votre téléphone précédemment, puis c’est probablement un problème avec le fournisseur de téléphone, pas votre compte. Assurez-vous que vous avez signal bonne cellule, et si vous voulez recevoir un message texte Vérifiez que que votre plan de téléphone et le service prend en charge les messages en texte.

Si vous avez attendu après quelques minutes pour un appel ou de texte, le moyen le plus rapide pour accéder à votre compte consiste à essayer une autre option.

1. Sélectionnez **utiliser une option de vérification différent** dans la page qui est en attente de vérification de votre.

    ![Vérification différent](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Sélectionner la méthode de remise ou de numéro de téléphone que vous voulez utiliser.

    Si vous avez reçu plusieurs codes de vérification, seul le plus récent est disponible.

Si vous n’avez pas une autre méthode configurée, contactez votre administrateur et demandez-lui d’effacer vos paramètres. La prochaine fois que vous vous connectez, vous devrez [configurer l’authentification multifacteur](multi-factor-authentication-end-user-first-time.md) à nouveau.


Si vous avez souvent retards en raison de signal cellule incorrecte, nous vous recommandons de qu'utiliser l' [application Microsoft authentificateur](multi-factor-authentication-microsoft-authenticator.md) sur votre smartphone. L’application peut générer des codes de sécurité aléatoires que vous utilisez pour vous connecter, et ces codes ne nécessitent une connexion internet ou le signal de cellule.


## <a name="app-passwords-are-not-working"></a>Les mots de passe application ne fonctionnent pas

Tout d’abord, assurez-vous que vous avez correctement entré le mot de passe d’application.  Si elle ne fonctionne toujours pas essayez d’ouverture de session et [créer un nouveau mot de passe d’application](multi-factor-authentication-end-user-app-passwords.md).  Si cela ne pas travailler, contactez votre administrateur et demandez-lui de [supprimer vos mots de passe d’application existant](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) et que vous pouvez créer un nouvel identifiant.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Vous n’avez pas de trouver une réponse à mon problème.

Si vous avez essayé ces étapes de dépannage mais sont toujours en cours d’exécution des problèmes, contactez votre administrateur ou la personne qui a configuré l’authentification multifacteur pour vous. Ils doivent être en mesure de vous aider.

En outre, vous pouvez publier une question sur le [Azure AD Forums](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [contactez le support technique](https://support.microsoft.com/contactus) et nous allons répondre à votre problème dès que possible.

Si vous contactez le support technique, incluez les informations suivantes :

- **ID d’utilisateur** – en quelle est l’adresse de messagerie que vous avez tenté de vous connecter avec ?
- **Description générale de l’erreur** – quel message d’erreur exact vouliez-vous dire voir ?  Si aucun message d’erreur s’est produite, décrivez le comportement inattendu remarqué, dans le détail.
- **Page** – quelle page avez-vous sur lorsque vous l’avez vu l’erreur (inclure l’URL) ?
- **Code d’erreur** - vous recevez le code d’erreur spécifique.
- **ID de session** - l’id de session spécifiques que vous recevez.
- **ID de corrélation** – en ce qui a été le code d’id de corrélation généré lorsque l’utilisateur vu l’erreur.
- **Horodatage** – ce qui a été précis date et heure que vous avez vu l’erreur (inclure le fuseau horaire) ?

La plupart de ces informations sont accessibles dans votre page de connexion. Lorsque vous ne vérifiez votre connexion dans le temps, sélectionnez **Afficher les détails**.

![Se connecter détails de l’erreur](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Inclure les informations nous permet de résoudre votre problème aussi rapidement que possible.

## <a name="related-topics"></a>Rubriques connexes
- [Gérer vos paramètres pour la vérification](multi-factor-authentication-end-user-manage-settings.md)  
- [Forum aux questions sur application Microsoft Authenticator](multi-factor-authentication-app-faq.md)
