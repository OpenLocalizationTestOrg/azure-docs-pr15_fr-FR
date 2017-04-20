<properties
   pageTitle="Prise en main des Services fiables | Microsoft Azure"
   description="Introduction à la création d’une application Microsoft Azure Service tissu avec les services avec et sans état."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Prise en main des Services fiables

> [AZURE.SELECTOR]
- [C# sur Windows](service-fabric-reliable-services-quick-start.md)
- [Java sous Linux](service-fabric-reliable-services-quick-start-java.md)

Cet article explique les notions de base des Services fiables Azure Service tissu et allant de créer et déployer une application de Service fiable simple écrite en Java.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, vérifiez que vous disposez de l’environnement de développement de Service TISSU configurer sur votre ordinateur.
Si vous devez configurer votre messagerie, consultez [prise en main sur Mac](service-fabric-get-started-mac.md) ou [mise en route sur Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour commencer à utiliser des Services fiables, il vous suffit de comprendre quelques concepts de base :

 - **Type de service**: il s’agit de votre implémentation de service. Il est défini par la classe que vous rédigez qui étend `StatelessService` et tout autre code ou les dépendances utilisés dans celui-ci, ainsi qu’un nom et un numéro de version.

 - **Instance du service nommé**: pour exécuter votre service, vous créez des instances nommées de votre type de service, beaucoup comme vous créez instances d’objet d’un type de cours. Instances de service sont en réalité des instanciations d’objet de votre classe de service que vous écrivez. 

 - **Hôte de service**: les instances de service nommé vous créez requis pour exécuter à l’intérieur d’un hôte. L’hôte de service est un processus de l’endroit où les instances de votre service peuvent s’exécuter.

 - **L’inscription du service**: inscription regroupe tout. Le type de service doit être enregistré par le tissu de Service d’exécution dans un hôte de service pour autoriser tissu de Service créer des instances de celui-ci pour exécuter.  

## <a name="create-a-stateless-service"></a>Créer un service sans état

Commencez par créer une nouvelle application de Service tissu. Service tissu pour Linux comprend un Yeoman générateur pour fournir la structure d’une application de Service tissu avec un service sans état. Démarrez en exécutant la Yeoman suivant commande :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **Service sans état fiable**. Pour ce didacticiel, nommez l’application « HelloWorldApplication » et le service « HelloWorld ». Le résultat inclura des répertoires de la `HelloWorldApplication` et `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Mettre en œuvre le service

Ouvrez **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Ce cours définit le type de service et peut exécuter de code. L’API de service fournit deux points d’entrée pour votre code :

 - Une méthode de point d’entrée ouvertes, appelée `runAsync()`, où vous pouvez commencer les charges de travail, y compris les charges de travail longue cluster en cours d’exécution.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Un point d’entrée de communication dans lequel vous pouvez connecter votre pile de communication de choix. Il s’agit de l’endroit où commencer à recevoir des demandes d’utilisateurs et d’autres services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Dans ce didacticiel, nous allons étudier le `runAsync()` méthode point d’entrée. Il s’agit de l’endroit où vous pouvez immédiatement commencer à exécuter votre code.

### <a name="runasync"></a>RunAsync

La plateforme appelle cette méthode lorsqu’une instance d’un service est placé et vous êtes prêt à exécuter. Le cycle d’Ouvrir/fermer d’une instance de service peut se produire plusieurs fois sur la durée de vie du service dans son ensemble. Cela peut se produire pour diverses raisons, notamment :

- Le système déplace vos instances de service pour l’équilibrage des ressources.
- Erreurs se produisent dans votre code.
- L’application ou le système est mis à niveau.
- Le matériel sous-jacent connaît une panne.

Cette orchestration est gérée en tissu de Service pour conserver votre service de disponibilité et non ajustées correctement.

#### <a name="cancellation"></a>Annulation

Il est essentiel que votre code dans `runAsync()` pouvez arrêter l’exécution lors de la notification par tissu de Service. La `CompletableFuture` retourné par `runAsync()` est annulé lorsque Service TISSU requiert votre service pour arrêter l’exécution. L’exemple suivant montre comment gérer un événement d’annulation : 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Inscription du service

Types de service doivent être enregistrés par le Service tissu d’exécution. Le type de service est défini dans la `ServiceManifest.xml` et votre classe service qui mettent en œuvre, `StatelessService`. L’enregistrement de service est effectué dans le point d’entrée principal processus. Dans cet exemple, le point d’entrée principal processus est `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Exécuter l’application

La Yeoman la structure inclut un script gradle pour générer l’application et bash déployer des scripts et désactivez-déploiement de l’application. Pour exécuter l’application, tout d’abord créer l’application avec gradle :

```bash
$ gradle
```

Vous obtiendrez un package d’application tissu de Service qui peut être déployé à l’aide du Service tissu Azure infrastructure du langage commun. Le script install.sh contient les commandes Azure infrastructure du langage commun nécessaires pour déployer le package d’application. Suffit d’exécuter le script install.sh déploiement :

```bask
$ ./install.sh
```
