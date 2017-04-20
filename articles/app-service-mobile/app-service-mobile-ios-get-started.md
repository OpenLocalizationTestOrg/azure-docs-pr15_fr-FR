<properties
    pageTitle="Créer une application iOS sur Azure Application Service Mobile applications | Microsoft Azure"
    description="Suivez ce didacticiel pour l’utilisation de l’application mobile Azure les serveurs principaux pour le développement iOS dans objectif-C ou Swift"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-ios-app"></a>Créer une application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter des [Applications Mobile Azure](app-service-mobile-value-prop.md), un service de serveur principal cloud, à une application iOS. Nous allons tout d’abord créer une nouvelle version de serveur mobile. Ensuite, nous allons utiliser une simple _liste Todo_ iOS application pour stocker les données dans Azure.

Pour effectuer ce didacticiel, vous avez besoin d’un Mac et [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)


## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Étape i : créer un nouveau contexte Azure application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Étape II : Configurer le projet principal

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Étape III : Téléchargez et exécutez l’application iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
