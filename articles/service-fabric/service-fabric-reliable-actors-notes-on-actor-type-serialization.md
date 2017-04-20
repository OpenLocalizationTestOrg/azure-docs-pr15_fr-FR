<properties
   pageTitle="Notes intervenants fiables sur acteur tapez sérialisation | Microsoft Azure"
   description="Décrit la configuration minimale requise pour la définition de classes sérialisables qui peuvent être utilisés pour définir les interfaces et les États Service TISSU fiable intervenants"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Notes sur Service TISSU fiable intervenants tapez sérialisation


Les arguments de toutes les méthodes, types de résultats de tâches renvoyées par chaque méthode dans une interface acteur et des objets stockés dans le Gestionnaire d’état d’un acteur doivent être [Contrat de données sérialisable](https://msdn.microsoft.com/library/ms731923.aspx)... Cela s’applique également aux arguments des méthodes définies dans [les interfaces événement acteur](service-fabric-reliable-actors-events.md#actor-events). (Acteur événement interface méthodes retournent toujours void.)

## <a name="custom-data-types"></a>Types de données personnalisés

Dans cet exemple, l’interface acteur suivante définit une méthode qui retourne un type de données personnalisé appelé `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

L’interface est impelemented par un acteur, qui utilise le Gestionnaire d’état pour stocker une `VoicemailBox` objet :

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Dans cet exemple, le `VoicemailBox` objet est sérialisé lorsque :
 - L’objet est transmis entre une instance acteur et un appelant.
 - L’objet est enregistré dans le Gestionnaire d’état dans lequel il est conservé sur le disque et répliquée vers d’autres nœuds.
 
L’infrastructure fiable acteur utilise DataContract sérialisation. Par conséquent, les objets de données personnalisés et leurs membres doivent être annotées avec les attributs **DataContract** et **DataMember** , respectivement

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes
 - [Acteur du cycle de vie et le garbage collection](service-fabric-reliable-actors-lifecycle.md)
 - [Rappels et les composants Timer acteur](service-fabric-reliable-actors-timers-reminders.md)
 - [Événements acteur](service-fabric-reliable-actors-events.md)
 - [Réentrance acteur](service-fabric-reliable-actors-reentrancy.md)
 - [Polymorphisme acteur et les modèles de conception orientée objet](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnostics acteur et analyse des performances](service-fabric-reliable-actors-diagnostics.md)
