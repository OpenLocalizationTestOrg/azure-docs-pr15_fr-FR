<properties
   pageTitle="Événements intervenants fiables | Microsoft Azure"
   description="Introduction aux événements de Service TISSU fiable intervenants."
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Événements acteur
Événements acteur offrent un moyen d’envoyer des notifications de mieux à partir de l’acteur aux clients. Événements acteur sont conçus pour une communication acteur-à-client et ne doivent pas être utilisés pour la communication acteur-acteur.

Extraits de code suivants montrent comment utiliser les événements acteur dans votre application.

Définissez une interface qui décrit les événements publiés par l’acteur. Cette interface doit être dérivée de la `IActorEvents` interface. Les arguments des méthodes doivent être [sérialisable de contrat de données](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Les méthodes doivent retourner void, en tant qu’événement notifications sont un moyen et mieux.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Déclarer les événements publiés par l’acteur dans l’interface acteur.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Sur le côté client, implémenter le Gestionnaire d’événements.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sur le client, créez un proxy vers l’acteur qui publie l’événement et s’abonner à ses événements.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

En cas de basculement, l’acteur peut basculer vers un autre processus ou un nœud. Le proxy acteur gère les abonnements actifs et automatiquement les abonnements nouveau. Vous pouvez contrôler l’intervalle de ré-abonnement via la `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Pour annuler l’abonnement, utilisez le `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Sur l’acteur, publiez simplement les événements en temps réel. S’il existe des abonnés à l’événement, le runtime intervenants les enverront la notification.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Étapes suivantes
 - [Réentrance acteur](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostics acteur et analyse des performances](service-fabric-reliable-actors-diagnostics.md)
 - [Documentation de référence acteur API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemples de code](https://github.com/Azure/servicefabric-samples)
