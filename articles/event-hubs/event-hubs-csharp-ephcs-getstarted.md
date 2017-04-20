<properties
    pageTitle="Prise en main avec Hubs événement en c# | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à l’aide d’Azure événement Hubs avec c# et l’utilisation de la EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Prise en main avec Hubs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Événement Hubs est un service qui traite de grandes quantités de données d’événement (télémétrie) à partir d’appareils connectés et les applications. Après avoir recueilli des données dans l’événement Hubs, vous pouvez stocker les données en utilisant un cluster de stockage ou transformer à l’aide d’un fournisseur analytique en temps réel. Cette fonctionnalité collecte et le traitement des événements à grande échelle est un composant clé des architectures d’applications modernes, y compris l’Internet des objets (IoT).

Ce didacticiel montre comment utiliser le portail classique Azure pour créer un concentrateur de l’événement. Il vous montre également comment collecter des messages à un concentrateur de l’événement à l’aide d’une application console écrite en c# et comment les récupérer en parallèle à l’aide de la bibliothèque c# [Hôte du processeur d’événements][] .

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un compte Azure actif. Si vous n’en avez pas, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À partir de Visual Studio, ouvrez le projet **récepteur** que vous avez créée.

2. Avec le bouton droit de la solution **récepteur** , puis cliquez sur **Ajouter**, puis cliquez sur **Projet existant**.
 
3. Recherchez le fichier Sender.csproj existant, puis double-cliquez dessus pour l’ajouter à la solution.
 
4. Là encore, avec le bouton droit de la solution **récepteur** , puis sur **Propriétés**. La page de propriétés **récepteur** s’affiche.

5. Cliquez sur **Projet de démarrage**, puis cliquez sur le bouton de **plusieurs projets de démarrage** . Définir la zone **Action** pour le **récepteur** et **l’expéditeur** projets pour **Démarrer**.

    ![][19]

6. Cliquez sur **dépendances du projet**. Dans la zone de **projets** , cliquez sur **l’expéditeur**. Dans la zone **dépend** , assurez-vous que **récepteur** est cochée.

    ![][20]

7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés** .

1.  Appuyez sur F5 pour exécuter le projet **récepteur** à partir de Visual Studio, puis patientez pour qu’il démarre les récepteurs pour toutes les partitions.

    ![][21]

2.  Le projet de **l’expéditeur** s’exécutera automatiquement. Appuyez sur **entrée** dans la fenêtre de la console et afficher les événements apparaissent dans la fenêtre récepteur.

    ![][22]

Appuyez sur **Ctrl + C** dans la fenêtre de **l’expéditeur** pour mettre fin à l’application de l’expéditeur, puis appuyez sur **entrée** dans la fenêtre récepteur pour arrêter cette application.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé une application opérationnelle qui crée un concentrateur de l’événement et envoie et reçoit des données, vous pouvez déplacer sur aux scénarios suivants :

- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .
- [Vue d’ensemble des Hubs événement][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Hôte du processeur d’événements]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
