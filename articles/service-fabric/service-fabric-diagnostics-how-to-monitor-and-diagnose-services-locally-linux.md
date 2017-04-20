<properties
   pageTitle="Localement surveiller et diagnostiquer les services écrits avec Azure Service TISSU | Microsoft Azure"
   description="Découvrez comment contrôler et diagnostiquer vos services écrites à l’aide de Microsoft Azure Service tissu sur un ordinateur de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Surveiller et diagnostiquer les services dans une configuration de développement ordinateur local


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Surveillance, détection et diagnostic et résolution des problèmes autorisent des services continuer avec une interruption minimale à l’expérience utilisateur. Surveillance et les diagnostics sont essentiels dans un environnement de production déployée réel. Adoptant un modèle similaire pendant le développement de services garantit que le pipeline de diagnostic fonctionne lorsque vous passez à un environnement de production. Service TISSU facilite pour les développeurs de service mettre en œuvre des diagnostics pouvant fonctionner en toute transparence sur les configurations de développement local seul ordinateur et installations cluster de production réel.


## <a name="debugging-service-fabric-java-applications"></a>Débogage des applications de Service TISSU Java

Pour les applications Java, [plusieurs structures de journalisation](http://en.wikipedia.org/wiki/Java_logging_framework) sont disponibles. Dans la mesure où `java.util.logging` est l’option par défaut avec l’environnement, il est également utilisé pour les [exemples de code en github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  La section suivante explique comment configurer la `java.util.logging` framework. 
 
À l’aide de java.util.logging vous pouvez rediriger les journaux de votre application dans la mémoire, les flux de sortie, fichiers de la console ou sockets. Pour chacun de ces options, il existe des gestionnaires de par défaut déjà fournis dans le cadre. Vous pouvez créer un `app.properties` fichier pour configurer le Gestionnaire de fichier pour votre application rediriger tous les journaux vers un fichier local. 

L’extrait de code suivant contient un exemple de configuration : 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Le dossier vers lequel pointe la `app.properties` fichier doit se trouver. Après le `app.properties` fichier est créé, vous devez également modifier votre script de point d’entrée, `entrypoint.sh` dans les `<applicationfolder>/<servicePkg>/Code/` dossier pour définir la propriété `java.util.logging.config.file` à `app.propertes` fichier. L’entrée doit ressembler à l’extrait de code suivante :

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Résultats de cette configuration dans les journaux collectés de manière rotation en `/tmp/servicefabric/logs/`. Les **%u** et **%g** permettent de création de fichiers plus, avec les noms de fichiers mysfapp0.log, mysfapp1.log et ainsi de suite. Par défaut si aucun gestionnaire n’est pas configuré explicitement, le Gestionnaire de console est enregistré. Une pouvez afficher les journaux de journal système sous /var/log/syslog.
 
Pour plus d’informations, voir les [exemples de code en github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Étapes suivantes
Le code de traçage même ajouté à votre application fonctionne également avec les Diagnostics de vérification de votre application sur un cluster Azure. Consultez ces articles qui décrivent les différentes options pour les outils et décrivent comment vous pouvez les définir.
* [Comment collecter les journaux de Diagnostics Azure](service-fabric-diagnostics-how-to-setup-lad.md)
