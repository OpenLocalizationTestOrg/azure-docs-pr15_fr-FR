<properties
   pageTitle="Mise à niveau de l’application : sérialisation de données | Microsoft Azure"
   description="Meilleures pratiques pour la sérialisation de données et comment il affecte propagées application."
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


# <a name="how-data-serialization-affects-an-application-upgrade"></a>Comment sérialisation de données affecte une mise à niveau de l’application

Dans une [mise à niveau application propagée](service-fabric-application-upgrade.md), la mise à niveau est appliqué à un sous-ensemble de nœuds, un domaine de mise à niveau à la fois. Pendant ce processus, certains domaines mise à niveau apparaîtra sur la version la plus récente de votre application et certains domaines mise à niveau apparaîtra sur l’ancienne version de votre application. Au cours du déploiement, la nouvelle version de votre application doit être en mesure de lire l’ancienne version de vos données, et l’ancienne version de votre application doit être en mesure de lire la nouvelle version de vos données. Si le format de données n’est pas compatible avec avant et arrière, la mise à niveau peut échouer ou pire, les données peuvent être perdues ou endommagées. Cet article décrit ce que constitue votre format des données et propose les meilleures pratiques pour s’assurer que vos données sont avant et arrière compatibles.


## <a name="what-makes-up-your-data-format"></a>En quoi consiste le format des données ?

Dans Azure Service tissu, les données sont conservées et répliquées provient de votre cours c#. Pour les applications qui utilisent les [Collections fiable](service-fabric-reliable-services-reliable-collections.md), c'est-à-dire les objets dans les dictionnaires fiables et les files d’attente. Pour les applications qui utilisent des [Intervenants fiable](service-fabric-reliable-actors-introduction.md), qui est l’état de sauvegarde de l’acteur. Ces classes c# doivent être sérialisables à rendre persistante et répliquées. Par conséquent, le format de données défini par les champs et les propriétés qui sont sérialisées, ainsi que la manière dont elles sont sérialisées. Par exemple, dans un `IReliableDictionary<int, MyClass>` les données sont une série `int` et une série `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Modifications du code résultat dans une modification de format de données

Étant donné que le format de données est déterminé par les classes c#, les modifications apportées aux classes risquent une modification de format de données. Soins doivent être prises pour vous assurer que la modification du format de données peut gérer la mise à niveau. Exemples de qui peuvent entraîner des modifications de format de données :

- Ajout ou suppression des champs ou des propriétés
- Renommer des champs ou des propriétés
- Modifier les types de champs ou des propriétés
- Modification du nom du cours ou un espace de noms

### <a name="data-contract-as-the-default-serializer"></a>Contrat de données en tant que le sérialiseur par défaut

Le sérialiseur est généralement responsable de la lecture des données et la désérialisation dans la version actuelle, même si les données sont dans une version antérieure ou *version ultérieure* . Le sérialiseur par défaut est le [sérialiseur de contrat de données](https://msdn.microsoft.com/library/ms733127.aspx)qui contient des règles de contrôle de version bien défini. Collections fiables autorisent le sérialiseur à être remplacés, mais intervenants fiable actuellement pas. Le sérialiseur de données s’avère particulièrement important par l’activation des mises à niveau. Le sérialiseur de contrat de données est le sérialiseur que nous recommandons pour les applications de Service tissu.


## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Comment le format de données affecte la mise à niveau

Pendant la mise à niveau, il existe deux scénarios principaux lesquels le sérialiseur peut se produire une version antérieure ou *version ultérieure* de vos données :

1. Après un nœud est mis à niveau et commence à sauvegarder, le nouveau sérialiseur charge les données qui a été conservées sur le disque par l’ancienne version.
2. Pendant la mise à niveau propagée, le cluster contiendra un mélange des versions anciens et nouveaux de votre code. Dans la mesure où réplicas peuvent être placés dans différents domaines de mise à niveau et réplicas envoient des données à l’autre, le nouveau et/ou ancienne la version de vos données peut-être être rencontrée par le nouveau et/ou ancienne la version de votre sérialiseur.

> [AZURE.NOTE] La « nouvelle version » et « ancienne version » ici font référence à la version de votre code est en cours d’exécution. Le « nouveau sérialiseur » fait référence au code sérialiseur qui s’exécute dans la nouvelle version de votre application. « Nouvelles données » fait référence à la classe c# série à partir de la nouvelle version de votre application.

Les deux versions du format de code et les données doivent être avant et arrière compatibles. Si elles ne sont pas compatibles, la mise à niveau propagée peut échouer ou données peuvent être perdues. La mise à niveau propagée peut échouer, car le code ou sérialiseur peut-être lever des exceptions ou une erreur quand elle rencontre l’autre version. Données peuvent être perdues si, par exemple, une nouvelle propriété a été ajoutée mais le sérialiseur ancien ignore pendant la désérialisation.

Contrat de données est la solution recommandée pour s’assurer que vos données sont compatibles. Il comporte des règles de contrôle de version bien définies pour l’ajout, suppression et modification des champs. Il est également prise en charge de traitement des champs inconnus, se connecter au processus de sérialisation et désérialisation et les deuxièmes l’héritage de classe. Pour plus d’informations, voir le [Contrat de données à l’aide](https://msdn.microsoft.com/library/ms733127.aspx).


## <a name="next-steps"></a>Étapes suivantes

[Uprading votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide dans une mise à niveau de l’application à l’aide de Visual Studio.

[Uprading votre Application utilisation de Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide dans une mise à niveau de l’application à l’aide de PowerShell.

Contrôler la façon dont votre application met à niveau en utilisant des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Découvrez comment utiliser les fonctionnalités avancées nécessitent pendant la mise à niveau votre application en faisant référence à [Des rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en faisant référence aux étapes de [Dépannage les mises à niveau de l’Application ](service-fabric-application-upgrade-troubleshooting.md).
