<properties 
    pageTitle="Surveiller les dépendances, des exceptions et temps d’exécution dans les applications web Java" 
    description="Étendue de surveillance de votre site Web avec des aperçus Application Java" 
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
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Surveiller les dépendances, des exceptions et temps d’exécution dans les applications web Java

*Analyse de l’application est en mode Aperçu.*

Si vous avez [instrumenté votre application web Java avec des aperçus Application][java], vous pouvez utiliser l’Agent Java pour obtenir des informations plus approfondies, sans modifier le code :


* **Dépendances :** Données relatives aux appels qui rend votre application à d’autres composants, y compris :
 * **Appels reste** effectués via HttpClient, OkHttp et RestTemplate (printemps).
 * Appels **redis** effectués via le client Jedis. Si l’appel prend plu de 10 s, l’agent extrait également les arguments d’appel.
 * **[Appels JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, base de données Oracle ou Apache Derby DB. appels « executeBatch » sont pris en charge. Pour MySQL et PostgreSQL, si l’appel prend plu de 10 s, l’agent signale le plan de requête. 
* **Capturés exceptions :** Données sur les exceptions qui sont gérées par votre code.
* **Exécution de la méthode :** Données relatives à la durée que nécessaire pour exécuter des méthodes spécifiques.

Pour utiliser l’agent Java, installez-le sur votre serveur. Vos applications web doivent être équipées de l' [Application Insights Java SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Installer l’agent Application perspectives pour Java

1. Sur l’ordinateur exécutant votre serveur Java, [Téléchargez l’agent](https://aka.ms/aijavasdk).
2. Modifiez le script de démarrage d’application server et ajoutez la machine virtuelle Java suivant :

    `javaagent:`*chemin d’accès complet au fichier JAR agent*

    Par exemple, dans Tomcat sur un ordinateur Linux :

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Redémarrez votre serveur d’applications.

## <a name="configure-the-agent"></a>Configurer l’agent

Créez un fichier nommé `AI-Agent.xml` et le placer dans le même dossier que le fichier JAR agent.

Définir le contenu du fichier xml. Modifier l’exemple suivant pour inclure ou omettre les fonctionnalités que vous voulez. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Vous devez activer l’exception de rapports et le minutage de méthode pour les différentes méthodes.

Par défaut, `reportExecutionTime` est vraie et `reportCaughtExceptions` est fausse.

## <a name="view-the-data"></a>Afficher les données

Dans la ressource Application perspectives, agrégés temps d’exécution dépendance et procédé à distance apparaît [sous la vignette de performances][metrics]. 

Ouvrir la [recherche]pour rechercher des instances individuelles des rapports de dépendance, d’exception et méthode,[diagnostic]. 

[Problèmes de dépendance de diagnostic - obtenir des informations supplémentaires](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Questions ? Problèmes ?

* Aucune donnée ? [Exceptions de pare-feu ensemble](app-insights-ip-addresses.md)
* [Résolution des problèmes de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
