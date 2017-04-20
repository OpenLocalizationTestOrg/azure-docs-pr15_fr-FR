<properties
    pageTitle="Résoudre 502 Passerelle incorrecte, 503 service indisponibles erreurs | Microsoft Azure"
    description="Résolution des problèmes 502 Passerelle incorrecte et 503 service indisponible les erreurs dans votre application web hébergé dans le Service d’application Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 Passerelle incorrecte, 503 service indisponible, erreur 503, erreur 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Résoudre les erreurs HTTP de « 502 Passerelle incorrecte » et « 503 service indisponible » dans vos applications web Azure

« 502 Passerelle incorrecte » et « 503 service indisponible » sont les erreurs courantes dans votre application web hébergé dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous permet de résoudre ces erreurs.

Si vous avez besoin d’une aide supplémentaire à tout moment dans cet article, vous pouvez contacter experts Azure sur [la Azure MSDN et les forums de débordement de pile](https://azure.microsoft.com/support/forums/). Par ailleurs, vous pouvez également créer un incident de support Azure. Accédez au [site d’assistance Azure](https://azure.microsoft.com/support/options/) et cliquez sur **Obtenir un Support**.

## <a name="symptom"></a>Manifestation

Lorsque vous accédez à l’application web, elle renvoie un HTTP erreur « 502 Passerelle incorrecte » ou un HTTP erreur « 503 Service non disponible ».

## <a name="cause"></a>Cause

Ce problème se produit souvent par des problèmes au niveau application, telle que :

-   demandes met beaucoup de temps
-   application à l’aide mémoire/processeur élevé
-   application cesse-t-il de fonctionner en raison d’une exception.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Étapes de dépannage pour résoudre « 502 Passerelle incorrecte » et les erreurs « 503 service indisponible »

Résolution des problèmes peuvent être divisée en trois tâches distinctes, dans un ordre séquentiel :

1.  [Observez et surveiller le comportement de l’application](#observe)
2.  [Collecte de données](#collect)
3.  [Limiter le problème](#mitigate)

[Application de Service Web Apps](/services/app-service/web/) vous propose différentes options à chaque étape.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observer et surveiller le comportement de l’application

####    <a name="track-service-health"></a>Effectuer le suivi de l’état du Service

Microsoft Azure publicizes chaque fois qu’il y a une dégradation des performances ou interruption du service. Vous pouvez suivre l’état du service sur le [Portail Azure](https://portal.azure.com/). Pour plus d’informations, voir [fonctionnement du service suivi](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Surveiller votre application web

Cette option permet de déterminer si votre application rencontre des problèmes. Carte de votre application web, cliquez sur la vignette **demandes et les erreurs** . La carte **métrique** montre tous les mesures que vous pouvez ajouter.

Parmi les mesures que vous pouvez surveiller pour votre application web

-   Mémoire moyenne jeu de travail
-   Temps de réponse moyen
-   Temps processeur
-   Utilisation de la mémoire
-   Demandes

![contrôler l’application web vers la résolution des erreurs HTTP 502 Passerelle incorrecte et 503 service indisponible](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Pour plus d’informations, voir :

-   [Surveiller des applications Web dans le Service d’application Azure](web-sites-monitor.md)
-   [Recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. collecte de données

####    <a name="use-the-azure-app-service-support-portal"></a>Utiliser le portail de Support Azure Application Service

Applications Web vous offre la possibilité de résoudre les problèmes liés à votre application web en consultant HTTP journaux, les journaux d’événements, les images complètes processus et autres éléments. Vous pouvez accéder à toutes ces informations à l’aide de notre portail de Support **http://&lt;votre nom de l’application >.scm.azurewebsites.net/Support**

Le portail du Service de l’application Azure prend en charge vous propose trois onglets distincts pour prendre en charge les trois étapes d’un scénario de résolution des problèmes courant :

1.  Observez le comportement actuel
2.  Analyser en collecte des informations de diagnostic et en exécutant les analyseurs intégrés
3.  Atténuer

Si le problème se produit immédiatement, cliquez sur **analyse** > **Diagnostics** > **Diagnostiquer maintenant** pour créer une session de diagnostic pour vous, qui collecterez HTTP consigne, Observateur d’événements, les images complètes, les journaux d’erreur PHP et PHP traitement état de mémoire.

Une fois que les données recueillies, il sera également exécuter une analyse des données et vous fournir un rapport HTML.

Au cas où vous voulez télécharger les données, par défaut, il est stocké dans le dossier D:\home\data\DaaS.

Pour plus d’informations sur le portail Azure Application Service prise en charge, voir [Nouvelles mises à jour à Extension de Site de prise en charge pour les sites Web Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Utiliser la Console de débogage Kudu

Applications Web est fourni avec une console de débogage que vous pouvez utiliser pour débogage, exploration, le téléchargement de fichiers, ainsi que les points de terminaison JSON pour obtenir des informations relatives à votre environnement. Cette option est appelée la _Console Kudu_ ou le _Tableau de bord SCM_ pour votre application web.

Vous pouvez accéder à ce tableau de bord en accédant au lien **https://&lt;votre nom de l’application >.scm.azurewebsites.net/**.

Parmi les éléments qui fournit des Kudu sont :

-   paramètres d’environnement pour votre application
-   flux de journal
-   vidage des Diagnostics
-   déboguer console dans laquelle vous pouvez exécuter les applets de commande Powershell et les commandes de base pratiques recommandées.


Une autre fonctionnalité utile de Kudu est que, au cas où votre application est lever des exceptions de première chance, vous pouvez utiliser Kudu et exporte l’outil SysInternals Procdump pour créer la quantité de mémoire. Ces images mémoire sont des instantanés du processus et peuvent souvent vous aider à résoudre les problèmes plus complexes avec votre application web.

Pour plus d’informations sur les fonctionnalités disponibles dans Kudu, voir [sites Web Azure outils en ligne que vous devez savoir sur](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. atténuer le problème

####    <a name="scale-the-web-app"></a>Échelle de l’application web

Dans le Service d’application Azure, pour améliorer les performances et de débit, vous pouvez ajuster l’échelle à partir duquel vous exécutez votre application. Mise à l’échelle d’une application web implique deux actions associées : modification de votre plan de services d’application à un niveau plus élevé de tarification et la configuration de certains paramètres une fois que vous avez basculé vers le niveau de prix supérieur.

Pour plus d’informations sur la mise à l’échelle, voir [échelle une application web dans le Service d’application Azure](web-sites-scale.md).

En outre, vous pouvez choisir d’exécuter votre application sur plusieurs instances. Cela non seulement vous offre une capacité de traitement plus, mais vous donne également certaines montant de tolérance de panne. Si le processus se déroule vers le bas sur une instance, l’autre instance continue servir les requêtes.

Vous pouvez définir la mise à l’échelle pour être manuelle ou automatique.

####    <a name="use-autoheal"></a>Utiliser AutoHeal

AutoHeal réutilise le processus de travail de votre application basée sur les paramètres que vous choisissez (par exemple, les modifications de configuration, demandes, limites en mémoire ou le temps nécessaire pour exécuter une requête). La plupart du temps, Corbeille le processus est le moyen le plus rapide pour résoudre un problème. Bien que vous pouvez toujours redémarrer l’application web à partir de directement à partir du portail Azure, AutoHeal fera automatiquement pour vous. Il vous souhaitez d’ajouter des déclencheurs de web.config racine de votre application web. Notez que ces paramètres seraient fonctionne de la même façon, même si votre application n’est pas un .net une.

Pour plus d’informations, voir [Correcteur automatique des Sites Web Azure](/blog/auto-healing-windows-azure-web-sites/).


####    <a name="restart-the-web-app"></a>Redémarrez l’application web

Il s’agit souvent la façon la plus simple pour récupérer des problèmes uniques. Dans le [Portail Azure](https://portal.azure.com/), sur la carte de votre application web, vous disposez des options pour arrêter ou redémarrer votre application.

 ![Redémarrez l’application pour résoudre les erreurs HTTP 502 Passerelle incorrecte et 503 service indisponible](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Vous pouvez également gérer votre application web à l’aide de Powershell Azure. Pour plus d’informations, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
