<properties 
   pageTitle="Informer les utilisateurs de données provenant de capteurs ou d’autres systèmes | Microsoft Azure"
   description="Décrit comment utiliser Hubs événement pour informer les utilisateurs de données."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Informer les utilisateurs de données provenant de capteurs ou d’autres systèmes

Supposons que vous avez une application qui surveille les données en temps réel, ou génère des rapports sur un planning. Si vous examinez le site Web sur lequel figurent les graphiques en temps réel ou des rapports, vous pourriez voir quelque chose qui nécessite une action. Que se passe-t-il si vous devez être alertée à ces situations, plutôt que d’utiliser sur n’oubliez pas de vérifier le site Web ? Imaginons que vous avez une lumière Agrandir dans une serre et que vous devez savoir immédiatement si la lumière éteint. Pour faire qui serait avec une sonde légère de serre, organisation à envoyer un message électronique si la lumière est désactivée.

![][1]

Dans un autre scénario, imaginons que vous exécutez une fonctionnalité aide compagnie et que vous devez être alerté pour niveaux d’alimentation stock faible. Par exemple, vous pourriez organiser à envoyer un message texte depuis votre système de planification si votre inventaire d’entrepôt des repas est réduit à un niveau critique. 

![][2]

Le problème est comment obtenir des informations critiques lorsque certaines conditions sont remplies, pas lorsque vous atteignez autour d’extraction d’un rapport statique. Si vous utilisez un [Concentrateur d’événement Azure][] ou [Azure IoT concentrateur][] pour recevoir des données à partir d’appareils ou des applications d’entreprise comme [Dynamics AX][], vous disposez de plusieurs options pour savoir comment les traiter. Vous pouvez de les consulter sur un site Web, vous pouvez les analyser, les stocker et vous pouvez les utiliser pour déclencher des commandes pour effectuer une opération. Pour ce faire, vous pouvez utiliser les outils puissants tels que [Des sites Web Azure][], [SQL Azure][], [HDInsight][], [Cortana Intelligence Suite][], [IoT Suite][], [Applications logique][]ou [Azure Notification Hubs][]. Mais parfois, vous souhaitez uniquement consiste à envoyer ces données à une personne avec un minimum de surcharge. Pour vous montrer comment procéder avec un peu de code, nous avons fourni un nouvel échantillon, [AppToNotifyUsers][]. Options incluses sont e-mail (SMTP), SMS et les téléphones.

## <a name="application-structure"></a>Structure de l’application

L’application est écrite en c#, et le fichier Lisez-moi dans l’échantillon contient toutes les informations que vous voulez modifier, créer et publier l’application. Les sections suivantes fournissent une vue d’ensemble de l’application.

Nous allons commencer en partant du principe que vous avez les événements critiques vous appuyez à un concentrateur d’événement Azure ou IoT concentrateur. N’importe quel concentrateur fera, tant que vous avez accès à celui-ci et connaissez la chaîne de connexion.

Si vous n’avez pas déjà un concentrateur événement concentrateur ou IoT, vous pouvez facilement configurer un lit de test avec une protection Arduino et un Pi framboises, en suivant les instructions dans le projet [Relier les points](https://github.com/Azure/connectthedots) . Le capteur d’éclairage sur la protection Arduino envoie les niveaux de lumière via la Pi à un [Concentrateur d’événement Azure][] (**ehdevices**), et une tâche [Azure flux Analytique](https://azure.microsoft.com/services/stream-analytics/) bascule alertes à un concentrateur événement deuxième (**ehalerts**) si les niveaux de lumière reçus inférieurs à un certain niveau.

**AppToNotify** au démarrage, il lit un fichier de configuration (App.config) pour obtenir l’URL et les informations d’identification pour le Hub événement recevoir des alertes. Il génère ensuite un processus pour surveiller en permanence qu’événement concentrateur pour tout message qui est fourni par l’intermédiaire – tant que vous disposez peut accéder à l’URL de l’événement concentrateur ou IoT concentrateur et les informations d’identification valides, ce code reader Hubs événement lira en continu les nouveautés. Lors du démarrage, l’application lit également l’URL et les informations d’identification pour le service de messagerie (téléphone de courrier électronique, SMS,) que vous voulez utiliser et le nom et adresse de l’expéditeur et une liste de destinataires.

Une fois que le moniteur d’événement concentrateur détecte un message, il déclenche un processus qui envoie le message à l’aide de la méthode spécifiée dans le fichier de configuration. Notez qu’il envoie chaque message que quand il détecte. Si vous définissez le moniteur pour qu’il pointe à un concentrateur de l’événement qui reçoit dix messages par seconde, l’expéditeur envoie des dix messages par seconde – messages dix électroniques par seconde, messages SMS dix par seconde, des appels téléphoniques dix par seconde. Pour cette raison, assurez-vous que vous surveillez un concentrateur événement recevant uniquement les alertes qui doivent être envoyée, pas un concentrateur événement recevant toutes les données brutes à partir de vos applications ou capteurs.

## <a name="applicability"></a>Conditions d’application

Le code dans cet exemple illustre uniquement comment contrôler l’événement Hubs et appeler des services de messagerie externe dans le cas où vous souhaitez ajouter cette fonctionnalité à votre application. Notez que cette solution est une soi-même, exemple destinés aux développeurs uniquement. Il ne traite pas les besoins de l’entreprise tels que redondance, basculement, redémarrez après une défaillance, etc.. Pour plus de solutions complets et de production, voir les rubriques suivantes :

- À l’aide des connecteurs ou les notifications push au moyen du service [Azure logique d’applications](../app-service-logic/app-service-logic-connectors-list.md) .
- À l’aide de [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx), comme décrit le blog de [notifications de transmission de diffusion à des millions d’appareils mobiles utilisant centraux et de Notification Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Étapes suivantes

Il est simple créer un service de notification simple qui envoie des messages électroniques ou des messages texte aux destinataires, les appels ou les, aux données de relais reçues par un concentrateur d’événement ou IoT concentrateur. Pour déployer la solution pour informer les utilisateurs en fonction de données reçues par ces hubs, visitez le site [AppToNotifyUsers][].

Pour plus d’informations sur ces hubs, voir les articles suivants :

- [Événement Azure Hubs]
- [Concentrateur IoT Azure]
- Commencer avec un [événement Hubs didacticiel].
- Un [exemple d’application qui utilise l’événement Hubs]complète.

Pour déployer la solution pour informer les utilisateurs en fonction des données reçues par ces hubs, visitez :

- [AppToNotifyUsers][]

[Didacticiel de Hubs d’événement]: event-hubs-csharp-ephcs-getstarted.md
[Concentrateur IoT Azure]: https://azure.microsoft.com/services/iot-hub/
[Événement Azure Hubs]: https://azure.microsoft.com/services/event-hubs/
[Concentrateur événement Azure]: https://azure.microsoft.com/services/event-hubs/
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sites Web Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Applications de logique]: https://azure.microsoft.com/services/app-service/logic/
[Notification Azure Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png