<properties 
    pageTitle="Résoudre les problèmes d’analyse de l’Application d’un projet web Java" 
    description="Guide de dépannage : surveillance des applications Java live avec des aperçus de l’Application." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Résolution des problèmes et questions/réponses et A pour avoir un aperçu Application pour Java

Questions ou problèmes avec [Visual Studio Application Insights dans Java][java]? Voici quelques conseils.


## <a name="build-errors"></a>Erreurs de génération

*Dans Eclipse, lorsque vous ajoutez le Kit de développement de perspectives Application via Maven ou Gradle, je reçois build ou somme de contrôle des erreurs de validation.*

* Si la dépendance <version> élément utilise un motif avec des caractères génériques (par exemple, (Maven) `<version>[1.0,)</version>` ou (Gradle) `version:'1.0.+'`), essayez de spécifier une version spécifique à la place comme `1.0.2`. Voir les [notes de publication](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) pour la version la plus récente.

## <a name="no-data"></a>Aucune donnée 

*J’ai ajouté perspectives d’Application et exécuté mon application, mais je n’ai jamais vu de données dans le portail.*

* Patientez quelques minutes et cliquez sur Actualiser. Les graphiques eux-mêmes actualiser régulièrement, mais vous pouvez également actualiser manuellement. L’intervalle d’actualisation dépend de la plage horaire du graphique.
* Vérifiez que vous disposez d’une clé d’instrumentation définie dans le fichier ApplicationInsights.xml (dans le dossier ressources dans votre projet)
* Vérifiez qu’il y a aucune `<DisableTelemetry>true</DisableTelemetry>` nœud dans le fichier xml.
* Dans votre pare-feu, vous devrez peut-être ouvrir les ports TCP 80 et 443 pour le trafic sortant à dc.services.visualstudio.com. Voir la [liste complète des exceptions de pare-feu](app-insights-ip-addresses.md)
* Dans le Microsoft Azure début Forum, consulter la carte d’état de service. S’il existe quelques indices alertes, patientez jusqu'à ce qu’ils ont renvoyé aux OK puis fermez et rouvrez votre carte application Application perspectives.
* Activer la journalisation dans la fenêtre de console IDE, en ajoutant une `<SDKLogger />` élément sous le nœud racine dans le fichier ApplicationInsights.xml (dans le dossier ressources dans votre projet) et rechercher les entrées de préface avec [erreur].
* Vérifiez que le fichier ApplicationInsights.xml correct a été correctement chargé par le Kit de développement Java, en consultant les messages de sortie de la console d’une instruction « fichier de Configuration a été trouvé ».
* Si ne trouve pas le fichier de configuration, consultez les messages de sortie pour afficher l’endroit où le fichier de configuration est recherché et vérifiez que le ApplicationInsights.xml se trouve dans un de ces emplacements de recherche. En règle générale, vous pouvez placer le fichier de configuration près de la JARs du Kit de développement logiciel Application perspectives. Par exemple : Tomcat, cela signifie que le dossier WEB-INF/bibliothèque.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>J’ai utilisé pour afficher des données, mais il est arrêté

* Consultez le [blog de l’état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous avez atteint votre quota mensuel de points de données ? Ouvrez Paramètres/Quota et tarification pour déterminer. Si c’est le cas, vous pouvez mettre à niveau votre plan ou payer pour une capacité supplémentaire. Consultez les [tarifs jeu](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Je ne vois pas toutes les données que je suis attendu.

* Ouvrez les Quotas et les prix carte et vérifiez si [échantillonnages](app-insights-sampling.md) est opérationnel. (100 % de transmission signifie qu’échantillonnages ne figure pas dans opération.) Le service d’analyse de l’Application peut être défini pour accepter seulement une fraction de télémétrie reçus depuis votre application. Cela vous permet de conserver au sein de votre quota mensuel de télémétrie. 

## <a name="no-usage-data"></a>Aucune donnée d’utilisation

*Je vois des données sur les demandes et les heures de réponse, mais pas en mode page, navigateur, ou utilisateur.*

Vous correctement configuré votre application pour envoyer de télémétrie à partir du serveur. À présent l’étape suivante consiste à [configurer vos pages web pour envoyer de télémétrie à partir du navigateur web][usage].

Par ailleurs, si votre client est une application dans un [téléphone ou un autre périphérique][platforms], vous pouvez envoyer télémétrie à partir de cet emplacement. 

Utiliser la même clé instrumentation pour configurer votre client et le serveur de télémétrie. Les données seront affichent dans la même ressource Application perspectives, et vous pourrez faire correspondre les événements de client et serveur.



## <a name="disabling-telemetry"></a>Désactivation de télémétrie

*Comment puis-je désactiver la collection de sites de télémétrie ?*

Dans le code :

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou** 

Mettre à jour ApplicationInsights.xml (dans le dossier ressources dans votre projet). Ajoutez le code suivant sous le nœud racine :

    <DisableTelemetry>true</DisableTelemetry>

Utilisez la méthode XML, vous devez redémarrer l’application lorsque vous modifiez la valeur.

## <a name="changing-the-target"></a>Modification de la cible

*Comment puis-je modifier les ressources Azure mon projet envoie des données au ?*

* [Obtenir la clé d’instrumentation de la nouvelle ressource.][java]
* Si vous avec le bouton droit ajouté analyse des applications à votre projet à l’aide de la boîte à outils Azure pour Eclipse, cliquez sur votre projet web, sélectionnez **Azure**, **Configurer Application perspectives**et modifier la clé.
* Dans le cas contraire, mettez à jour la touche en ApplicationInsights.xml dans le dossier de ressources dans votre projet.


## <a name="the-azure-start-screen"></a>L’écran d’accueil Azure

*Je recherche sur [le portail Azure](https://portal.azure.com). La carte indique quelque chose sur mon application ?*

Non, il affiche l’état des serveurs Azure dans le monde entier.

*À partir de la carte de démarrage Azure (écran d’accueil), comment trouver des données sur mon application ?*

En supposant que vous [Configurez votre application pour avoir un aperçu Application][java]cliquez sur Parcourir, Application perspectives et sélectionnez la ressource application que vous avez créé pour votre application. Pour obtenir il plus rapidement à avenir, vous pouvez épingler votre application dans le forum de démarrage.

## <a name="intranet-servers"></a>Serveurs intranet

*Puis-je analyser un serveur sur mon intranet ?*

Oui, à condition que votre serveur peut envoyer télémétrie au portail Insights Application via l’internet public. 

Dans votre pare-feu, vous devrez peut-être ouvrir les ports TCP 80 et 443 pour le trafic sortant dc.services.visualstudio.com et f5.services.visualstudio.com.

## <a name="data-retention"></a>Conservation des données 

*Combien de données sont conservées dans le portail ? Est sécurisé ?*

Voir [confidentialité et la rétention des données][data].

## <a name="next-steps"></a>Étapes suivantes

*Configurer la Application perspectives pour mon application serveur Java. Que puis-je faire d’autre ?*

* [Contrôler la disponibilité de vos pages web][availability]
* [Surveiller l’utilisation des pages web][usage]
* [Effectuer le suivi de l’utilisation et diagnostiquer les problèmes dans les applications de votre appareil][platforms]
* [Écrire du code pour effectuer le suivi de l’utilisation de votre application][track]
* [Capturer les journaux de diagnostic][javalogs]


## <a name="get-help"></a>Obtenir de l’aide

* [Débordement de la pile](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 