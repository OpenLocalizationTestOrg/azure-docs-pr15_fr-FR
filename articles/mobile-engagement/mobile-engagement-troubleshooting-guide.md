<properties 
   pageTitle="Guides de dépannage Azure Engagement Mobile" 
   description="Guide de dépannage pour Azure Engagement Mobile" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Engagement Mobile - Guide de dépannage

## <a name="introduction"></a>Introduction
Guide de dépannage suivant vous aidera à comprendre les causes premières de certains problèmes couramment affichées et permettent de résoudre vos propres. 

## <a name="general"></a>Général

En règle générale, vous devez toujours vous assurer les éléments suivants :

1. Vous assurer que vous avez consulté toutes les étapes nécessaires pour l’intégration conformément à nos [didacticiels de prise en main](mobile-engagement-windows-store-dotnet-get-started.md)
2. Vous utilisez la dernière version de la plateforme SDK. 
3. Tester sur un périphérique réel et un émulateur car quelques-uns des problèmes sont spécifiques aux émulateur uniquement. 
4. Vous atteignez pas les limites/limitations d’Engagement Mobile qui sont présentées [ici](../azure-subscription-service-limits.md)
5. Si vous n’êtes pas en mesure de vous connecter à la version de serveur de service Mobile Engagement ou voir se chargent ne pas en permanence puis faire en sorte qu’il n’y a aucun incident de service en cours en vérifiant [ici](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problèmes de « Moniteur »

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Je ne vois pas mon périphérique apparaît sous l’onglet Moniteur
Onglet Moniteur affiche les appareils connectés à votre plateforme Mobile Engagement en temps réel. Si vous effectuez le débogage un émulateur et un périphérique, vous devez voir au moins une session ici. Si l’application a été distribuée, vous verrez l’indicateur de Sessions actives refléter les périphériques qui sont connectés à la plateforme en temps réel. 

Si vous ne voyez pas votre appareil sous l’onglet Moniteur, il est probablement un problème d’intégration SDK. Certaines étapes à suivre pour résoudre les problèmes courants sont les suivantes :

1. Vérifiez que vous utilisez la chaîne de connexion correcte dans l’application mobile et il est à partir de la section touches SDK et pas la section clés de l’API. La chaîne de connexion connecte à votre application mobile à l’instance de l’application Mobile Engagement dans laquelle vous verrez votre appareil sous l’onglet Moniteur. 
2. Pour la plateforme Windows - si votre page remplace la `OnNavigatedTo` méthode, veillez à appeler `base.OnNavigatedTo(e)`.
3. Si vous intégrez Engagement Mobile dans une application mobile existante, puis vous pouvez également vous assurer qu’il manque pas toutes les étapes en consultant les étapes d’intégration avancées [ici](mobile-engagement-windows-store-integrate-engagement.md)
4. Vérifiez que vous envoyez au moins un écran/activité en substituant la page avec EngagementActivity selon la plateforme que vous utilisez comme décrit dans les [didacticiels de prise en main](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>L’onglet Moniteur affichant une session apparaît même lorsque j’ai déconnecté ou fermé mon application / émulateur. 
Si vous êtes le seul connecté à la plateforme à ce stade et que vous utilisez un émulateur pour ouvrir l’application, il est probable en raison de faiblesses émulateur. En règle générale, vous devez vous assurer que vous revenez à l’écran d’accueil de l’émulateur pour la session d’application à déconnecter correctement. En outre, sur plate-forme Windows, pendant le débogage avec Visual Studio, vous devrez peut-être vérifier que dans Visual Studio, vous accédez à la barre de menus **Événements du cycle de vie** et cliquez sur **mise en veille** vraiment fermer la session. Pour plus d’informations, voir [didacticiel Windows](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>Problèmes de « Analytique »

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Je ne vois pas toutes les données / actualisation des données sous l’onglet Analytique 
Données Analytique sont recalculées régulièrement et cela peut prendre jusqu'à 24 heures pour cette actualisation. N’est pas ces données en temps réel et vous verrez qu’il actualisées pendant cette période de 24 heures.
Assurez-vous toutefois que vous voulez envoyer au moins un écran ou activité sur le serveur principal plateforme par soit substitution au moins une page associée `EngagementActivity` ou que vous appelez `SendActivity` explicitement. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Date/heure incorrectement capturée pour un appareil apparaît sous l’onglet Analytique
La période de temps pour Analytique est basée sur la date à partir des paramètres de périphérique de l’utilisateur. Assurez-vous que l’appareil comporte la date correctement définie. 

## <a name="segment-issues"></a>Problèmes 'Segment'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>J’ai créé un segment et il est apparaît en grisé ou n’apparaît ne pas toutes les données
La création de segment n’est pas en temps réel pour le moment. Il est calculé en même temps comme les données analytique sont agrégées et par conséquent cela peut prendre jusqu'à 24 heures. Vous devez revérifier plus tard, mais pendant ce temps vous devez également vous assurer que vos applications mobiles envoient bien les données sur la base desquels vous soyez former les segments. Par exemple Si un événement dites « foo » n’est pas envoyé par n’importe quel appareil mobile, puis il serait pas des données de segment pour un segment créé avec EventName = foo comme critère. Vous devez également vérifier l’intégration SDK afin de votre application mobile envoie correctement les données. 

## <a name="reach-or-push-notifications-issues"></a>Problèmes de « Joindre » ou les Notifications de transmission

### <a name="my-push-messages-are-not-being-delivered"></a>Mes messages push ne sont pas remis 

1. Essayez d’envoyer des notifications sur un appareil test tout d’abord afin de vous assurer que tous les composants - application mobile, Kit de développement logiciel et le service sont en mesure d’effectuer les notifications push et correctement connecté. 
2. Toujours envoyer tout d’abord la plus simple 'absence d’application notification via une campagne qui n’est pas planifiée et ni qu’il comporte n’importe quel critère audience spécifié. Il s’agit à nouveau pour prouver que la connectivité de notification fonctionne correctement. 
3. Si vous rencontrez des problèmes lors de la remise de notifications dans l’application, aussi il est une bonne première étape tente d’envoyer une notification d’absence de app tout d’abord à. 
4. Assurez-vous que la « diffuser Native » est correctement configuré pour votre application mobile. En fonction de la plateforme il impliquera soit clés (Android, Windows) ou des certificats (iOS). Voir [Interface utilisateur - paramètres](mobile-engagement-user-interface-settings.md)
5. Déconnecter de l’application notifications pourraient également être bloquées par l’utilisateur via le système d’exploitation mobile donc garantir que ceci n’est pas le cas. 
6. Assurez-vous que vous ne définissez pas l’option *Ignorer l’Audience, push sera envoyé aux utilisateurs via l’API* dans la section **campagne** d’une campagne accessibles, car cela garantit que les notifications push peuvent uniquement être envoyées par API. 
7. Vérifiez que vous testez votre campagne push avec les deux un appareil connecté via un réseau de l’opérateur Wi-Fi et téléphone afin d’éliminer la connexion de réseau de source des problèmes de synchronisation.
8. Vérifiez que la date/heure système sur votre appareil/émulateur est correcte car n’importe quel appareil pas synchronisé est également interférer avec capacité de transmission Notification du Service à fournir les notifications. 

Plateforme plus spécifique de résolution des problèmes d’instructions ci-dessous :

1. **iOS** 

    - Assurez-vous que les certificats sont valides et non expiré pour iOS Notifications de transmission. 
    - Vérifiez que vous configurez correctement un certificat de *Production* dans votre application Mobile Engagement. 
    - Vous assurer que vous testez sur un *appareil réel, physique.* Le simulator iOS Impossible de traiter les messages envoyés.
    - Assurez-vous que l’identificateur de groupe est correctement configuré dans l’application mobile. Consultez les instructions [ici](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Lorsque vous testez, utilisez distribution « Ad Hoc » dans votre profil de mise en service mobile. Vous ne pourrez pas recevoir une notification si votre application est compilée à l’aide de « Déboguer »

2. **Android**

    - Vérifiez que vous avez spécifié le nombre de projet correct dans fichier de AndroidManifest.xml de votre application mobile qui est suivi par caractère \n. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Vérifiez que vous ne sont pas manquantes ou incorrectement configuré des autorisations dans le fichier de manifeste Android 
    - Vérifiez que le numéro du projet que vous ajoutez à votre application client est de même compte où vous avez obtenu la clé du serveur GCM. Toute incompatibilité entre les deux empêchera votre pousse sortants. 
    - Si vous recevez des notifications système, mais pas dans l’application puis révision la [spécifier une icône pour la section notifications](mobile-engagement-android-get-started.md) comme susceptibles de vous ne spécifiez pas l’icône correcte dans le fichier de manifeste Android. 
    - Si vous envoyez une notification BigPicture, puis garantir que si vous avez des serveurs d’images externes puis ils doivent être en mesure de prendre en charge HTTP « GET » et « Chef ».

3. **Windows**
    
    - Vérifiez que vous avez associé à l’application une application du Windows Store valide. Dans Visual Studio - vous devez cliquez avec le bouton droit sur le projet et sélectionnez l’option « Associer application avec Store » et sélectionnez l’application que vous avez créé dans le Windows Store. Cette application du Windows Store doit être la même que celle à partir de l’endroit où vous vous trouvez les informations d’identification native push pour configurer le portail d’Engagement Mobile.
    - Si vous recevez des notifications de transmission hors de l’application mais pas dans l’application des notifications avec `EngagementOverlay` intégration puis assurez-vous est un élément de grille racine dans votre page. EngagementOverlay utilise le premier élément « Grille » détectés dans votre fichier xaml à ajouter web deux affichages dans votre page. Si vous souhaitez trouver l’endroit où définir les affichages web, vous pouvez définir une grille nommée « EngagementGrid » comme toutefois vous devrez Assurez-vous est suffisant hauteur et largeur pour le web ultérieures deux affichages qui s’affichent la notification et l’annonce suivante en tant que notification dans l’application :
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>J’ai créé une notification push/annonce/campagne et même après qu’il m’a envoyé la notification, il est affiché comme « Actif ». Que signifie ? 
La **campagne** que vous avez créé lors de l’Engagement Mobile est appelée car il s’agit d’une signification de notification push longue nouveaux appareils se connectent à votre plateforme engagement mobile, ils seront automatiquement envoyés la notification que vous configurez ici, dans la mesure où ils répondent au critère que vous définissez dans la campagne. Il s’agit pas une configuration notification unique capture d’une seule. Vous devrez manuellement Cliquez sur le bouton **Terminer** pour mettre fin à la campagne afin qu’il n’envoyer davantage les notifications. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>J’ai créé une campagne push et je suis recevoir des notifications par correctement toutefois chaque fois que j’ai ouvrir l’application, j’obtiens la même notification même lorsque j’ai eu actionnées avant ? 
Il s’agit susceptible de se produire pendant le test et si vous utilisez émulateurs ou certains infrastructure comme TestFlight de test. Quoi de neuf Voici qu’à chaque application exécutée instance, il est acquérir un identificateur nouveau et envoyer à notre principal qui est à l’origine de la plateforme Mobile Engagement pour le traiter comme un nouvel appareil et envoi de la notification. 

## <a name="getting-support"></a>Prise en charge

Si vous ne parvenez pas à résoudre le problème vous-même, vous pouvez :

1. Rechercher votre problème dans les threads existants sur forum StackOverflow et [forum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) et si pas puis poser une question il. 
2. Si vous trouvez une fonctionnalité manquants ajouter/vote puis pour la demande sur notre [forum UserVoice](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Si vous avez Microsoft prennent en charge ouvrir un incident de support en fournissant les informations suivantes : 
    - ID de l’abonnement Azure
    - Plateforme (par exemple, iOS, Android etc.)
    - ID d’application
    - ID de campagne (pour les problèmes de notification push)
    - ID de périphérique
    - Version du Kit de développement mobile Engagement (par exemple, Android SDK v2.1.0)
    - Détails de l’erreur avec message d’erreur exact et scénario
