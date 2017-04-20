<properties
    pageTitle="Azure Service d’application pour les applications API web et mobile | Microsoft Azure"
    description="Découvrez comment Azure Application Service vous permet de développer, déployer et gérer les applications mobiles et web."
    keywords="service d’application, service application azure, application coût service, échelle, format SVG, le déploiement des applications, le déploiement des applications azure, paas, plateforme en tant que service, site Web, site web, web, azure mobile"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>Quel est le Service d’application Azure ?

*Application Service* est une [plateforme en tant que service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) offre de Microsoft Azure. Créer web et des applications mobiles pour chaque plate-forme ou périphérique. Intégrer vos applications solutions logiciel-service, se connecter aux applications en local et automatiser des processus métier. Azure s’exécute vos applications sur une machine virtuelle entièrement gérée (machines virtuelles), avec un choix de ressources partagées de machine virtuelle ou machines virtuelles dédiés.

Application Service inclut le web et les fonctionnalités mobiles qui nous précédemment fournis séparément en tant que sites Web Azure et de Services mobiles Azure. Il inclut également des nouvelles fonctionnalités pour automatiser des processus métiers et l’hébergement cloud API. Comme un service intégré, application Service vous permet de composer des divers composants--sites Web, extrémités précédent de l’application mobile, API RESTful et processus métier--en une solution unique.

La vidéo 4 minutes suivante fournit une brève description de la relation entre application Service et les offres Azure antérieures et quelles sont les nouveautés dans celle-ci.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Pourquoi utiliser le Service d’application ?

Voici certaines fonctionnalités clés et des fonctions de Service d’application :

- **Plusieurs langues et structures** - application Service a prise en charge exhaustive pour ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [Windows PowerShell et d’autres scripts ou des exécutables](../app-service-web/web-sites-create-web-jobs.md) sur machines virtuelles application Service.

- **Optimisation DevOps** - configurer [déploiement et l’intégration continue](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Promouvoir les mises à jour par le biais [de test et intermédiaire environnements](../app-service-web/web-sites-staged-publishing.md). Effectuer [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gérer vos applications dans le Service d’application à l’aide de [PowerShell Azure](../powershell-install-configure.md) ou l' [interface de ligne de disponibilité sur plusieurs plateformes (commande)](../xplat-cli-install.md).

- **Global évoluer disponibilité** - échelle [vers le haut](../app-service-web/web-sites-scale.md) ou [Déconnecter](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Héberger vos applications n’importe où dans l’infrastructure de Microsoft Centre de données global et le Service d’application [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promet disponibilité.

- **Connexions à SaaS plateformes et données locales** - opérer plus de 50 [connecteurs](../connectors/apis-list.md) pour les systèmes d’entreprise (par exemple, SAP, Siebel et Oracle), services SaaS (par exemple, force de vente et Office 365) et les services internet (par exemple, Facebook et Twitter). Accéder aux données locales à l’aide de [Connexions hybride](../biztalk-services/integration-hybrid-connection-overview.md) et [Réseaux virtuels Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sécurité et conformité** - application Service est [ISO, sécurité sociale et compatible PCI](https://www.microsoft.com/TrustCenter/).

- **Modèles d’application** - choisir parmi une liste complète des modèles d’application sur le [Azure Marketplace](https://azure.microsoft.com/marketplace/) qui vous permettent d’utiliser un Assistant pour installer le logiciel open source populaires tels que WordPress Joomla et Drupal.

- **Intégration de visual Studio** - outils dédiés dans Visual Studio rationalisent le travail de création, le déploiement et débogage.

## <a name="app-types-in-app-service"></a>Types d’application dans le Service d’application

Application Service propose plusieurs *types d’application*, chacun d'entre eux est destiné à un type spécifique de la charge de travail d’hôte :

- [**Applications web**](../app-service-web/app-service-web-overview.md) - pour héberger des applications web et sites Web.

- [**Applications mobiles**](../app-service-mobile/app-service-mobile-value-prop.md) Pour l’hébergement de l’application mobile back-end.

- [**Applications API**](../app-service-api/app-service-api-apps-why-best-platform.md) - pour API RESTful d’hébergement.

- [**Applications logique**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - pour automatisation des processus métier et intégration des systèmes et des données au sein de nuages sans écrire de code.

Word *application* ici fait référence aux ressources d’hébergement dédiés à l’exécution d’une charge de travail. Prise de notes « web app » par exemple, vous êtes habitué à penser d’une application web que les ressources de calcul et le code d’application qui ensemble fournir des fonctionnalités à un navigateur. Mais dans le Service d’application une *application web* est les ressources cluster fournissant Azure pour héberger votre code de l’application. Si votre application est composée d’un site web frontal et une API RESTful back-end, vous pouvez déployer les deux, à une application web ou vous pouvez déployer votre code frontal pour une application web et votre code principale pour une application API. Votre application peut comporter plusieurs applications de Service d’application de différents types.

## <a name="app-service-plans"></a>Plans de services d’application

[Application Service Plans](azure-web-sites-web-hosting-plans-in-depth-overview.md) spécifier le type de ressources de calcul qui vos applications s’exécutent sur. Si vous prévoyez de trafic légère, vous pouvez utiliser des machines virtuelles partagés (**libre** et **Shared** tarifs niveaux). Pour les charges plus élevées, vous pouvez choisir parmi plusieurs tailles de machines virtuelles dédiés (niveaux de**base**, **Standard**et **Premium** ). Plusieurs applications de Service d’application peuvent partager la même offre, et ils adapter monter et Descendre les niveaux de prix ensemble dans l’offre.

Si vous avez besoin plus isolement réseau et extensibilité élevées, vous pouvez exécuter vos applications dans un [Environnement de Service d’application](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur la quantité coûts de Service d’application, voir [Application Service tarifs](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Testez l’application de Service

[Créer une application web exemple, l’application mobile ou application logique](http://go.microsoft.com/fwlink/?LinkId=523751) et essayez-le pour une heure, sans carte de crédit n’obligatoire, aucune engagements, sans soucis.

Ou ouvrez un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/), puis essayez l’un de nos didacticiels mise en route :

* [Didacticiel : Créer une application web](../app-service-web/app-service-web-get-started.md)
* [Didacticiel : Créer une application mobile](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Didacticiel : Créer une application API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Didacticiel : Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
