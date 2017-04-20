<properties 
    pageTitle="Notification Azure Hubs - instructions de diagnostic" 
    description="Instructions sur la façon de diagnostiquer les problèmes courants avec Hubs de Notification Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure Notification Hubs - instructions de diagnostic

##<a name="overview"></a>Vue d’ensemble

Une des questions plus courantes que nous entendre des clients Azure Notification Hubs est comment faire pour déterminer pourquoi ils ne voient pas une notification envoyée à partir de son serveur principal application apparaissent sur le périphérique client - où et pourquoi les notifications ont été supprimées et comment corriger ce problème. Dans cet article nous allons les différentes raisons pourquoi les notifications peuvent être perdues ou ne finissent pas sur les appareils. Nous allons également via façons dans lequel vous pouvez analyser et identifier la cause. 

Tout d’abord, il est essentiel de comprendre comment Azure Notification Hubs envoie des notifications pour les appareils.
![][0]

Dans un flux de notification Envoyer classique, le message est envoyé à partir de l' **application serveur principal** à **Azure Notification concentrateur (NH)** qui à son tour effectue un traitement sur tous les enregistrements en prenant en compte les balises configurés et les expressions de balise pour déterminer « cibles » c'est-à-dire que tous les enregistrements que vous avez besoin pour recevoir les notifications. Ces enregistrements peuvent s’étendre sur tout ou partie de notre plates-formes prises en charge - iOS, Google, Windows, Windows Phone, Kindle et Baidu pour Android Chine. Une fois les cibles établis, NH puis élaborer de notifications, divisée en plusieurs lot des enregistrements, à la plateforme périphérique spécifique **Du Service de Notification Push (solution)** - par exemple, APNS pour les ordinateurs Apple, GCM pour Google, etc.. NH authentifie avec la solution respectifs basée sur les informations d’identification que vous définissez dans le portail classique Azure dans la page Configurer les concentrateur de Notification. La solution transfère ensuite les notifications pour les **périphériques client**correspondantes. Il s’agit de la plateforme recommandée consiste à fournir les notifications push et notez que la branche finale d’une remise de notification a lieu entre la plateforme solution et l’appareil. Par conséquent, nous avons quatre composants principaux - *client*, *application serveur principal*, *Azure Notification Hubs (NH)* et *Services de Notification Push (solution)* et un de ces peuvent entraîner des notifications cesse déconnectées. Plus d’informations sur cette architecture est disponible dans la [Vue d’ensemble des Hubs de Notification].

Échec de livrer des notifications peut se produire pendant le test initial/intermédiaire phase indiquant un problème de configuration ou elle peut se produire dans production où tout ou partie des notifications peut obtenir indiquant que certaines applications plus approfondie rejetés ou messagerie problème de motif. Dans la section ci-dessous nous allons examiner différents scénarios notifications ignorés comprise entre courantes et le type rare, que certains d'entre eux vous trouverez peut-être évidentes et certaines autres pas trop. 

##<a name="azure-notifications-hub-mis-configuration"></a>Configuration de mauvaise concentrateur de Notifications Azure 

Azure Hubs Notification doit s’authentifier dans le contexte de l’application du développeur pour pouvoir envoyer correctement des notifications à la solution correspondantes. Cela est possible par le développeur de création d’un compte développeur avec la plateforme respectif (Google, Apple, etc. Windows), puis l’enregistrer leur application leur permettant d’obtenir les informations d’identification qui doivent être configurés dans le portail sous la section configuration Hubs de Notification. Si aucune notification n’apportez via, première étape doit être pour vous assurer que les informations d’identification correctes sont configurées dans le Hub de Notification de les associer avec l’application créé sous leur propre compte plateforme spécifiques pour les développeurs. Vous sera utile notre [Prise en main les didacticiels] de passez en revue ce processus d’une manière étape par étape. Voici des configurations mauvaise courantes :

1. **Général**
 
    a) à faire assurer que votre nom de concentrateur de notification (sans fautes de frappe) est la même :

    - Lorsque vous enregistrez à partir du client 
    - L’endroit où vous envoient des notifications à partir du serveur principal,  
    - Où vous avez configuré les informations d’identification de la solution et 
    - Dont vous avez configuré sur le client et le serveur principal les informations d’identification associations de sécurité. 
        
    b) Vérifiez que vous utilisez les chaînes sa configuration sur le client et le serveur principal application. En règle générale, vous devez utiliser le **DefaultListenSharedAccessSignature** sur le client et **DefaultFullSharedAccessSignature** sur le serveur principal application (qui donne l’autorisation pour pouvoir envoyer une notification à la NH)

2. **Configuration du Service de Notification Push Apple (APNS)**
 
    Vous devez garder deux hubs différents - une pour production et un autre pour le test objectif. Cela signifie télécharger le certificat que vous vous apprêtez à utiliser dans l’environnement sandbox à un concentrateur distinct et le certificat que vous souhaitez utiliser en production à un concentrateur distincte. N’essayez pas de télécharger les différents types de certificats au même concentrateur car elle peut entraîner des échecs de notification la ligne vers le bas. Si vous trouvez vous-même dans un emplacement où vous avez téléchargées par inadvertance différents types de certificat au même concentrateur, il est recommandé pour supprimer le hub et mettre à jour. Si pour une raison quelconque, vous ne pourrez pas supprimer le hub puis au minimum, vous devez supprimer tous les enregistrements existants à partir du hub. 

3. **Configuration de la messagerie de Cloud Google (GCM)** 

    a) à faire que que vous souhaitez activer « Google Cloud messagerie pour Android » sous votre projet cloud. 
    
    ![][2]
    
    b) à faire que de créer une « clé de serveur » tandis qu’obtenir les informations d’identification quel NH utilise pour vous authentifier avec GCM. 
    
    ![][3]
    
    c) à faire que que vous avez configuré « ID de projet » sur le client qui est une entité entièrement numérique que vous pouvez obtenir du tableau de bord :
    
    ![][1]

##<a name="application-issues"></a>Problèmes d’applications

1) **Les balises / ajouter des balises à des expressions**

Si vous utilisez des balises ou des expressions de balise segmenter votre public, il est toujours possible que lorsque vous envoyez la notification, il n’existe aucune cible n’a été trouvée basée sur les expressions de balises/balise que vous spécifiez dans votre appel envoyer. Il est préférable d’examiner vos enregistrements pour vous assurer qu’il existe des indicateurs qui correspondent aux lorsque vous envoyez une notification, puis vérifiez la réception de notification uniquement à partir des clients avec ces enregistrements. Par exemple Si tous vos enregistrements avec NH ont été réalisés avec affichent et indiquent que la balise « Politique » et que vous envoyez une notification avec sa balise « Sports », il ne sera pas envoyé à n’importe quel appareil. Un cas complexe peut impliquer expressions balise où vous enregistré uniquement avec « Balise A » ou « Balise B » mais lors de l’envoi des notifications, vous ciblez « Balise A & & balise B ». Dans la section diagnostiquer automatiquement les conseils ci-dessous, vous pouvez manières dans laquelle vous pouvez consulter vos enregistrements ainsi que les mots clés. 

2) **Problèmes de modèle**

Si vous utilisez des modèles, vous assurer que vous suivez les instructions décrites au [Guide de modèle]. 

3) **Enregistrements non valides**

En supposant que le Hub de Notification a été configuré correctement et toutes les expressions balises/balise ont été utilisées correctement ce qui crée de la recherche de cibles valides à laquelle les notifications doivent être envoyées, NH déclenche plusieurs lots de traitement en parallèle - chaque lot envoyant des messages à un jeu d’enregistrements. 

> [AZURE.NOTE] Étant donné que nous faire le traitement en parallèle, nous ne garantissons l’ordre dans lequel les notifications seront remises. 

Maintenant Azure Notifications concentrateur est optimisé pour un modèle de remise de message « une seule fois à la plupart ». Cela signifie que nous essayer une suppression des doublons pour qu’aucune notification n’est remise plusieurs fois sur un appareil. Pour ce faire, nous parcourez les enregistrements et vérifiez qu’un seul message est envoyé par identificateur d’appareil avant de l’envoyer le message à la solution. Comme chaque lot est envoyée à la solution, qui à son tour accepte et valider les enregistrements, il est possible que la solution détecte une erreur avec un ou plusieurs des enregistrements dans un lot, renvoie une erreur à Azure NH et arrête le traitement ainsi déplacer ce lot complètement. Ceci est particulièrement vrai avec APNS qui utilise un protocole de flux de données TCP. Bien que nous apportions optimisées pour les au plus une fois que la remise, dans ce cas, nous supprimer l’inscription erreur notre base de données et recommencez l’envoi des notifications pour le reste des périphériques dans ce lot.

Vous pouvez obtenir des informations d’erreur lors de la tentative d’échec de la remise par rapport à un enregistrement à l’aide de l’API REST de Azure Notification Hubs : [par Message télémétrie : télémétrie de Message de Notification obtenir](https://msdn.microsoft.com/library/azure/mt608135.aspx) et [Commentaires de la solution](https://msdn.microsoft.com/library/azure/mt705560.aspx). Voir la [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) code par exemple.

##<a name="pns-issues"></a>Problèmes de solution

Une fois que le message de notification a été reçu par la solution correspondantes, il est sa responsabilité d’effectuer la notification à l’appareil. Azure Hubs Notification n’appartient pas l’image ici et n’a aucun contrôle lorsqu’ou si la notification doit être remis à l’appareil. Dans la mesure où les services de notification de plateforme sont assez robustes, les notifications de tendent à atteindre les périphériques en quelques secondes à partir de la solution. Si la solution est toutefois limitation Azure Notification Hubs applique-t-il une stratégie d’interruption exponentielle et si la solution reste inaccessible pendant 30 minutes puis nous avons une stratégie en place pour arrive à expiration et supprimer définitivement les messages. 

Si une solution tente de remettre une notification, mais l’appareil est en mode hors connexion, la notification est stockée par la solution pour une période limitée et remise à l’appareil lorsqu’il est disponible. Qu’une seule notification récente pour une application spécifique est stockée. Si plusieurs notifications sont envoyées lorsque l’appareil est en mode hors connexion, chaque nouvelle notification entraîne la notification préalable seront perdues. Ce comportement de conserver uniquement la notification plus récent est appelé fusion des notifications dans APNS et réduction dans GCM (qui utilise une clé de réduction). Si le périphérique est toujours en mode hors connexion pour un certain temps, toutes les notifications qui ont été stockées pour qu’elle sont ignorées. Source - [recommandations APNS] & [conseils GCM]

Avec Hubs de Notification Azure - vous pouvez passer une clé de fusion via un en-tête HTTP en utilisant le modèle générique `SendNotification` API (par exemple pour .NET SDK – `SendNotificationAsync`) qui prend également les en-têtes HTTP qui sont passés comme consiste à la solution correspondantes. 

##<a name="self-diagnose-tips"></a>Conseils diagnostiquer automatiquement

Ici, nous allons examiner les différents moyens de diagnostiquer et racine entraîner des problèmes de concentrateur de Notification :

###<a name="verify-credentials"></a>Vérifier les informations d’identification

1. **Portail de développement de solution**

    Vérifier les au respectif portail de développement de solution (APNS, GCM, WNS etc.) à l’aide de notre [Prise en main les didacticiels].

2. **Portail classique Azure**

    Accédez à l’onglet configurer à consulter faire correspondre les informations d’identification avec celles obtenues à partir du portail développeur solution. 

    ![][4]

###<a name="verify-registrations"></a>Vérifier les enregistrements

1. **Visual Studio**

    Si vous utilisez Visual Studio pour le développement vous pouvez vous connecter à Microsoft Azure et afficher et gérer un ensemble de services Azure, y compris les Notifications concentrateur « Explorateur de serveurs ». Ceci est principalement utile pour votre environnement de développement/test. 

    ![][9]

    Vous pouvez afficher et gérer tous les enregistrements dans le hub classés façon optimisée pour plate-forme, native ou d’enregistrement de modèle, les balises, solution identificateur, id de l’enregistrement et la date d’expiration. Vous pouvez également modifier un enregistrement à la volée - qui est utile que si vous voulez modifier les balises. 

    ![][8]
 
    > [AZURE.NOTE] Les fonctionnalités Visual Studio afin de modifier les enregistrements doivent être utilisée uniquement au cours de développement/test avec un nombre limité d’enregistrements. Si il se présente un besoin pour résoudre vos enregistrements en bloc, envisagez d’utiliser la fonctionnalité d’enregistrement d’exportation/importation décrite ici - [D’exportation/importation des enregistrements] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorer Bus des services**

    De nombreux clients utilisent ServiceBus explorer décrites ici - [ServiceBus Explorer] pour afficher et gérer leur concentrateur de notification. Il s’agit d’un projet open source disponible à partir de code.microsoft.com - [code ServiceBus Explorer]

###<a name="verify-message-notifications"></a>Vérifier les notifications de message

1. **Portail classique Azure**

    Vous pouvez accéder à l’onglet « Débogage » pour envoyer des notifications de test à vos clients sans recourir à n’importe quel service principal vers le haut et en cours d’exécution. 

    ![][7]

2. **Visual Studio**

    Vous pouvez également envoyer les notifications de test depuis le confort de Visual Studio :

    ![][10]

    Vous pouvez en savoir plus sur les fonctionnalités de l’Explorateur de Visual Studio Notification concentrateur Azure ici - 
    
    - [Vue d’ensemble de l’Explorateur de serveur VS]
    - [Billet de Blog de l’Explorateur de serveur VS - 1]
    - [Billet de Blog de l’Explorateur de serveur VS - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Notifications d’échecs de débogage / analyse des résultats de notification

**Propriété EnableTestSend**

Lorsque vous envoyez une notification par Hubs de Notification, il simplement Obtient la file d’attente pour NH effectuer un traitement pour identifier le toutes ses cibles initialement et finalement NH envoie ensuite à la solution. Cela signifie que lorsque vous utilisez l’API REST ni aucun du Kit de développement logiciel client, l’un retour de votre appel envoyer uniquement signifie que le message a été correctement la file d’attente avec concentrateur de Notification. Il ne fournit un aperçu des passée une fois que NH finalement envoyer le message à solution. Si votre notification n’est pas arrivée au niveau du périphérique client, il est possible que lorsque NH essayé de remettre le message à la solution, une erreur par exemple, la taille de la charge utile a dépassé la valeur maximale autorisée par la solution s’est produite ou les informations d’identification configurées dans NH sont etc. non valide. Pour obtenir un aperçu des erreurs de la solution, nous avons mis en place une propriété appelée [EnableTestSend fonctionnalité]. Cette propriété est activée automatiquement lorsque vous envoyez des messages de test à partir du portail ou le client Visual Studio et par conséquent permet de vous permettent d’afficher des informations de débogage détaillées. Vous pouvez utiliser cette fonction via des API l’exemple du Kit de développement .NET où elle n’est disponible actuellement et sont ajoutés à tous les clients SDK finalement. Pour utiliser cette l’appel reste, consiste à ajouter un paramètre de chaîne de requête appelé « tester » à la fin de votre appel envoyer, par exemple 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Exemple (.NET SDK)*
 
Supposons que vous utilisiez .NET SDK pour envoyer une notification toast native :

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`s’il vous suffit état `Enqueued` à la fin de l’exécution sans tout claires sur votre push est passée. Vous pouvez désormais utiliser la `EnableTestSend` propriété booléenne lors de l’initialisation de la `NotificationHubClient` et accessible état détaillées sur les erreurs de solution rencontrés lors de l’envoi de la notification. L’appel envoyer ici prendra plus de temps pour retourner, car elle renvoie uniquement après que NH a remis la notification de solution pour déterminer le résultat. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Exemple de sortie*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Ce message indique soit informations d’identification non valides sont configurées dans le hub de notification ou un problème avec les enregistrements dans le hub et la marche à suivre recommandée auront pour supprimer cet enregistrement et laisser le client recréer avant d’envoyer le message. 
 
> [AZURE.NOTE] Notez que l’utilisation de cette propriété est très limitée et si vous ne devez utiliser ce dans l’environnement de développement/test avec un ensemble limité des enregistrements. Nous envoyer uniquement les notifications de débogage à 10 périphériques. Nous avons également une limite de traitement débogage envoie 10 par minute. 

###<a name="review-telemetry"></a>Passez en revue télémétrie 

1. **Utiliser Azure portail classique**

    Le portail vous permet d’obtenir un aperçu rapide de toutes les activités sur votre concentrateur de Notification. 
    
    a) à partir de l’onglet « tableau de bord », vous pouvez afficher une vue agrégée des enregistrements, les notifications ainsi que les erreurs par plate-forme. 
    
    ![][5]
    
    b) vous pouvez également ajouter plusieurs autres mesures spécifiques de plateforme de l’onglet « Moniteur » à Examinons plus approfondie en particulier les erreurs spécifiques solution renvoyée alors NH tente d’envoyer une notification à la solution. 
    
    ![][6]
    
    c) vous devez commencer par consulter les **Messages entrants**, **Opérations d’enregistrement**, **Les Notifications de réussite** , puis accédez à par onglet plateforme passer en revue les erreurs spécifiques de solution. 
    
    d) si vous avez le hub de notification configuré avec les paramètres d’authentification solution d’authentification d’erreur ne s’affiche. Il s’agit d’une bonne indication pour vérifier les informations d’identification de la solution. 

2) **Accès par programme**

Plus de détails ici- 

- [Télémétrie programmation Access]
- [Accès télémétrie par exemple API] 

> [AZURE.NOTE] Télémétrie plusieurs fonctionnalités liées comme **Des enregistrements d’exportation/importation**, **Access de télémétrie via des API** etc. sont disponibles uniquement en couche Standard. Si vous essayez d’utiliser ces fonctionnalités si vous êtes en libre ou couche base vous obtiendrez puis message d’exception à cet effet lors de l’utilisation du Kit de développement et un HTTP 403 (refusé) lors de leur utilisation directement à partir de l’API REST. Vérifiez que vous accédiez au Standard de niveau via le portail classique Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Vue d’ensemble des Hubs de notification]: notification-hubs-push-notification-overview.md
[Didacticiels de mise en route]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Conseils de modèle]: https://msdn.microsoft.com/library/dn530748.aspx 
[Conseils APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Conseils GCM]: http://developer.android.com/google/gcm/adv.html
[Les enregistrements d’exportation/importation]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorer ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[Code ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Vue d’ensemble de l’Explorateur de serveur VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Billet de Blog de l’Explorateur de serveur VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Billet de Blog de l’Explorateur de serveur VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Fonctionnalité EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Télémétrie programmation Access]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Accès télémétrie par exemple API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 