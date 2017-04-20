<properties
    pageTitle="Azure Active Directory identité Protection - comment débloquer les utilisateurs | Microsoft Azure"
    description="Découvrez comment débloquer les utilisateurs qui ont été bloqués par une stratégie de Protection d’identité Azure Active Directory."
    services="active-directory"
    keywords="protection d’identité Azure AD, débloquer utilisateur"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory identité Protection - comment débloquer les utilisateurs

Avec Protection d’identité Azure Active Directory, vous pouvez configurer des stratégies pour empêcher les utilisateurs si les conditions configurées sont satisfaites. En règle générale, un utilisateur bloqué contacts support technique déblocage. Les rubriques décrit les étapes que vous pouvez effectuer pour débloquer un utilisateur bloqué.


## <a name="determine-the-reason-for-blocking"></a>Déterminer le motif du blocage

Première étape consiste à débloquer un utilisateur, vous devez déterminer le type de stratégie qui a bloqué l’utilisateur, car les étapes suivantes sont associées. Avec Protection d’identité Azure Active Directory, un utilisateur peut également être bloqué soit par une stratégie de connexion à risque ou un utilisateur risque. 

Vous pouvez obtenir le type de stratégie qui a bloqué un utilisateur travaillant dans l’en-tête dans la boîte de dialogue qui a été présenté à l’utilisateur lors d’une tentative de connexion :

|Stratégie | Boîte de dialogue utilisateur|
|--- | --- |
|Connexion à risque | ![Connexion bloqué](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Risque d’utilisateur | ![Compte bloqué](./media/active-directory-identityprotection-unblock-howto/104.png) |


Un utilisateur qui est bloqué par :

- Une stratégie de risque de connexion est également connue sous connexion suspect
- Une stratégie de risque utilisateur est également connue sous un compte auprès de risque

 
## <a name="unblocking-suspicious-sign-ins"></a>Signe-ins suspects déblocage

Pour débloquer une connexion suspect, vous disposez des options suivantes :

1. **Se connecter à partir d’un emplacement de votre choix ou un appareil** - une raison courante de signe-ins suspects bloqués sont tentatives de connexion à partir d’emplacements inhabituels ou appareils. Vos utilisateurs peuvent rapidement déterminer s’il s’agit de la raison pour laquelle blocage en essayant de se connecter à partir d’un emplacement de votre choix ou appareil.


3. **Exclure de la stratégie** - si vous pensez que la configuration actuelle de votre stratégie de connexion à l’origine de problèmes pour des utilisateurs spécifiques, vous pouvez exclure les utilisateurs à partir de celui-ci. Voir [politique de risque de connexion](active-directory-identityprotection.md#sign-in-risk-policy) pour plus d’informations.
 
4. **Désactiver la stratégie** - si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Voir [politique de risque de connexion](active-directory-identityprotection.md#sign-in-risk-policy) pour plus d’informations.


## <a name="unblocking-accounts-at-risk"></a>Comptes déblocage exposés

Pour débloquer un compte au risque, vous disposez des options suivantes :

1. **Réinitialiser votre mot de passe** : vous pouvez réinitialiser le mot de passe. Voir [Réinitialiser votre mot de passe sécurisé manuel](active-directory-identityprotection.md#manual-secure-password-reset) pour plus d’informations.

2. **Ignorer tous les événements risque** - la stratégie de risque utilisateur bloque un utilisateur si le niveau de risque utilisateur configuré pour bloquer l’accès a été atteinte. Vous pouvez encore réduire un utilisateur du niveau de risque en fermant manuellement signalés événements risque. Pour plus d’informations, voir [clôture d’événements risque manuellement](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Exclure de la stratégie** - si vous pensez que la configuration actuelle de votre stratégie de connexion à l’origine de problèmes pour des utilisateurs spécifiques, vous pouvez exclure les utilisateurs à partir de celui-ci. Voir [politique de risque utilisateur](active-directory-identityprotection.md#user-risk-policy) pour plus d’informations.
 
4. **Désactiver la stratégie** - si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Voir [politique de risque utilisateur](active-directory-identityprotection.md#user-risk-policy) pour plus d’informations.




## <a name="next-steps"></a>Étapes suivantes

 Vous souhaitez en savoir plus sur la protection des données Azure AD personnelles ? Consultez [Azure Active Directory identité Protection](active-directory-identityprotection.md).
 

