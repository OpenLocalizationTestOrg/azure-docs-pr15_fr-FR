<properties
    pageTitle="Azure B2C annuaire Active : Jeton, session et configuration de l’authentification unique | Microsoft Azure"
    description="Jeton, session et configuration d’authentification unique dans Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure B2C annuaire Active : Jeton, session et configuration de l’authentification unique

Cette fonctionnalité vous permet d’un contrôle fin, sur une [stratégie de base](active-directory-b2c-reference-policies.md), de :
 
1. Durée de vie des jetons de sécurité émis par B2C Azure Active Directory (AD Azure).
2. Durée de vie des sessions d’applications web gérées par Azure AD B2C.
3. Authentification unique (SSO) comportement sur plusieurs applications et stratégies de votre client B2C.

Vous pouvez utiliser cette fonctionnalité dans votre client B2C comme suit :

1. Suivez ces étapes pour [accéder à la carte de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Cliquez sur **stratégies de connexion**. *Remarque : vous pouvez utiliser cette fonctionnalité sur n’importe quel type de stratégie, non seulement sur* *Stratégies de connexion***.
3. Ouvrez une stratégie en cliquant dessus. Par exemple, cliquez sur **B2C_1_SiIn**.
4. Cliquez sur **Modifier** en haut de la carte.
5. Cliquez sur **jeton, session et configuration d’authentification unique**.
6. Apportez les modifications voulues. En savoir plus sur les propriétés disponibles dans les sections suivantes.
7. Cliquez sur **OK**.
8. En haut de la carte, cliquez sur **Enregistrer** .

![Capture d’écran du jeton, session et configuration d’authentification unique](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configuration de la durée de vie des jetons

Azure AD B2C prend en charge le [protocole d’autorisation OAuth 2.0](active-directory-b2c-reference-protocols.md) pour activer l’accès sécurisé aux ressources protégées. Pour mettre en œuvre cette prise en charge, Azure AD B2C émet différents [jetons de sécurité](active-directory-b2c-reference-tokens.md). Voici les propriétés que vous pouvez utiliser pour gérer les durées de vie des jetons de sécurité émis par Azure AD B2C :

- **Accès & ID jeton durée de vie (minutes)**: la durée de vie du jeton PORTEUR OAuth 2.0 utilisée pour accéder à une ressource protégée. Azure AD B2C émet des jetons de ID uniquement pour le moment. Cette valeur appliquera à ainsi que les jetons d’accès lorsque nous ajoutons leur prise en charge.
   - Par défaut = 60 minutes.
   - Minimum (inclus) = 5 minutes.
   - Maximum (inclus) = 1 440 minutes.
- **Durée de vie jeton actualisation (jours)**: la durée maximale avant laquelle un jeton d’actualisation peut être utilisé pour acquérir un nouveau accès ou le jeton d’ID (et éventuellement un nouveau actualisation jeton, si votre application a été accordée le `offline_access` étendue).
   - Par défaut = 14 jours.
   - Minimum (inclus) = 1 jour.
   - Maximum (inclus) = 90 jours.
- **Jeton d’actualisation décalée de durée de vie fenêtre (jours)**: une fois cet intervalle de temps écoulé l’utilisateur est obligé de s’authentifier à nouveau, quelle que soit la période de validité de la plus récente actualiser jeton acquise par l’application. Il peut être fourni uniquement si le commutateur est défini sur **DELIMITEE**. Il doit être supérieure ou égale à la **actualisation jeton durée de vie (jours)** valeur. Si le commutateur est défini sur **Unbounded**, vous ne pouvez pas fournir une valeur spécifique.
   - Par défaut = 90 jours.
   - Minimum (inclus) = 1 jour.
   - Maximum (inclus) = 365 jours.

Voici quelques exemples d’utilisation que vous pouvez activer l’utilisation de ces propriétés :

- Autoriser un utilisateur rester connecté à une application mobile indéfiniment, dans la mesure où il est continuellement actif sur l’application. Vous pouvez le faire en définissant la **actualisation jeton coulissante fenêtre durée de vie (jours)** basculer vers **Unbounded** dans votre stratégie de connexion.
- Répondre aux exigences de conformité et de sécurité de votre secteur en définissant la durée de vie jeton d’accès appropriés.

## <a name="session-configuration"></a>Configuration de la session

Azure AD B2C prend en charge le [protocole d’authentification de OpenID se connecter](active-directory-b2c-reference-oidc.md) pour l’activation de connexion sécurisé aux applications web. Voici les propriétés que vous pouvez utiliser pour gérer les sessions de l’application web :

- **Durée de vie session (en minutes) dans le navigateur**: la durée de vie des cookies de session de B2C Azure AD qui se trouve sur le navigateur de l’utilisateur lors de l’authentification réussie.
   - Par défaut = 1 440 minutes.
   - Minimum (inclus) = 15 minutes.
   - Maximum (inclus) = 1 440 minutes.
- **Expiration de la session Web app**: Si ce commutateur a la valeur **absolue**, l’utilisateur est obligé de s’authentifier à nouveau après la période de temps spécifiée par au terme de la **durée de vie session (en minutes) dans le navigateur** . Si ce commutateur est défini sur **propagées** (paramètre par défaut), l’utilisateur reste connecté dans la mesure où l’utilisateur est actif en permanence dans votre application web.

Voici quelques exemples d’utilisation que vous pouvez activer l’utilisation de ces propriétés :

- Répondre aux exigences de conformité et de sécurité de votre secteur en définissant la session d’application web appropriée durée de vie.
- Forcer ré-l’authentification après une période donnée pendant l’interaction d’un utilisateur avec un composant haute sécurité de votre application web. 

## <a name="single-sign-on-sso-configuration"></a>Configuration authentification unique (SSO)

Si vous avez plusieurs applications et les stratégies de votre client B2C, vous pouvez gérer les interactions utilisateur entre eux à l’aide de la propriété de **configuration de l’authentification unique** . Vous pouvez définir la propriété à un des paramètres suivants :

- **Client**: il s’agit du paramètre par défaut. À l’aide de ce paramètre permet à plusieurs applications et les stratégies de votre client B2C partager la même session utilisateur. Par exemple, une fois qu’un utilisateur s’inscrit dans une application, Contoso vos achats, il ou elle peut également en toute transparence vous connecter à un autre pharmacie Contoso une, et à y accéder.
- **Application**: cela vous permet de maintenir une session utilisateur en mode exclusif pour une application, indépendamment des autres applications. Par exemple, si vous souhaitez que l’utilisateur à se connecter à Contoso pharmacie (avec les mêmes informations d’identification), même s’il est déjà connecté à vos achats Contoso, une autre application sur le même B2C du client. 
- **Stratégie**: cela vous permet de maintenir une session utilisateur en mode exclusif pour une stratégie, indépendamment des applications à l’utiliser. Par exemple, si l’utilisateur a déjà connecté et terminé une étape à plusieurs facteurs d’authentification (MFA), il peut être donné accès à des parties de sécurité accrue d’applications plusieurs tant que la session liée à la stratégie n’expire jamais.
- **Désactivé**: Ceci oblige l’utilisateur se dérouler voyage complet des utilisateurs à chaque exécution de la stratégie. Par exemple, cela permettra plusieurs utilisateurs à s’inscrire à votre application (dans un scénario de bureau partagé), même lorsqu’un utilisateur unique reste connecté pendant toute la durée.
