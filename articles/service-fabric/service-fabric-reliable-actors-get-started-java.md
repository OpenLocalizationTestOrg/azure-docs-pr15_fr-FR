<properties
   pageTitle="Prise en main Service TISSU fiable intervenants | Microsoft Azure"
   description="Ce didacticiel vous guide dans les étapes de création, le débogage et déploiement d’un service basé sur acteur simple à l’aide du Service TISSU fiable intervenants."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Prise en main intervenants fiable

> [AZURE.SELECTOR]
- [C# sur Windows](service-fabric-reliable-actors-get-started.md)
- [Java sous Linux](service-fabric-reliable-actors-get-started-java.md)

Cet article explique les notions de base des intervenants fiable Azure Service tissu et allant de créer et déployer une application acteur fiable simple en Java.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, vérifiez que vous disposez de l’environnement de développement de Service TISSU configurer sur votre ordinateur.
Si vous devez configurer votre messagerie, consultez [prise en main sur Mac](service-fabric-get-started-mac.md) ou [mise en route sur Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour commencer à utiliser intervenants fiable, il vous suffit de comprendre quelques concepts de base :

 * **Service acteur**. Intervenants fiables sont empaquetés dans des Services fiables qui peut être déployée dans l’infrastructure tissu de Service. Instances acteur sont activés dans une instance de service nommée.
 
 * **Enregistrement d’un acteur**. Comme avec des Services fiables, un service fiable acteur doit être enregistré avec le runtime tissu de Service. En outre, le type d’un acteur doit être enregistré par le runtime acteur.
 
 * **Interface acteur**. L’interface acteur est utilisée pour définir une interface fortement typée publique d’un acteur. Dans la terminologie de modèle acteur fiable, l’interface acteur définit les types de messages que l’acteur peut comprendre et processus. L’interface acteur est utilisé par d’autres intervenants et les applications clientes pour « envoyer » (asynchrone) des messages à l’acteur. Intervenants fiables peuvent implémenter plusieurs interfaces.
 
 * **Classe ActorProxy**. La classe ActorProxy est utilisée par les applications clientes pour appeler les méthodes exposées via l’interface acteur. La classe ActorProxy fournit deux fonctionnalités importantes :
    * Résolution de noms : il est en mesure de localiser l’acteur dans le cluster (rechercher le nœud du cluster dans lequel il est hébergé).
    * Gestion de défaillance : il peut réessayer appels de méthode et l’emplacement d’un acteur de résoudre après, par exemple, une défaillance nécessitant l’acteur être déplacé vers un autre nœud dans le cluster.

Les règles suivantes s’appliquent aux interfaces acteur valent mention :

- Méthodes de l’interface acteur ne peut pas être surchargés.
- Interface acteur méthodes ne doivent pas avoir arrière, ref ou paramètres facultatifs.
- Interfaces génériques ne sont pas pris en charge.

## <a name="create-an-actor-service"></a>Créer un service acteur
Commencez par créer une nouvelle application de Service tissu. Service tissu pour Linux comprend un Yeoman générateur pour fournir la structure d’une application de Service tissu avec un service sans état. Démarrez en exécutant la Yeoman suivant commande :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **Service acteur fiable**. Pour ce didacticiel, nommez l’application « HelloWorldActorApplication » et l’acteur « HelloWorldActor. » La structure suivante est créée :

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Blocs de construction de base fiables intervenants

Les concepts de base décrites précédemment traduisent en base blocs de construction d’un service acteur fiable.

### <a name="actor-interface"></a>Interface acteur

Elle contient la définition d’interface pour l’acteur. Cette interface définit le contrat acteur partagé par la mise en œuvre acteur et les clients appelant l’acteur pour qu’il est judicieux de définir dans un emplacement est différente de l’implémentation d’un acteur généralement et peut être partagé par plusieurs autres services ou des applications clientes.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Service acteur 
Contient votre implémentation acteur et le code d’inscription acteur. La classe acteur met en œuvre l’interface acteur. C’est l’endroit où votre acteur son travail.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Enregistrement d’un acteur

Le service acteur doit être inscrit à un type de service dans le runtime tissu de Service. Dans l’ordre service acteur exécuter votre instances acteur, votre type d’acteur doit également être inscrit auprès du Service acteur. La `ActorRuntime` méthode d’inscription effectue ce travail pour intervenants.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Client de test

Il s’agit d’une application cliente de test simple que vous pouvez exécuter séparément à partir de l’application de Service tissu pour tester votre service acteur. Voici un exemple de l’endroit où le ActorProxy peut être utilisée pour activer et communiquer avec les instances d’un acteur. Il ne pas obtenir déployé avec votre service.

### <a name="the-application"></a>L’application 

Enfin, l’application conditionne le service acteur et autres services, que vous pouvez ajouter à l’avenir ensemble pour le déploiement. Il contient les détenteurs *ApplicationManifest.xml* et emplacement pour le package de service acteur.

## <a name="run-the-application"></a>Exécuter l’application

La Yeoman la structure inclut un script gradle pour générer l’application et bash déployer des scripts et désactivez-déploiement de l’application. Pour exécuter l’application, tout d’abord créer l’application avec gradle :

```bash
$ gradle
```

Vous obtiendrez un package d’application tissu de Service qui peut être déployé à l’aide du Service tissu Azure infrastructure du langage commun. Le script install.sh contient les commandes Azure infrastructure du langage commun nécessaires pour déployer le package d’application. Suffit d’exécuter le script install.sh déploiement :

```bask
$ ./install.sh
```
