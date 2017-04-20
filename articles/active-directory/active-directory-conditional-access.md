<Properties
    pageTitle="Accès à conditionnelle Azure Active Directory | Microsoft Azure"  
    description="Utiliser le contrôle d’accès conditionnel dans Azure Active Directory pour vérifier des conditions spécifiques lors de l’authentification pour l’accès aux applications."  
    services="active-directory"
    keywords="accès conditionnel aux applications, accès conditionnel avec Azure AD, un accès sécurisé aux ressources d’entreprise, les stratégies d’accès conditionnel"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Accès conditionnel dans Azure Active Directory   

Les fonctionnalités de contrôle dans access conditionnelle Azure Active Directory (AD Azure) offrent des méthodes simples pour aider à sécuriser les ressources dans le cloud et en local. Les stratégies d’accès conditionnel telles que l’authentification multifacteur peut vous protéger contre les risques en matière de vol et les informations d’identification récoltées. Autres stratégies d’accès conditionnelle permettent de protéger les données de votre organisation. Par exemple, en plus des informations d’identification, vous devrez une stratégie que seuls périphériques êtes inscrit à un système de gestion des appareils mobiles tels que Microsoft Intune peuvent accéder aux services sensibles de votre organisation.


## <a name="prerequisites"></a>Conditions préalables

Azure access conditionnelle AD est une fonctionnalité [d’Azure Active Directory Premium](http://www.microsoft.com/identity). Chaque utilisateur qui accède à une application qui dispose des stratégies d’accès conditionnel appliqués doit disposer d’une licence Azure AD Premium. Vous pouvez en savoir plus sur la configuration requise de licence dans le [rapport des utilisateurs sans licence](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Comment le contrôle d’accès conditionnelle est appliqué ?  

Avec le contrôle d’accès conditionnel en place, Azure AD vérifie les conditions spécifiques que vous définissez pour un utilisateur pour accéder à une application. Une fois access conditions sont remplies, l’utilisateur est authentifié et accéder à l’application.  

![Vue d’ensemble de l’accès conditionnel](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Conditions

Il s’agit des conditions que vous pouvez inclure dans une stratégie d’accès conditionnelle :

- **L’appartenance aux groupes**. Contrôler l’accès d’un utilisateur en fonction de l’appartenance à un groupe.

- **Emplacement**. Utilisez l’emplacement de l’utilisateur pour l’authentification multifacteur déclencheur et utilisez les contrôles de bloc lorsqu’un utilisateur n’est pas sur un réseau approuvé.

- **Plateforme de l’appareil**. Utiliser la plate-forme de périphérique, tel qu’iOS, Android, Windows Mobile ou Windows, comme une condition pour appliquer la stratégie.

- **Périphérique activé**. État du périphérique, activé ou désactivé, est validée lors de l’évaluation de stratégie d’appareil. Si vous désactivez un périphérique de perte ou de vol de l’annuaire, l’événement peut n’est plus répondre aux exigences de la stratégie.

- **Risque de connexion et utilisateur**. Vous pouvez utiliser [la Protection Azure AD identité](active-directory-identityprotection.md) pour les stratégies de risque d’accès conditionnel. Stratégies de risque d’accès conditionnelle vous permettent de donner à votre organisation avance protection par risque événements et activités connexion inhabituelles.


## <a name="controls"></a>Contrôles

Il s’agit des contrôles que vous pouvez utiliser pour appliquer une stratégie d’accès conditionnel :

- **L’authentification multifacteur**. Vous pouvez nécessite l’authentification renforcée via l’authentification multifacteur. Vous pouvez utiliser l’authentification multifacteur avec l’authentification multifacteur Azure ou en utilisant un fournisseur de l’authentification multifacteur en local, combiné avec les Services de fédération Active Directory (AD FS). À l’aide de l’authentification multifacteur permet de protéger les ressources d’accéder à un utilisateur non autorisé qui peut-être avoir accédé aux informations d’identification d’un utilisateur valide.

- **Bloc**. Vous pouvez appliquer des conditions comme emplacement de l’utilisateur pour bloquer l’accès utilisateur. Par exemple, vous pouvez bloquer l’accès lorsqu’un utilisateur n’est pas sur un réseau approuvé.

- **Périphériques compatibles**. Vous pouvez définir les stratégies d’accès conditionnelle au niveau du périphérique. Vous pouvez définir une stratégie permettant uniquement les ordinateurs qui sont associés à un domaine ou des périphériques mobiles qui sont inscrits dans une application de gestion des périphériques mobiles, peuvent accéder aux ressources de votre organisation. Par exemple, vous pouvez utiliser Intune pour vérifier la conformité appareil et signaler à Azure AD pour l’application lorsqu’un utilisateur tente d’accéder à une application. Pour obtenir des instructions détaillées sur l’utilisation des Intune pour protéger des applications et des données, voir [protéger applications et des données avec Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Vous pouvez également utiliser Intune pour appliquer la protection des données pour les périphériques de perte ou de volés. Pour plus d’informations, consultez [l’aide de protéger vos données avec réinitialisation sélective ou complète à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Applications

Vous pouvez appliquer une stratégie d’accès conditionnelle au niveau de l’application. Définir les niveaux d’accès pour les applications et services dans le nuage ou sur site. La stratégie est appliquée directement au site Web ou au service. La stratégie est appliquée pour l’accès au navigateur et aux applications qui accèdent au service.


## <a name="device-based-conditional-access"></a>Accès conditionnelle basée sur un appareil

Vous pouvez limiter l’accès aux applications à partir d’appareils qui sont enregistrés avec Azure AD, et qui répondent aux conditions spécifiques. Accès conditionnelle basée sur un appareil protège les utilisateurs essaient d’accéder aux ressources à partir de ressources d’une organisation :

- Appareils inconnues ou non gérées.
- Appareils mobiles qui ne respectent pas les stratégies de sécurité de configurer votre organisation.

Vous pouvez définir des stratégies en fonction de ces exigences :

- **Appareils à un domaine**. Définir une stratégie pour restreindre l’accès aux appareils qui sont joints à un domaine Active Directory local, et qui sont également inscrits avec Azure AD. Cette stratégie s’applique aux bureaux, des ordinateurs portables et tablettes d’entreprise Windows.
Pour plus d’informations sur la configuration de l’enregistrement automatique des périphériques à un domaine avec Azure Active Directory, voir [configurer l’enregistrement automatique de Windows périphériques à un domaine avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Périphériques compatibles**. Définir une stratégie pour restreindre l’accès aux appareils qui sont marqués **conformes** dans le répertoire système de gestion. Cette stratégie garantit que seuls les périphériques qui répondent à des stratégies de sécurité telles que l’application de chiffrement des fichiers sur un appareil sont autorisés à accéder. Vous pouvez utiliser cette stratégie pour limiter l’accès à partir des périphériques suivants :

    - **Appareils à un domaine Windows**. Gérés par System Center Configuration Manager (dans la branche actuels) déployée dans une configuration hybride.
    - **Travail de Windows 10 Mobile ou d’un appareil personnel**. Gérés par Intune ou par un système de gestion des périphériques mobiles tiers pris en charge.
    - **appareils iOS et Android**. Géré par Intune.


Les utilisateurs qui accèdent aux applications qui sont protégées par basée sur un appareil, stratégie d’autorité de certification doit accéder à l’application d’un appareil qui répond aux exigences de cette stratégie. Accès refusé si vous avez essayé sur un appareil qui ne répond pas aux exigences de la stratégie.

Pour plus d’informations sur la configuration d’une stratégie d’autorité de certification basée sur l’appareil, dans Azure Active Directory, voir [définir une stratégie d’accès conditionnelle basée sur un appareil pour applications connectées Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Ressources

Consultez les catégories de ressources et articles pour en savoir plus sur la définition d’accès conditionnel pour votre organisation ci-dessous.


### <a name="multi-factor-authentication-and-location-policies"></a>Stratégies d’authentification et emplacement multifacteur

- [Mise en route d’accès conditionnel aux applications Azure connecté AD en fonction de groupe, l’emplacement et les stratégies d’authentification multifacteur](active-directory-conditional-access-azuread-connected-apps.md)
- [Applications qui sont prises en charge](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Accès conditionnelle basée sur un appareil

- [Définir la stratégie d’accès conditionnelle basée sur un appareil pour contrôler l’accès aux applications connecté Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
- [Configurer l’enregistrement automatique de Windows périphériques à un domaine avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Résolution des problèmes d’accès Azure Active Directory](active-directory-conditional-access-device-remediation.md)
- [Protection des données sur les périphériques de perte ou de volés à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Protéger des ressources en fonction de connexion à risque

-   [Protection d’identité AD Azure](active-directory-identityprotection.md)

### <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel des questions fréquentes](active-directory-conditional-faqs.md)
- [Guide de référence technique](active-directory-conditional-access-technical-reference.md)
