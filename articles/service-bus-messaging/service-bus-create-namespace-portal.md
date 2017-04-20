<properties
    pageTitle="Créer un espace de noms Bus des services à l’aide du portail Azure | Microsoft Azure"
    description="Pour commencer à Service, vous avez besoin d’un espace de noms. Voici comment en créer un à l’aide du portail Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Créer un espace de noms Bus des services à l’aide du portail Azure

Un espace de noms est un conteneur courantes pour tous les composants de votre messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un espace de noms, et espaces de noms servent souvent conteneurs d’applications. Il existe actuellement deux façons de créer un espace de noms Bus des services.

1.  Portail Azure (cet article)

2.  [Modèles de gestionnaire de ressources][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Créer un espace de noms dans le portail Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous venez de créer un espace de noms de Service de messagerie Bus.

## <a name="next-steps"></a>Étapes suivantes

Consultez nos [exemples GitHub] [ github-samples] qui présentent certaines des fonctionnalités plus avancées de la messagerie d’Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples