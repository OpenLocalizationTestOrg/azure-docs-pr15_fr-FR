<properties
    pageTitle="Prise en main avec Hubs événement en Java avec vague d’Apache | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser Azure événement Hubs ; envoi d’événements avec Java et leur réception dans un cluster Apache vague."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>Prise en main avec Hubs d’événement

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction

Événement Hubs est un système de réception hautement scalable qui peuvent d’admission des millions d’événements par seconde, permettant à une application de traiter et analyser les grandes quantités de données obtenus par vos périphériques connectés et les applications. Une fois collectées dans Hubs événement, vous pouvez transformer et stocker des données à l’aide de n’importe quel fournisseur analytique en temps réel ou un cluster de stockage.

Pour plus d’informations, consultez la [Vue d’ensemble des Hubs événement][].

Ce didacticiel décrit comment collecter des messages à un concentrateur de l’événement à l’aide d’une application console dans Java et les récupérer en parallèle à l’aide de vague d’Apache.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Environnement de développement Java configuré pour exécuter [Maven](http://maven.apache.org/). Pour ce didacticiel, nous part du principe [Eclipse](https://www.eclipse.org/).

+ Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1.  Exécutez la classe **LogTopology** à partir de Eclipse, puis patientez pour qu’il démarre les récepteurs pour toutes les partitions.

2.  Exécutez le projet de **l’expéditeur** , appuyez sur **entrée** dans la fenêtre de la console et afficher les événements apparaissent dans la fenêtre récepteur.

    ![][22]

> [AZURE.NOTE] Dans ce didacticiel uniquement, utilisez vague d’en mode local à des fins de développement. Consultez [Vue d’ensemble de vague d’HDInsight][] et la documentation [Vague d’Apache][] officielle pour plus d’informations des modèles et les déploiements vague.

## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes sont disponibles pour développer des applications, intégration événement Hubs et vague de.

- [Analyse des données de capteur avec vague d’et HDInsight] est un didacticiel scénario complet à l’aide d’événement Hubs, vague d’et HBase pour ajouter des données de capteur dans un cluster Hadoop.
- [Développer des applications de traitement des données avec SCP.NET et c# sur vague d’et HDInsight de flux][] est un didacticiel sur l’écriture de pipelines vague à l’aide de c#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md

[Vague d’Apache]: https://storm.incubator.apache.org
[Vue d’ensemble de vague HDInsight]: ../hdinsight/hdinsight-storm-overview.md
[Analyse des données de capteur avec vague d’et HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Développer des applications de traitement des données avec SCP.NET et c# sur vague d’et HDInsight de flux]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 