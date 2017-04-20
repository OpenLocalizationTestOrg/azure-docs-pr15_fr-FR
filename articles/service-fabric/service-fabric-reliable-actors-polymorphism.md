<properties
   pageTitle="Polymorphisme dans le cadre intervenants fiable | Microsoft Azure"
   description="Créer des hiérarchies des interfaces .NET et des types dans le cadre intervenants fiable réutiliser les fonctionnalités et les définitions de l’API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorphisme dans le cadre intervenants fiable

L’infrastructure intervenants fiable permet vous permet de générer des intervenants à l’aide des mêmes techniques que vous utiliseriez dans la conception orientée objet. Un de ces techniques est polymorphisme, qui permet aux types et interfaces pour héritent de plus generalized parents. Héritage des autorisations dans le cadre intervenants fiable suivant généralement le modèle .NET avec quelques contraintes supplémentaires.

## <a name="interfaces"></a>Interfaces

L’infrastructure d’intervenants fiable, vous devez définir au moins une interface à mettre en œuvre par votre type acteur. Cette interface est utilisée pour générer une classe proxy qui peut être utilisée par les clients pour communiquer avec votre intervenants. Interfaces peuvent hériter d’autres interfaces dans la mesure où toutes les interfaces qui sont implémentées par un type d’un acteur et tous ses parents finalement dérivent de IActor. IActor est l’interface de base définis par plateforme pour intervenants. Par conséquent, l’exemple classique polymorphisme à l’aide de formes peut ressembler à ceci :

![Hiérarchie de l’interface pour intervenants de forme][shapes-interface-hierarchy]


## <a name="types"></a>Types de

Vous pouvez également créer une hiérarchie de types acteur, qui sont dérivées de la classe acteur base qui est fournie par la plateforme. Dans le cas de formes, il peut qu’une base de `Shape` type :

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes de `Shape` pouvez ignorer les méthodes de base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Remarque la `ActorService` attribut sur le type d’un acteur. Cet attribut indique à l’infrastructure d’acteur fiable qu’il doit créer automatiquement un service d’hébergement intervenants de ce type. Dans certains cas, vous souhaiterez peut-être créer un type de base qui est destiné uniquement partageant la fonctionnalité avec sous-catégories et sera jamais utilisé d’instanciation intervenants concrets. Dans ce cas, vous devez utiliser le `abstract` mot clé pour indiquer que vous allez créer jamais un acteur basé sur ce type.


## <a name="next-steps"></a>Étapes suivantes

- Voir [comment l’infrastructure intervenants fiable s’appuie sur la plateforme tissu de Service](service-fabric-reliable-actors-platform.md) pour assurer la fiabilité, extensibilité élevées et cohérentes.
- Obtenir des informations sur le [cycle de vie acteur](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
