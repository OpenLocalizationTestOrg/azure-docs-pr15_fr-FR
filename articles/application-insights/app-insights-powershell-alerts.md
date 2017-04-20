<properties
    pageTitle="Utiliser Powershell pour définir des alertes dans perspectives d’Application"
    description="Automatiser la configuration des perspectives d’Application pour obtenir des messages électroniques concernant des modifications métriques."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utiliser PowerShell pour définir des alertes dans perspectives d’Application

Vous pouvez automatiser la configuration des [alertes](app-insights-alerts.md) dans [Visual Studio Application perspectives](app-insights-overview.md).

En outre, vous pouvez [définir webhooks pour automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Programme d’installation unique

Si vous n’avez pas utilisé PowerShell avec votre abonnement Azure avant :

Installez le module Azure Powershell sur l’ordinateur où vous voulez exécuter les scripts.

 * Installer [Microsoft Web Platform Installer (v5 ou une version ultérieure)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Utilisez-le pour installer Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Se connecter à Azure

Démarrez Azure PowerShell et [se connecter à votre abonnement](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Recevoir des alertes

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Ajout d’alerte


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Exemple 1

M’envoyer un e-mail si la réponse du serveur pour les demandes HTTP, plus de 5 minutes, la moyenne est inférieure à 1 seconde. Mes ressources Insights Application est appelée IceCreamWebApp, et il se trouve dans le groupe de ressources Fabrikam. Je suis propriétaire de l’abonnement Azure.

Le GUID est l’ID de l’abonnement (pas la clé d’instrumentation de l’application).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemple 2

J’ai une application dans lequel utiliser [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) pour signaler une métrique nommée « salesPerHour ». Envoyer qu'un message électronique à Mes collègues si « salesPerHour » est inférieur à 100, moyenne supérieures à 24 heures.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La même règle peut être utilisée pour la mesure signalée en utilisant le [paramètre de mesure](app-insights-api-custom-events-metrics.md#properties) de suivi d’un autre appel tels que TrackEvent ou trackPageView.

## <a name="metric-names"></a>Noms métriques

Métrique nom | Nom de l’écran | Description
---|---|---
`basicExceptionBrowser.count`|Exceptions de navigateur|Nombre d’exceptions non gérées levée dans le navigateur.
`basicExceptionServer.count`|Exceptions de serveur|Nombre d’exceptions non gérées levée par l’application
`clientPerformance.clientProcess.value`|Temps de traitement client|Temps écoulé entre recevoir le dernier octet d’un document jusqu'à ce que le DOM est chargé. Demandes asynchrone peuvent être encore en cours.
`clientPerformance.networkConnection.value`|Temps de connexion de réseau de chargement de page| Durée le navigateur pour vous connecter au réseau. Peut être 0 si mis en cache.
`clientPerformance.receiveRequest.value`|Réception de temps de réponse| Temps écoulé entre le navigateur envoyant demande au début de la réception de réponse.
`clientPerformance.sendRequest.value`|Envoyer la durée de la requête| Durée de navigateur pour envoyer la demande.
`clientPerformance.total.value`|Temps de chargement de page de navigateur|Temps écoulé jusqu'à ce que DOM, les feuilles de style, les scripts et les images sont chargées à la demande de l’utilisateur.
`performanceCounter.available_bytes.value`|Quantité de mémoire disponible|Mémoire physique immédiatement disponible pour un processus ou pour une utilisation du système.
`performanceCounter.io_data_bytes_per_sec.value`|Processus IO taux|Nombre total d’octets par seconde lecture et l’écriture de fichiers, réseau et appareils mobiles.
`performanceCounter.number_of_exceps_thrown_per_sec`|taux d’exception|Exceptions levées par seconde.
`performanceCounter.percentage_processor_time.value`|Processeur de processus|Le pourcentage de temps écoulé de tous les threads de processus utilisé par le processeur à exécuter les instructions pour le processus d’applications.
`performanceCounter.percentage_processor_total.value`|Temps processeur|Le pourcentage de temps que le processeur passe en threads non inactifs.
`performanceCounter.process_private_bytes.value`|Octets privés de processus|Mémoire en mode exclusif affectée au processus de l’application contrôlée.
`performanceCounter.request_execution_time.value`|Temps d’exécution requête ASP.NET|Temps d’exécution de la requête la plus récente.
`performanceCounter.requests_in_application_queue.value`|Requêtes ASP.NET dans la file d’exécution|Longueur de la file de demande d’application.
`performanceCounter.requests_per_sec`|Taux de requêtes ASP.NET|Taux de toutes les requêtes à l’application par seconde à partir d’ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Échecs de dépendance|Nombre d’appels échecs apportées par l’application serveur à des ressources externes.
`request.duration`|Temps réponse du serveur|Durée entre reçoit une demande HTTP et finition envoyer la réponse.
`request.rate`|Taux de requêtes|Taux de toutes les requêtes à l’application par seconde.
`requestFailed.count`|Échecs de requêtes|Requêtes compter de HTTP qui a donné lieu à un code de réponse > = 400
`view.count`|Affichages de page|Nombre de demandes d’utilisateur client pour une page web. Le trafic synthétique est filtré.
{votre nom métrique personnalisé}|{Votre nom métrique}|Votre valeur métrique signalées par [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou dans le [paramètre de mesures d’un appel de suivi](app-insights-api-custom-events-metrics.md#properties).

Les mesures sont envoyés par les modules de télémétrie différentes :

Groupe métrique | Module collecteur
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>affichage | [Navigateur JavaScript](app-insights-javascript.md)
performanceCounter | [Performances](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dépendance](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
demande,<br/>requestFailed|[Demande de serveur](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

Vous pouvez [automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure appelle une adresse web de votre choix lors d’une alerte.

## <a name="see-also"></a>Voir aussi


* [Script de configuration de perspectives d’Application](app-insights-powershell-script-create-resource.md)
* [Créer des ressources de test web et analyse des applications à partir de modèles](app-insights-powershell.md)
* [Automatiser coupler Microsoft Azure Diagnostics analyse des applications](app-insights-powershell-azure-diagnostics.md)
* [Automatiser votre réponse à une alerte](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
