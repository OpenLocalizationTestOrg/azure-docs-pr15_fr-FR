<properties
    pageTitle="Service d’application Azure : Mise à l’échelle des Applications de Service d’application"
    description="Découvrez les tenants et aboutissants de mise à l’échelle d’Application dans le Service d’application."
    keywords="application service, azure application service, plan de services application scalable, échelle, coût de service d’application"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Service d’application Azure : Mise à l’échelle des Applications de Service d’application

Applications hébergées dans le Service d’application Azure peuvent [Atteindre grande échelle](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Toutefois, la mise à l’échelle d’une application est un problème complexe qui ne dispose pas d’une solution « convenant à tous ». Sur une échelle de correctement votre application il sont 3 zones principales qui contribuent à votre succès d’applications :

1. Comprendre votre architecture d’application et ses faiblesses.
    * Est votre état d’Application ? Sans état ?
    * Quelles sont tous les composants de votre application ?
        * Où se trouvent les engorgements dans l’application ?
    * Lorsque le chargement est appliqué à votre application, les sauts de page premières ?
2. Comprendre les besoins de charge et les performances attendues.
    * L’application n’a besoin pour répondre à une milliers d’utilisateurs ? ou un million ?
    * Le trafic est transférés à partir d’un seul emplacement géographique ou globalement ?
    * Existe-t-il des variations saisonnières ? pointes de trafic ?
    * La vitesse doit répondre l’application ? 1 seconde ? 1 millisecondes ?
3. Comprendre et correctement Tirez parti de la plateforme d’hébergement de votre application.
    * Quelles sont les fonctionnalités dois-je utiliser pour atteindre mes objectifs échelle ?

Cette section vous aidera à comprendre tous les facteurs et aide vous concevoir une stratégie qui tire parti des fonctionnalités application Service nécessaires pour atteindre vos objectifs extensibilité élevées.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
