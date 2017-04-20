<properties 
    pageTitle="Résolution des problèmes et Questions sur les aspects d’Application" 
    description="Un élément de Visual Studio Application Insights précisé ou ne fonctionne pas ? Essayez ici." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Questions - Application aperçus pour ASP.NET

## <a name="configuration-problems"></a>Problèmes de configuration

*Je rencontre des difficultés pour mon :*

* [Application .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Contrôle d’une application déjà en cours d’exécution](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnostics de Windows Azure](app-insights-azure-diagnostics.md)
* [Dans le navigateur Java](app-insights-java-troubleshoot.md)
* [D’autres plateformes](app-insights-platforms.md)

*Je ne reçois aucuns données à partir de mon serveur*

* [Exceptions de pare-feu ensemble](app-insights-ip-addresses.md)
* [Configurer un serveur ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurer un serveur Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>Puis-je utiliser Application analyse avec... ?

[Voir plateformes][platforms]


## <a name="is-it-free"></a>Est-ce gratuit ?

* Oui, si vous choisissez la gratuit [tarifs niveau](app-insights-pricing.md). Vous obtenez la plupart des fonctionnalités et un quota favorables de données. 
* Vous devez fournir des données de carte de crédit inscrire auprès de Microsoft Azure, mais sans frais impliquera, sauf si vous utilisez un autre payé pour Azure service, ou vous explicitement mettre à niveau vers un niveau de paiement.
* Si votre application envoie plus de données que le quota mensuel pour la couche gratuite, l’arrêt en cours d’enregistrement. Dans ce cas, vous pouvez choisir de début du paiement ou patienter jusqu'à ce que le quota est réinitialisé à la fin du mois.
* Données d’utilisation et session base ne sont pas soumis à un quota.
* Il existe également une évaluation gratuite de 30 jours, pendant laquelle vous offre les fonctionnalités payants gratuites.
* Chaque ressource application a un quota distinct, et vous définir son niveau de tarification indépendamment de celles des autres.

#### <a name="what-do-i-get-if-i-pay"></a>Ce que je reçois si je payer ?

* Un plus grand [quota mensuel de données](https://azure.microsoft.com/pricing/details/application-insights/).
* Option si vous payez 'limites d’âge » pour poursuivre la collecte de données via le quota mensuel. Si vos données dépasse de quota, vous êtes facturés par Mo.
* [Exporter continu](app-insights-export-telemetry.md).


## <a name="q14"></a>Ce que modifie-t-il Application Insights dans mon projet ?

Les détails dépendent du type de projet. Pour une application web :


+ Ajoute ces fichiers à votre projet :

 + ApplicationInsights.config. 
 + ai.js


+ Installe ces packages NuGet :

 -  *Application Insights API* - l’API principale

 -  *Application Insights API pour les Applications Web* - utilisé pour envoyer de télémétrie à partir du serveur

 -  *Application Insights API pour les Applications JavaScript* - utilisé pour envoyer de télémétrie à partir du client

    Les packages comprennent ces assemblys :

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insère des éléments dans :

 - Web.config

 - packages.config

+ (Nouveaux projets uniquement - si vous [Ajoutez des perspectives d’Application à un projet existant][start], vous devez effectuer cette opération manuellement.) Insère extraits dans le code client et serveur les initialisation avec l’ID de ressource Application perspectives. Par exemple, dans une application MVC, code est inséré dans la page maître Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Comment passer d’anciennes versions de kit de développement logiciel ?

Voir les [notes de publication](app-insights-release-notes.md) pour le Kit de développement correspondant à votre type d’application. 



## <a name="update"></a>Comment puis-je modifier les ressources Azure mon projet envoie des données au ?

Dans l’Explorateur de solutions, avec le bouton droit `ApplicationInsights.config` et sélectionnez **Mise à jour Application perspectives**. Vous pouvez envoyer les données à une ressource existante ou nouveau dans Azure. L’Assistant de mise à jour modifie la clé d’instrumentation dans ApplicationInsights.config, qui détermine l’endroit où le Kit de développement logiciel serveur envoie vos données. Sauf si vous désactivez l’option « Tout mettre à jour », elle remplacera également la clé où il apparaît dans vos pages web.


#### <a name="data"></a>Combien de données sont conservées dans le portail ? Est sécurisé ?

Consultez une [conservation des données et confidentialité][data].

## <a name="logging"></a>Journalisation

#### <a name="post"></a>Comment afficher les données de publication dans Rechercher des Diagnostics ?

Nous n’enregistre pas automatiquement les données de publication, mais vous pouvez utiliser un appel TrackTrace : placer les données dans le paramètre de message. Cela a une limite de taille plus longue que les limites des propriétés de chaîne, bien que vous ne pouvez pas filtrer. 

## <a name="security"></a>Sécurité

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Mes données est sécurisé dans le portail ? Combien il est conservé ?

Voir [données rétention et confidentialité][data].


## <a name="q17"></a>Ai activé tous les éléments de l’analyse des applications ?

<table border="1">
<tr><th>Vous devriez voir</th><th>Comment l’obtenir</th><th>Pourquoi souhaité</th></tr>
<tr><td>Graphiques de disponibilité</td><td><a href="../app-insights-monitor-web-app-availability/">Tests de site Web</a></td><td>Connaître que votre application web est vers le haut</td></tr>
<tr><td>Serveur application performance : temps de réponse,...
</td><td><a href="../app-insights-asp-net/">Ajouter des perspectives d’Application à votre projet</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Installer le moniteur d’état AI sur serveur</a> (ou écrire votre propre code pour <a href="../app-insights-api-custom-events-metrics/#track-dependency">suivre les dépendances</a>)</td><td>Détecter les problèmes de performances</td></tr>
<tr><td>Télémétrie de dépendance</td><td><a href="../app-insights-monitor-performance-live-website-now/">Installer le moniteur d’état AI sur serveur</a></td><td>Diagnostiquer les problèmes avec les bases de données ou d’autres composants externes</td></tr>
<tr><td>Obtenir les traces de la pile d’exceptions</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Insérer des appels TrackException dans votre code</a> (mais certains sont signalés automatiquement)</td><td>Détecter et diagnostiquer les exceptions</td></tr>
<tr><td>Recherche journal traces</td><td><a href="../app-insights-search-diagnostic-logs/">Ajouter une carte de journalisation</a></td><td>Diagnostiquer les exceptions, problèmes de performances</td></tr>
<tr><td>Notions fondamentales sur l’utilisation de client : affichages de page, des sessions,...</td><td><a href="../app-insights-javascript/">Initialiseur JavaScript dans les pages web</a></td><td>Analytique de l’utilisation</td></tr>
<tr><td>Indicateurs personnalisés client</td><td><a href="../app-insights-api-custom-events-metrics/">Suivi des appels dans les pages web</a></td><td>Améliorer l’expérience utilisateur</td></tr>
<tr><td>Mesures du serveur personnalisés</td><td><a href="../app-insights-api-custom-events-metrics/">Suivi des appels dans le code serveur</a></td><td>À la décision</td></tr>
</table>


## <a name="automation"></a>Automatisation

Vous pouvez [l’écriture de scripts PowerShell](app-insights-powershell.md) pour créer et mettre à jour des ressources d’analyse de l’Application.

## <a name="more-answers"></a>Autres réponses sont disponibles

* [Forum de perspectives d’application](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 