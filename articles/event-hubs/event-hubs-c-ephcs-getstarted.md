<properties
    pageTitle="Prise en main avec Hubs événement en C et c# | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser Azure événement Hubs ; envoi d’événements C et la réception de bordure en c# à l’aide de la EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Prise en main avec Hubs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Événement Hubs est un système de réception hautement scalable qui peuvent d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les grandes quantités de données obtenus par vos périphériques connectés et les applications. Une fois collectées dans Hubs événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur analytique en temps réel ou un cluster de stockage.

Pour plus d’informations, voir [Vue d’ensemble des Hubs événement][].

Dans ce didacticiel, vous allez apprendre pour recevoir des messages à un concentrateur de l’événement à l’aide d’une application console dans C et les récupérer en parallèle à l’aide de la bibliothèque c# [Hôte du processeur d’événements][] .

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Un environnement de développement C. Pour ce didacticiel, nous considérons la pile gcc sur une [Machine virtuelle Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) avec Ubuntu 14.04. Instructions pour d’autres environnements seront fournies dans les liens externes.

+ Microsoft Visual Studio Express pour Windows

+ Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez le projet **récepteur** à partir de Visual Studio, puis patientez pour qu’il démarre les récepteurs pour toutes les partitions.

    ![][21]

2.  Exécutez le programme de **l’expéditeur** et afficher les événements apparaissent dans la fenêtre récepteur.

    ![][24]

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé une application opérationnelle qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez déplacer sur aux scénarios suivants :

- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .
- [Vue d’ensemble des Hubs événement][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
