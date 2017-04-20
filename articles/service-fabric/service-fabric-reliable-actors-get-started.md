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
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Prise en main intervenants fiable

> [AZURE.SELECTOR]
- [C# sur Windows](service-fabric-reliable-actors-get-started.md)
- [Java sous Linux](service-fabric-reliable-actors-get-started-java.md)

Cet article explique les notions de base des intervenants fiable Azure Service tissu et allant de création, le débogage et déploiement d’une application fiable acteur simple dans Visual Studio.

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, vérifiez que vous disposez de l’environnement de développement de Service TISSU configurer sur votre ordinateur.
Si vous avez besoin configurer votre messagerie, consultez les instructions détaillées sur la [façon de configurer l’environnement de développement](service-fabric-get-started.md).

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

## <a name="create-a-new-project-in-visual-studio"></a>Créer un projet dans Visual Studio
Une fois que vous avez installé les outils de Service tissu pour Visual Studio, vous pouvez créer de nouveaux types de projets. Les nouveaux types de projet sont situées sous la catégorie **Cloud** de la boîte de dialogue **Nouveau projet** .


![Outils de service tissu pour Visual Studio - nouveau projet][1]

Dans la boîte de dialogue suivante, vous pouvez choisir le type de projet que vous voulez créer.

![Modèles de projets tissu de service][5]

Pour le projet HelloWorld, nous allons utiliser le service Service TISSU fiable intervenants.

Après avoir créé la solution, vous devriez voir la structure suivante :

![Structure de projet tissu de service][2]

## <a name="reliable-actors-basic-building-blocks"></a>Blocs de construction de base fiables intervenants

Une solution fiable intervenants classique est composée de trois projets :

* **Le projet d’application (MyActorApplication)**. Il s’agit du projet qui stocke tous les services gagnante pour le déploiement. Il contient les scripts *ApplicationManifest.xml* et PowerShell pour gérer l’application.

* **Le projet de l’interface (MyActor.Interfaces)**. Il s’agit du projet qui contient la définition d’interface pour l’acteur. Dans le projet MyActor.Interfaces, vous pouvez définir les interfaces qui seront utilisés par les intervenants dans la solution. Votre interfaces acteur peuvent être définies dans n’importe quel projet avec n’importe quel nom, alors que l’interface définit le contrat acteur qui est partagé par l’implémentation d’un acteur et les clients appelant l’acteur, donc généralement rend judicieux de définir dans un assembly qui est différente de l’implémentation d’un acteur et peut être partagé par plusieurs autres projets.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Le projet de service acteur (MyActor)**. Il s’agit du projet utilisé pour définir le service de Service tissu destinée à héberger l’acteur. Il contient l’implémentation de l’acteur. Une mise en œuvre d’un acteur est une classe dérivée du type de base `Actor` et met en œuvre les interfaces qui sont définis dans le projet MyActor.Interfaces. Une classe d’acteur doit également implémenter un constructeur acceptant une `ActorService` instance et un `ActorId` et les transmet à la base de `Actor` cours. Cela permet d’injection de dépendance constructeur de dépendances de plateforme.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Le service acteur doit être inscrit à un type de service dans le runtime tissu de Service. Dans l’ordre service acteur exécuter votre instances acteur, votre type d’acteur doit également être inscrit auprès du Service acteur. La `ActorRuntime` méthode d’inscription effectue ce travail pour intervenants.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si vous démarrez à partir d’un projet dans Visual Studio et que vous n'avez qu’une seule définition acteur, l’enregistrement est inclus par défaut dans le code que Visual Studio génère. Si vous définissez les autres intervenants dans le service, vous devez ajouter l’enregistrement d’un acteur à l’aide de :

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Le runtime Service TISSU intervenants émet certains [compteurs de performance et des événements liés aux méthodes acteur](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ils sont utiles dans de diagnostics et de l’analyse des performances.


## <a name="debugging"></a>Le débogage

Les outils de Service tissu pour Visual Studio prend en charge le débogage sur votre ordinateur local. Vous pouvez démarrer une session de débogage en appuyant sur la touche F5. Visual Studio génère (si nécessaire) packages. Par ailleurs, déploie l’application sur le cluster Service TISSU local et attache le débogueur.

Pendant le processus de déploiement, vous pouvez voir la progression dans la fenêtre de **sortie** .

![Fenêtre de sortie de débogage tissu de service][3]


## <a name="next-steps"></a>Étapes suivantes
 - [Comment intervenants fiable utiliser la plateforme tissu de Service](service-fabric-reliable-actors-platform.md)
 - [Gestion de l’état acteur](service-fabric-reliable-actors-state-management.md)
 - [Acteur du cycle de vie et le garbage collection](service-fabric-reliable-actors-lifecycle.md)
 - [Documentation de référence acteur API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
