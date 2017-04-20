<properties
    pageTitle="Comment envoyer des notifications planifiées | Microsoft Azure"
    description="Cette rubrique décrit l’utilisation de Notifications planifiées avec Hubs de Notification Azure."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notifications Push, les notifications push, planification des notifications de transmission"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Comment : Envoyer des notifications planifiées


##<a name="overview"></a>Vue d’ensemble

Si vous avez un scénario dans lequel vous voulez envoyer une notification à un moment donné à l’avenir, mais ne disposez pas d’une méthode simple pour réactiver votre code principale pour envoyer la notification. Couche standard Hubs Notification prend en charge une fonctionnalité qui vous permet de planifier les notifications des 7 jours à l’avenir.

Lorsque vous envoyez une notification, utilisez simplement la classe [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) dans le Kit de développement Hubs Notification comme le montre l’exemple suivant :

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

En outre, vous pouvez annuler une notification précédemment planifiée à l’aide de son notificationId :

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Il n’existe aucune limite sur le nombre de notifications planifiées, que vous pouvez envoyer.