<properties 
    pageTitle="Vous authentifier avec locale d’Active Directory dans votre application Azure | Microsoft Azure" 
    description="En savoir plus sur les différentes options pour les applications de professionnelles dans le Service d’application Azure pour vous authentifier avec Active Directory local" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Vous authentifier avec locale d’Active Directory dans votre application Azure #

Cet article vous explique comment vous authentifier avec Active Directory (AD) dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md)local. Une application Azure est hébergée dans le nuage, mais il existe moyens d’authentifier local utilisateurs AD en toute sécurité. 

## <a name="authenticate-through-azure-active-directory"></a>Authentifier via Azure Active Directory
Un client Azure Active Directory peut être directory synchronisé avec un locaux AD. Cette approche permet aux utilisateurs AD pour accéder à votre application à partir d’internet et s’authentifier à l’aide de leurs informations d’identification locales. En outre, Azure Application Service fournit une [solution clé en main pour cette méthode](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). En quelques clics seulement d’un simple clic, vous pouvez activer l’authentification avec un client de synchronisation d’annuaire pour votre application Azure. Cette approche présente les avantages suivants :

-   Ne nécessite pas de code de l’authentification dans votre application. Permettent de Service d’application faire l’authentification pour vous et votre temps consacrer de fournir des fonctionnalités dans votre application.
-   [API Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) autorise l’accès aux données de l’annuaire de votre application Azure.
-   Fournit l’authentification unique pour [toutes les applications pris en charge par Azure Active Directory](/marketplace/active-directory/), y compris Office 365, Dynamics CRM Online, Microsoft Intune et des milliers d’applications non Microsoft cloud. 
-   Azure Active Directory prend en charge le contrôle d’accès basé sur un rôle. Vous pouvez utiliser le modèle [Authorize(Roles="X")] avec peu de modifications à votre code.

Pour voir comment écrire une application Azure-métier qui authentifie avec Azure Active Directory, voir [créer une application Azure - métier avec l’authentification Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Authentification via un STS local
Si vous avez un local sécurisé jeton service (STS) tel que les Services de fédération Active Directory (AD FS), vous pouvez utiliser que fédérer l’authentification de votre application Azure. Cette approche est recommandée lors de la politique d’entreprise interdit données AD ne soient pas enregistrées dans Azure. Toutefois, notez les points suivants :

-   STS topologie doit être déployée en local avec coût et gestion de la charge.
-   Seuls les administrateurs AD FS peuvent configurer [confiance approbations tiers et les règles de revendications](http://technet.microsoft.com/library/dd807108.aspx), qui peuvent limiter les options du développeur. En revanche, il est possible gérer et personnaliser des [revendications](http://technet.microsoft.com/library/ee913571.aspx) sur une base de l’application.
-   Accès local données AD nécessitent une solution distincte à travers le pare-feu d’entreprise.

Pour voir comment écrire une application Azure-métier qui authentifie auprès d’un STS local, voir [créer une application Azure - métier avec l’authentification AD FS](web-sites-dotnet-lob-application-adfs.md).
 
