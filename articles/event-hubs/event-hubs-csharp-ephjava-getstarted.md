<properties
    pageTitle="Prise en main avec Hubs événement en c# | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser Azure événement Hubs ; envoi d’événements c# et leur réception dans Java à l’aide de la EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Prise en main avec Hubs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Événement Hubs est un service qui traite de grandes quantités de données d’événement (télémétrie) à partir d’appareils connectés et les applications. Après avoir recueilli des données dans l’événement Hubs, vous pouvez stocker les données en utilisant un cluster de stockage ou transformer à l’aide d’un fournisseur analytique en temps réel. Cette fonctionnalité collecte et le traitement des événements à grande échelle est un composant clé des architectures d’applications modernes, y compris l’Internet des objets (IoT).

Ce didacticiel montre comment utiliser le portail classique Azure pour créer un concentrateur de l’événement. Il vous montre également comment collecter des messages à un concentrateur de l’événement à l’aide d’une application console écrite en c# et comment les récupérer en parallèle à l’aide de la bibliothèque hôte du processeur d’événements Java.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un compte Azure actif. <br/>Si vous n’en avez pas, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez le projet **combiné** .

    ![][21]

2.  Exécutez le projet de **l’expéditeur** .

    ![][22]

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé une application opérationnelle qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez déplacer sur aux scénarios suivants :

- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .
- [Vue d’ensemble des Hubs événement][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
