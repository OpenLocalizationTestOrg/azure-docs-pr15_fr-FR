<properties
    pageTitle="Les applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory | Microsoft Azure"
    description="Contrôle d’accès conditionnelle, Azure Active Directory vérifie pour des conditions spécifiques lors de l’authentification de l’utilisateur et autoriser l’accès aux applications."
    services="active-directory"
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
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory

Règles d’accès conditionnelle sont prises en charge dans Azure Active Directory (AD Azure)-applications connectées et intégration préalables logiciel fédérée en tant qu’une applications de service (SaaS), les applications qui utilisent le mot de passe session unique (SSO), des applications métier et les applications qui utilisent le Proxy d’Application Azure AD. Pour une liste détaillée des applications pour lesquelles vous pouvez utiliser access conditionnelle, consultez [les Services activés avec accès conditionnel](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Accès conditionnel fonctionne à la fois avec les applications de bureau et mobiles qui utilisent l’authentification moderne. Dans cet article, nous expliquer comment conditionnelle works access dans les applications mobiles et de bureau.

Vous pouvez utiliser les Azure AD connexion pages dans les applications qui utilisent l’authentification moderne. Avec une page de connexion, un utilisateur est invité à entrer pour l’authentification multifacteur. Un message s’affiche si l’accès de l’utilisateur est bloqué. Authentification moderne est requise pour l’appareil pour vous authentifier avec Azure AD, afin que les stratégies d’accès conditionnelle basée sur un appareil sont évaluées.

Il est important de savoir quelles applications peuvent utiliser les règles d’accès conditionnel et les étapes que vous devrez peut-être suivre pour sécuriser les autres points d’entrée application.

## <a name="applications-that-use-modern-authentication"></a>Applications qui utilisent l’authentification moderne

> [AZURE.NOTE] Si vous avez une stratégie d’accès conditionnel dans Azure AD qui dispose d’un équivalent dans Office 365, configurer les stratégies d’accès conditionnelle. Par exemple, ce serait appliquer aux stratégies d’accès conditionnel pour Exchange Online ou SharePoint Online.

Les applications suivantes prennent en charge l’accès conditionnel pour Office 365 et autres applications de service Azure connecté AD :

| Service cible  | Plateforme  | Application                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|Application courrier/calendrier/contacts, Outlook 2016, Outlook 2013 (avec authentification moderne), Skype entreprise (avec authentification moderne)|
|Office 365 Exchange Online| Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (avec authentification moderne), Skype entreprise (avec authentification moderne)|
|Office 365 Exchange Online|iOS, Android|  Application mobile Outlook|
|Office 365 Exchange Online|Mac OS X| Outlook 2016 pour l’authentification multifacteur et l’emplacement uniquement ; prise en charge de la stratégie basée sur un appareil prévue pour l’avenir, Skype support technique planifié à l’avenir|
|Office 365 SharePoint Online|Windows 10| Applications Office 2016, les applications Office universel, Office 2013 (avec l’authentification moderne), OneDrive for Business application (Client de synchronisation prochaine génération ou NGSC) prend en charge planifié à l’avenir, prise en charge des groupes Office prévue pour l’avenir, prise en charge de l’application SharePoint planifiée pour l’avenir|
|Office 365 SharePoint Online|Windows 8.1, Windows 7|Applications Office 2016, Office 2013 (avec authentification moderne), l’application OneDrive entreprise (client de synchronisation Groove)|
|Office 365 SharePoint Online|iOS, Android|  Applications Office mobile |
|Office 365 SharePoint Online|Mac OS X| Applications Office 2016 pour l’authentification multifacteur et l’emplacement uniquement ; prise en charge de la stratégie basée sur un appareil prévue pour l’avenir|
|Office 365 Yammer|Windows 10, iOS et Android | Application Office Yammer|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS et Android | Application Dynamics CRM|
|Service PowerBI|Windows 10, Windows 8.1, Windows 7, iOS et Android | Application PowerBI|
|Service application Remote Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android et Mac OS X |Application Remote Azure|
|N’importe quel service d’application mes applications|Android et iOS|N’importe quel service d’application mes applications |


## <a name="applications-that-do-not-use-modern-authentication"></a>Applications qui n’utilisent pas d’authentification moderne

Pour l’instant, vous devez utiliser d’autres méthodes pour bloquer l’accès aux applications qui n’utilisent pas d’authentification moderne. Règles d’accès pour les applications qui n’utilisent pas d’authentification moderne ne sont pas appliquées par access conditionnelle. Il s’agit principalement un facteur important pour l’accès Exchange et SharePoint. La plupart des versions antérieures des applications utilisent anciens protocoles de contrôle d’accès.

### <a name="control-access-in-office-365-sharepoint-online"></a>Contrôler l’accès dans Office 365 SharePoint Online
Vous pouvez désactiver les protocoles hérités pour l’accès à SharePoint à l’aide de l’applet de commande Set-SPOTenant. Utilisez cette applet de commande pour empêcher les clients Office qui utilisent des protocoles d’authentification moderne non à partir de l’accès aux ressources SharePoint Online.

**Exemple de commande**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Contrôler l’accès dans Office 365 Exchange Online

Exchange propose deux catégories principales de protocoles. Passez en revue les options suivantes, puis sélectionnez la stratégie est adaptée à votre organisation.

-   **Exchange ActiveSync**. Par défaut, les stratégies d’accès conditionnel pour l’authentification multifacteur et l’emplacement ne sont pas appliquées pour Exchange ActiveSync. Vous devez protéger l’accès à ces services en configurant la stratégie Exchange ActiveSync directement ou en bloquant Exchange ActiveSync à l’aide de règles de Services de fédération Active Directory (AD FS).
-   **Protocoles héritée**. Vous pouvez bloquer les protocoles hérités avec AD FS. Vous bloquez l’accès à des clients Office plus anciens, tels que Office 2013 sans l’authentification moderne est activée et les versions antérieures d’Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>AD FS permet de bloquer les protocole hérité

Vous pouvez utiliser les règles d’exemple suivantes pour bloquer l’accès protocole hérité au niveau AD FS. Le choix entre deux configurations courantes.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Option 1 : Autoriser Exchange ActiveSync et autoriser les applications héritées, mais uniquement sur l’intranet

Par les règles suivantes trois l’ADFS compter approbation tiers pour Microsoft Office 365 identité plateforme, le trafic Exchange ActiveSync et navigateur et le trafic d’authentification moderne, ont accès. Applications héritées sont bloquées depuis l’extranet.

##### <a name="rule-1"></a>Règle 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Règle 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Règle 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Option 2 : Exchange ActiveSync, verte et applications héritées

Par les règles suivantes trois l’ADFS compter approbation tiers pour Microsoft Office 365 identité plateforme, le trafic Exchange ActiveSync et navigateur et le trafic d’authentification moderne, ont accès. Applications héritées sont bloquées depuis n’importe où.

##### <a name="rule-1"></a>Règle 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Règle 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Règle 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
