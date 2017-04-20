<properties
    pageTitle="Routage et Expressions de balise"
    description="Cette rubrique explique routage et balise expressions pour hubs notification Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Expressions de routage et de balise

##<a name="overview"></a>Vue d’ensemble

Balise expressions vous permettent de cibler des jeux spécifiques de périphériques ou plus précisément les enregistrements, lorsque vous envoyez une notification push via Hubs de Notification.


## <a name="targeting-specific-registrations"></a>Cibler les enregistrements spécifiques

Le seul moyen cible notification spécifique des enregistrements consiste à leur associer des balises, puis cibler ces balises. Comme indiqué dans [L’enregistrement gestion](notification-hubs-push-notification-registration-management.md), afin de recevoir des notifications push une application doit inscrire une poignée d’appareil sur un concentrateur de notification. Une fois qu’un enregistrement est créé sur un concentrateur de notification, le système principal application peut lui envoyer les notifications push.
Le système principal application pouvez choisir les enregistrements à cible avec une notification spécifique des façons suivantes :

1. **Diffusion**: tous les enregistrements dans le hub de notification recevoir une notification.
2. **Balise**: tous les enregistrements qui contiennent la balise spécifiée recevoir une notification.
3. **Expression de l’étiquette**: tous les enregistrements dont l’ensemble de balises correspond à l’expression spécifiée recevoir une notification.

## <a name="tags"></a>Balises

Un indicateur peut être une chaîne, jusqu'à 120 caractères, contenant alphanumériques et les caractères non alphanumériques : '_', ‘@’, '#', '. ',' : », «- ». L’exemple suivant montre une application à partir de laquelle vous pouvez recevoir des notifications de toast à propos des groupes de musique spécifique. Dans ce scénario, un moyen simple pour acheminer des notifications consiste aux enregistrements étiquette avec des balises qui représentent des bandes différentes, comme dans l’illustration suivante.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Dans cette image, le message balisé **Beatles** atteint uniquement la tablette enregistré avec la balise **Beatles**.

Pour plus d’informations sur la création des enregistrements pour les balises, voir [Gestion de l’enregistrement](notification-hubs-push-notification-registration-management.md).

Vous pouvez envoyer des notifications à des balises à l’aide des méthodes send les notifications de la `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe dans le Kit de développement logiciel [Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) . Vous pouvez également utiliser Node.js ou les API de reste les Notifications de transmission.  Voici un exemple d’utilisation du Kit de développement.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Les balises ne doivent pas nécessairement être préalablement généré et peuvent faire référence à plusieurs concepts spécifiques à l’application. Par exemple, les utilisateurs de cette application exemple peuvent commenter bandes et souhaitez recevoir toasts, non seulement pour les commentaires sur leurs groupes préférés, mais également pour tous les commentaires à partir de leurs amis, quelle que soit la bande sur lequel ils sont commenter. L’image suivante montre un exemple de ce scénario :



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Dans cette image, Alice est intéressée mises à jour pour Beatles et Alexandre est intéressé mises à jour des Wailers. Alexandre est également intéressé par des commentaires de Charlie et Charlie se trouve dans les Wailers vous intéresse. Lorsqu’une notification est envoyée commentaire de Charlie sur Beatles, Alice et Bob reçoit.

Pendant que vous pouvez encoder plusieurs problèmes de balises (par exemple, « band_Beatles » ou « follows_Charlie »), les balises sont des chaînes simples et non des propriétés avec les valeurs. Un enregistrement de correspondance uniquement sur la présence ou non d’une balise spécifique.

Didacticiel complète sur l’utilisation de balises pour envoyer à des groupes d’intérêt, voir [Transmettre les nouvelles](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Utilisation de balises pour cibler les utilisateurs

Une autre façon d’utiliser des balises consiste à identifier tous les périphériques d’un utilisateur particulier. Les enregistrements peuvent être marqués avec une balise qui contient un id d’utilisateur, comme dans l’illustration suivante :


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Dans cette image, le message balisé uid:Alice atteint tous les uid:Alice balisé des enregistrements ; Par conséquent, tous les périphériques d’Alice.


##<a name="tag-expressions"></a>Expressions de balise

Il existe des cas dans lequel la notification a afin de cibler un jeu d’enregistrements est identifié pas par un seul indicateur, mais d’une expression booléenne balises.

Envisagez d’une application sports qui envoie un rappel à tous les participants Boston concernant un jeu entre le Red Sox et Cardinals. Si l’application cliente enregistre balises concernant les intérêts dans les équipes et l’emplacement, la notification doit être ciblée à tous les participants Boston qui est intéressé par la loi Sox rouge ou les Cardinals. Cette condition peut être exprimée avec l’expression booléenne suivante :

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Balise expressions peuvent contenir tous les opérateurs booléens, tel que AND (& &), ou (|) et non ( !). Ils peuvent également contenir des parenthèses. Balise expressions sont limitées à 20 balises s’ils contiennent uniquement ORs ; dans le cas contraire, ils sont limités à 6 balises.

Voici un exemple d’envoi des notifications avec les expressions de balise à l’aide du Kit de développement.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
