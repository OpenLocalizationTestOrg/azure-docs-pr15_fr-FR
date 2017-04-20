<properties
    pageTitle="Prise en main avec Hubs événement en c# avec vague d’Apache | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser Azure événement Hubs ; envoi d’événements c# et leur réception dans un cluster Apache vague."
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
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Prise en main avec Hubs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Événement Hubs est un système de réception hautement scalable qui peuvent d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les grandes quantités de données obtenus par vos périphériques connectés et les applications. Une fois collectées dans Hubs événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur analytique en temps réel ou un cluster de stockage.

Pour plus d’informations, voir [vue d’ensemble de l’événement Hubs].

Dans ce didacticiel, vous allez apprendre pour recevoir des messages à un concentrateur de l’événement à l’aide d’une application console en c# et les récupérer en parallèle à l’aide de vague d’Apache.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous supposons [Éclipse](https://www.eclipse.org/)

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, voir <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure la version d’évaluation gratuite</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez la classe **LogTopology** à partir de Eclipse, puis patientez pour qu’il démarre les récepteurs pour toutes les partitions.

2.  Exécutez le projet de **l’expéditeur** , appuyez sur **entrée** dans la fenêtre de la console et afficher les événements apparaissent dans la fenêtre récepteur.

    ![][22]

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé une application opérationnelle qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez déplacer sur aux scénarios suivants :

- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 