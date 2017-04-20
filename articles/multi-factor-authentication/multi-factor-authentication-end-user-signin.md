<properties
    pageTitle="Expérience de connexion de l’authentification Multifacteur Azure avec l’authentification multifacteur Azure"
    description="Cette page vous fournira des conseils sur où vous pouvez pour voir les différentes méthodes de connexion disponibles avec l’authentification Multifacteur Azure."
    keywords="l’authentification des utilisateurs, l’expérience de connexion, se connecter avec un téléphone mobile, se connecter avec un téléphone de bureau"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>La connexion expérience avec l’authentification multifacteur Azure
> [AZURE.NOTE]  La documentation suivante fournie sur cette page affiche une expérience de connexion classique.  Pour vous aider à se connecter, voir [vous rencontrez des problèmes avec l’authentification multifacteur Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>Quel sera votre expérience de connexion ?
Diffère selon la façon dont vous connectez et utilisez l’authentification multifacteur, votre expérience.  Dans cette section nous fournit des informations sur ce qui se passe lorsque vous vous connectez.  Choisissez celle qui décrit le mieux ce que vous faites :


Qu'est-ce que tu fais ?|Description
:------------- | :------------- |
[Pour vous connecter avec un téléphone mobile ou d’office](#signing-in-with-mobile-or-office-phone) | Voici ce que vous pouvez attendre de se connecter à l’aide de votre téléphone mobile ou office.
[Connexion à l’aide de l’application Microsoft Authenticator à l’aide de notification](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Voici ce que vous pouvez attendre à l’aide de l’application Microsoft Authenticator avec les notifications.
[Connexion à l’aide de l’application Microsoft Authenticator à l’aide de code de vérification](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Voici ce que vous pouvez attendre à l’aide de la thapp Microsoft Authenticator avec un code de vérification.
[Pour vous connecter avec une autre méthode](#signing-in-with-an-alternate-method)|Cela vous montrent à quoi s’attendre si vous souhaitez utiliser une autre méthode.

## <a name="signing-in-with-mobile-or-office-phone"></a>Pour vous connecter avec un téléphone mobile ou d’office

Les informations suivantes décrira l’expérience de l’utilisation de l’authentification multifacteur avec votre téléphone mobile ou office.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Pour vous connecter à l’aide d’un appel à votre bureau ou un téléphone mobile

- Se connecter à une application ou un service tel que Office 365 à l’aide de votre nom d’utilisateur et mot de passe.
- Microsoft vous appelle.

![Appels de Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Répondre au téléphone et appuyez sur la touche #.

![Réponse](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Vous devez maintenant être connecté.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Connexion à l’aide de l’application Microsoft Authenticator à l’aide de notification

Les informations suivantes décrira l’expérience de l’utilisation de l’authentification multifacteur avec l’application Microsoft Authenticator lorsqu’une notification est envoyée.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Pour vous connecter avec une notification envoyée à l’application Microsoft Authenticator

- Se connecter à une application ou un service tel que Office 365 à l’aide de votre nom d’utilisateur et mot de passe.
- Microsoft vous envoie une notification.

![Microsoft envoie une notification](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Répondre au téléphone et appuyez sur la touche vérifier.  Si votre société nécessite un code confidentiel vous demandera de celui-ci ici.

![Vérifier](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Vous devez maintenant être connecté.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Connexion à l’aide de l’application Microsoft Authenticator à l’aide de code de vérification

Les informations suivantes décrira l’expérience de l’utilisation de l’authentification multifacteur avec l’application Microsoft Authenticator lorsque vous l’utilisez avec un code de vérification.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Se connecter à l’aide d’un code de vérification avec l’application Microsoft Authenticator

- Se connecter à une application ou un service tel que Office 365 à l’aide de votre nom d’utilisateur et mot de passe.
- Microsoft vous demandera un code de vérification.

![Entrez le code de vérification](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Ouvrez l’application Microsoft Authenticator sur votre téléphone et entrez le code dans la zone où vous vous connectez.

![Obtenez le code](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Vous devez maintenant être connecté.


## <a name="signing-in-with-an-alternate-method"></a>Pour vous connecter avec une autre méthode


La section suivante montre comment connecter avec une autre méthode lorsque votre méthode principale ne soient pas disponible.

### <a name="to-sign-in-with-an-alternate-method"></a>Pour vous connecter avec une autre méthode

- Se connecter à une application ou un service tel que Office 365 à l’aide de votre nom d’utilisateur et mot de passe.
- Sélectionnez utiliser une option de vérification.  Vous serez présenter avec un éventail de différentes options. Le numéro que vous voir va être basée sur le nombre que vous avez définis.

![Utiliser une autre méthode](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Choisissez une autre méthode et se connecter.
