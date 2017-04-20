<properties
  pageTitle="Client et serveur SDK le contrôle de version dans les applications Mobile et de Services mobiles | Service application Azure"
  description="Liste de clients SDK et compatibilité avec les versions de kit de développement logiciel serveur pour Mobile Services et applications Mobile Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Contrôle de version client et serveur dans les applications Mobile et de Services mobiles

La dernière version de Services mobiles Azure est la fonctionnalité **Mobile applications** de Service d’application Azure.

Applications Mobile client et serveur kits de développement logiciel à l’origine basées sur celles de Services mobiles, mais ils sont *pas* compatibles avec eux.
En d’autres termes, vous devez utiliser un client *Mobile applications* SDK avec un serveur *d’Applications Mobile* SDK et de la même façon pour *Les Services mobiles*. Ce contrat est appliqué au moyen d’une valeur d’en-tête spécial utilisée par le client et serveur SDK, `ZUMO-API-VERSION`.

Remarque : chaque fois que ce document fait référence à un serveur principal de *Services mobiles* , il ne pas nécessairement être hébergées sur les Services mobiles. Il est désormais possible de migrer un service mobile pour exécuter sans modifier le code de service d’application, mais le service serait continue à utiliser les versions de kit de développement de *Services mobiles* .

Pour plus d’informations sur la migration sans modifier le code de service d’application, voir l’article [migrer un Service Mobile au Service d’application Azure].

## <a name="header-specification"></a>Spécification d’en-têtes

La touche `ZUMO-API-VERSION` peut être indiquée dans l’en-tête HTTP ou la chaîne de requête. La valeur est une chaîne de version dans le formulaire **x.y.z**.

Par exemple :

OBTENIR https://service.azurewebsites.net/tables/TodoItem

EN-TÊTES : ZUMO-API-VERSION : 2.0.0

VALIDER https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Désactivation de la vérification de la version

Vous pouvez désactiver la vérification en définissant la valeur **true** pour l’application **MS_SkipVersionCheck**de définition de la version. Spécifier dans votre fichier web.config ou dans la section Paramètres de l’Application du portail Azure.

> [AZURE.NOTE] Il existe un certain nombre de modifications de comportement entre Services mobiles et des applications mobiles, en particulier dans les zones de synchronisation hors connexion, l’authentification et les notifications push. Vous devez uniquement cesser d’utiliser vérification après tous les tests pour vous assurer que ces modifications comportement ne pas interrompu des fonctionnalités de votre application de la version.

## <a name="summary-of-compatibility-for-all-versions"></a>Résumé de compatibilité pour toutes les versions

Le tableau ci-dessous indique la compatibilité entre tous les types de client et serveur. Un serveur principal est classé comme Mobile **Services** ou les **applications** mobiles basé sur le serveur SDK qu’il utilise.

|                           | **Services mobiles** Node.js ou .NET | **Applications mobiles** Node.js ou .NET |
| ----------                | -----------------------             |   ----------------              |
| [Clients de Services mobiles] | Bien                                  | Erreur\*                         |
| [Clients applications Mobile]     | Erreur\*                             | Bien                              |

\*Cela peut être contrôlé en spécifiant **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Services mobiles clientes et serveur

Le client SDK dans le tableau ci-dessous sont compatibles avec **Les Services mobiles**.

Remarque : le client de Services mobiles SDK *ne pas* envoyer une valeur d’en-tête `ZUMO-API-VERSION`. Si le service reçoit cet en-tête ou la valeur de chaîne de requête, une erreur est renvoyée, sauf si vous avez explicitement choisi arrière comme décrit ci-dessus.

### <a name="MobileServicesClients"></a>Clients de *Services* mobiles SDK

| Plate-forme client                   | Version                                                                   | Valeur de l’en-tête version |
| -------------------               | ------------------------                                                  | -------------------  |
| Client géré (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/a                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/a                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/a                  |
| HTML                              | [1.2.7).](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/a     |

### <a name="mobile-services-server-sdks"></a>Serveur de *Services* Mobile SDK

| Plate-forme serveur  | Version                                                                                                        | En-tête de version acceptée |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* Version 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Aucun en-tête de version** |
| Node.js          | (bientôt disponible)                        | **Aucun en-tête de version** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportement de Services mobiles les serveurs principaux

| VERSION DE L’API ZUMO | Valeur de MS_SkipVersionCheck | Réponse |
| ---------------- | ---------------------------- | -------- |
| Pas spécifié    | Tout                          | 200 - OK |
| N’importe quelle valeur        | Vrai                         | 200 - OK |
| N’importe quelle valeur        | Faux/non spécifié          | 400 - Requête incorrecte |

## <a name="2.0.0"></a>Azure applications Mobile client et le serveur

### <a name="MobileAppsClients"></a>Client *applications* Mobile SDK

Vérification de la version a été introduite commençant par les versions suivantes du Kit de développement logiciel client pour **Les applications Azure Mobile**:

| Plate-forme client                   | Version                   | Valeur de l’en-tête version |
| -------------------               | ------------------------  | -----------------    |
| Client géré (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Serveur *d’applications* Mobile SDK

Vérification de la version est incluse dans server SDK versions suivantes :

| Plate-forme serveur  | KIT DE DÉVELOPPEMENT                                                                                                        | En-tête de version acceptée |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [Azure mobile applications)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportement des applications Mobile les serveurs principaux

| VERSION DE L’API ZUMO | Valeur de MS_SkipVersionCheck | Réponse |
| ---------------- | ---------------------------- | -------- |
| x.y.z ou Null    | Vrai                         | 200 - OK |
| Null             | Faux/non spécifié          | 400 - Requête incorrecte |
| 1.x.y            | Faux/non spécifié          | 400 - Requête incorrecte |
| 2.0.0-2.x.y      | Faux/non spécifié          | 200 - OK |
| 3.0.0-3.x.y      | Faux/non spécifié          | 400 - Requête incorrecte |


## <a name="next-steps"></a>Étapes suivantes

- [Migrer un Service Mobile au Service application Azure]


[Clients de Services mobiles]: #MobileServicesClients
[Clients applications Mobile]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrer un Service Mobile au Service application Azure]: app-service-mobile-migrating-from-mobile-services.md

