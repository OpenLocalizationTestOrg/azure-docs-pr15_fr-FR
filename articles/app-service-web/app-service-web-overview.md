<properties
    pageTitle="Vue d’applications Web | Microsoft Azure"
    description="Découvrez comment Azure Application Service vous permet de développer et héberger des applications web"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Vue d’ensemble des applications Web

*Application de Service Web Apps* est une plateforme cluster entièrement gérée qui est optimisée pour l’hébergement des applications web et sites Web. Cette offre de [plateforme en tant que service](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure vous permet de vous concentrer sur votre logique métier tout Azure prend en charge de l’infrastructure d’exécution et d’adapter vos applications.

La vidéo suivante 5 minutes présente Azure Application Service Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>Qu’est une application web dans le Service d’application ?

Dans le Service d’application, une *application web* est les ressources cluster fournissant Azure pour l’hébergement d’une site Web ou une application web.  

Les ressources de calcul est peut-être sur partagé ou dédiés les machines virtuelles (machines virtuelles), selon le niveau de tarification que vous choisissez. Code de votre application s’exécute dans une machine virtuelle gérée isolé provenant d’autres clients.

Votre code peut se trouver dans une langue ou un cadre est pris en charge par le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md), tel que ASP.NET, Node.js, Java, PHP ou Python. Vous pouvez également exécuter des scripts qui utilisent [PowerShell et autres langues d’écriture de script](web-sites-create-web-jobs.md#acceptablefiles) dans une application web.

Pour obtenir des exemples de scénarios d’application standard que vous pouvez utiliser des applications Web pour, voir [scénarios d’application Web](https://azure.microsoft.com/documentation/scenarios/web-app/) et la section **scénarios et recommandations** de [Service d’application Azure, comparaison Machines virtuelles, tissu de Service et les Services en nuage](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Pourquoi utiliser des applications Web ?

Voici certaines fonctionnalités clés du Service d’application qui s’appliquent aux applications Web :

- **Plusieurs langues et structures** - application Service a prise en charge exhaustive pour ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter [PowerShell et autres scripts ou des exécutables](../app-service-web/web-sites-create-web-jobs.md) sur machines virtuelles application Service.

- **Optimisation DevOps** - configurer [déploiement et l’intégration continue](../app-service-web/app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub ou BitBucket. Promouvoir les mises à jour par le biais [de test et intermédiaire environnements](../app-service-web/web-sites-staged-publishing.md). Effectuer [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gérer vos applications dans le Service d’application à l’aide de [PowerShell Azure](../powershell-install-configure.md) ou l' [interface de ligne de disponibilité sur plusieurs plateformes (commande)](../xplat-cli-install.md).

- **Global évoluer disponibilité** - échelle [vers le haut](../app-service-web/web-sites-scale.md) ou [Déconnecter](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Héberger vos applications n’importe où dans l’infrastructure de Microsoft Centre de données global et le Service d’application [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promet disponibilité.

- **Connexions à SaaS plateformes et données locales** - opérer plus de 50 [connecteurs](../connectors/apis-list.md) pour les systèmes d’entreprise (par exemple, SAP, Siebel et Oracle), services SaaS (par exemple, force de vente et Office 365) et les services internet (par exemple, Facebook et Twitter). Accéder aux données locales à l’aide de [Connexions hybride](../biztalk-services/integration-hybrid-connection-overview.md) et [Réseaux virtuels Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sécurité et conformité** - application Service est [ISO, sécurité sociale et compatible PCI](https://www.microsoft.com/TrustCenter/).

- **Modèles d’application** - choisir parmi une liste complète des modèles d’application sur le [Azure Marketplace](https://azure.microsoft.com/marketplace/) qui vous permettent d’utiliser un Assistant pour installer le logiciel open source populaires tels que WordPress Joomla et Drupal.

- **Intégration de visual Studio** - outils dédiés dans Visual Studio rationalisent le travail de création, le déploiement et débogage.

En outre, une application web peut tirer parti des fonctionnalités proposées par les [Applications de l’API](../app-service-api/app-service-api-apps-why-best-platform.md) (tel que CORS prend en charge) et [Les applications Mobile](../app-service-mobile/app-service-mobile-value-prop.md) (par exemple, les notifications push). Pour plus d’informations sur les types d’application dans le Service d’application, voir [Présentation du Service d’application Azure](../app-service/app-service-value-prop-what-is.md).

Outre les applications Web dans le Service d’application, Azure offre d’autres services pouvant être utilisées pour héberger les applications web et sites Web. La plupart des scénarios, applications Web est le meilleur choix.  Pour microservice architecture, envisagez [Tissu de Service](https://azure.microsoft.com/documentation/services/service-fabric)et si vous avez besoin de davantage de contrôle sur les ordinateurs virtuels que votre code s’exécute sur, envisagez [Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Pour plus d’informations sur le choix entre ces services Azure, voir [Service d’application Azure, Machines virtuelles, tissu de Service et comparaison des Services Cloud](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Prise en main

Pour commencer en déployant des exemples de code vers une nouvelle application web dans le Service d’application, suivez le didacticiel [déployer votre application web première vers Azure dans 5 minutes](app-service-web-get-started.md) . Vous avez besoin d’un compte Azure gratuit.

Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.
