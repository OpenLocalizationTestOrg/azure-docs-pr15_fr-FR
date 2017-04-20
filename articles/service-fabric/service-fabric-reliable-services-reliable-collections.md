<properties
   pageTitle="Collections fiables | Microsoft Azure"
   description="Service tissu avec état services fournit des collections fiables qui vous permettent d’écrire des applications cloud hautement disponible, scalable et faible latence."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduction aux Collections fiable dans les services avec état tissu de Service Azure

Collections fiables permettent d’écrire des applications de cloud hautement disponible, scalable et faible latence comme si vous écrivez applications seul ordinateur. Les classes dans l’espace de noms **Microsoft.ServiceFabric.Data.Collections** offrent un ensemble de collections de prédéfinies qui rendent automatiquement votre état hautement disponibles. Les développeurs ont besoin programmer uniquement à l’API de collection de sites fiable et permettre aux Collections fiable gérer l’état répliquée et locale.

La différence clée entre Collections fiable et d’autres technologies de disponibilité (par exemple, Redis, le service de Table Azure et service Queue Azure) est que l’état est conservé localement dans l’instance de service lors de l’également sachant hautement disponible. Cela signifie que :

- Toutes les lectures sont locales, qui aboutit à faible latence et haut débit lit.
- Toutes les écritures entraînent le nombre minimal de réseau IOs, ce qui crée une latence faible et haut débit écrit.

![Image de l’évolution des collections de sites.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Collections fiables peuvent être considérées comme l’évolution naturelle de classes **System.Collections** : un nouvel ensemble de collections de sites qui ont été conçues pour les applications cloud et sur plusieurs ordinateurs sans accroître la complexité pour les développeurs. Dès lors, Collections fiable sont :

- Répliquée : Modifications de l’état sont répliquées de disponibilité.
- Conservées : Les données sont conservées sur le disque pour durer contre les interruptions à grande échelle (par exemple, une centre de données coupure de courant).
- Asynchrone : API sont asynchrone pour vous assurer que les threads ne sont pas bloqués lorsque subir IO.
- Transactions : API utilise l’abstraction des transactions qui vous pouvez de gérer facilement plusieurs Collections fiable au sein d’un service.

Collections fiables offrent la cohérence forte garantit prêts à l’emploi afin de faciliter la logique à propos de l’état de l’application.
Cohérence forte est atteint en veillant à ce transaction validations se terminer uniquement une fois la totalité de la transaction a été enregistrée sur un quorum majorité des réplicas, y compris le serveur principal.
Pour garantir la cohérence plus faible, applications pouvant acceptez revenir au client/demandeur avant la validation asynchrone renvoie.

Les API Collections fiable sont une évolution des collections simultanées API (figurant dans l’espace de noms **System.Collections.Concurrent** ) :

- Asynchrone : Renvoie une tâche dans la mesure où, à la différence des collections simultanées, les opérations sont répliquées et conservées.
- Paramètres de sortie ne : utilise `ConditionalValue<T>` pour renvoyer une valeur booléenne et une valeur à la place de paramètres de sortie. `ConditionalValue<T>`revient à `Nullable<T>` , mais ne nécessite ne pas T pour être une structure.
- Transactions : Utilise un objet de transaction pour permettre à l’utilisateur à des actions de groupe sur plusieurs Collections fiable dans une transaction.

Aujourd'hui, **Microsoft.ServiceFabric.Data.Collections** contient deux collections :

- [Dictionnaire fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx): représente une collection répliquée, transactions et asynchrone de paires clé/valeur. Similaire à **ConcurrentDictionary**, la clé et la valeur peuvent être de n’importe quel type.
- [File d’attente fiable](https://msdn.microsoft.com/library/azure/dn971527.aspx): représente un répliquée, transactions et asynchrone strict première connexion, sorti file d’attente. Similaire à **ConcurrentQueue**, la valeur peut être de n’importe quel type.

## <a name="isolation-levels"></a>Niveaux d’isolement
Niveau d’isolement spécifient l’auquel la transaction doit être isolée des modifications apportées par d’autres transactions.
Il existe deux niveaux d’isolement pris en charge dans les Collections fiable :

- **Lecture renouvelable**: Spécifie que les instructions ne peuvent pas lire les données qui ont été modifiées, mais pas encore enregistrées par d’autres transactions et qu’aucune autre transaction ne peut modifier les données qui a été lu par la transaction actuelle avant la fin de la transaction en cours. Pour plus d’informations, voir [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Instantané**: Spécifie que les données lues par toute instruction dans une transaction seront la version cohérente des données existantes au début de la transaction.
La transaction peut reconnaître uniquement les modifications apportées aux données qui ont été validées avant le début de la transaction.
Modifications apportées par d’autres transactions après le début de la transaction actuelle aux données ne sont pas visibles aux instructions en cours d’exécution dans la transaction actuelle.
L’effet est que si les instructions dans une transaction obtenir un instantané des données validées tel qu’il était au début de la transaction.
Instantanés sont cohérents dans Collections fiable.
Pour plus d’informations, voir [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Collections fiables choisissent automatiquement le niveau d’isolement à utiliser pour une opération de lecture donnée en fonction de l’opération et le rôle du réplica au moment de la création d’une transaction.
Voici le tableau qui décrit les valeurs par défaut au niveau d’isolement pour les opérations de dictionnaire fiable et file d’attente.

| Opération \ rôle      | Principal          | Secondaire        |
| --------------------- | :--------------- | :--------------- |
| En lecture seule entité    | Lecture renouvelable  | Instantané         |
| Énumération \ Nb   | Instantané         | Instantané         |

>[AZURE.NOTE] Sont des exemples courants pour les opérations entité unique `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

Le dictionnaire fiable et la file d’attente fiable prend en charge écrit votre lecture.
En d’autres termes, toute écriture au sein d’une transaction est visibles à une lecture suivante appartenant à la même transaction.

## <a name="locking"></a>Verrouillage
Dans Collections fiable, toutes les transactions sont en deux phases : une transaction ne libère pas les verrous qu’elle a acquise jusqu'à ce que la transaction se termine par un abandon ou une validation.

Dictionnaire fiable utilise verrouillage pour toutes les opérations seule entité au niveau de ligne.
File d’attente fiable reprises désactiver concurrence pour la propriété de FIFO stricte.
File d’attente fiable utilise verrous opération autoriser une transaction avec `TryPeekAsync` et/ou `TryDequeueAsync` et une transaction avec `EnqueueAsync` à la fois.
Notez que pour conserver FIFO, si un `TryPeekAsync` ou `TryDequeueAsync` respecte jamais que la file d’attente fiable est vide, ils verrouille également `EnqueueAsync`.

Écrire des opérations sont toujours verrouillages.
Pour les opérations de lecture, le verrouillage dépend de plusieurs facteurs.
Toute opération de lecture terminée à l’aide d’isolement de capture instantanée est verrou gratuit.
Une opération de lecture renouvelable par défaut prend verrous partagés.
Toutefois, pour une opération de lecture qui prend en charge la lecture renouvelable, l’utilisateur peut demander un verrou de mise à jour au lieu du verrou partagé.
Un verrou de mise à jour est un verrou asymétrique utilisé pour éviter les formes courantes de blocages qui se produisent lorsque plusieurs transactions verrouillez ressources les mises à jour potentiels ultérieurement.

Vous trouverez la matrice de compatibilité verrouiller ci-dessous :

| Demander \ accordées | Aucun         | Partagé       | Mise à jour      | Exclusif    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Partagé            | Pas de conflit  | Pas de conflit  | Conflit    | Conflit     |
| Mise à jour            | Pas de conflit  | Pas de conflit  | Conflit    | Conflit     |
| Exclusif         | Pas de conflit  | Conflit     | Conflit    | Conflit     |

Notez qu’un argument délai dans les API Collections fiable est utilisé pour la détection de blocage.
Par exemple, deux transactions (T1 et T2) tentez de lire et de mettre à jour K1.
Il est possible qu’elles se bloquer, car ils sont tous deux placés ayant le verrou partagé.
Dans ce cas, une ou plusieurs des opérations sera délai d’expiration.

Notez que le scénario de blocage ci-dessus est un excellent exemple de la façon dont un verrou de mise à jour peut bloquer des blocages.

## <a name="persistence-model"></a>Modèle de persistance
Le Gestionnaire d’état fiable et Collections fiable suivent un modèle de persistance qui est appelé journal et point de contrôle.
Il s’agit d’un modèle dans lequel chaque modification de l’état est connectée sur le disque et appliquée uniquement en mémoire.
L’état d’achèvement lui-même est conservé qu’occasionnellement (également appelé Point de contrôle).
L’avantage est que delta est transformées en écritures ajout uniquement séquentielles sur disque pour améliorer les performances.

Pour mieux comprendre le modèle de journal et point de contrôle, tout d’abord Examinons le scénario de disque infini.
Le Gestionnaire d’état fiable enregistre toutes les opérations avant qu’il est dupliqué.
Cela permet la Collection fiable appliquer uniquement l’opération en mémoire.
Dans la mesure où les journaux sont conservés, même si le réplica échoue et doit être redémarré, le Gestionnaire d’état fiable dispose d’informations suffisantes dans ses journaux à relire toutes les opérations de que la réplique est perdu.
Comme le disque est infini, les enregistrements de journal doivent jamais être supprimés et la Collection fiable doit gérer uniquement l’état en mémoire.

Maintenant examinons le scénario de disque déterminée.
Comme les enregistrements de journal s’accumuler, le Gestionnaire d’état fiable s’exécutera suffisamment d’espace disque.
Dans ce cas, le Gestionnaire d’état fiable doit tronquer son journal pour pouvoir placer les enregistrements plus récentes.
Il demande les Collections fiable au point de contrôle leur état en mémoire sur le disque.
Il est responsabilité Collections fiable pour conserver son état jusqu'à ce point.
Une fois que les Collections fiable terminé leurs points de contrôle, le Gestionnaire d’état fiable peut tronquer le journal afin de libérer de l’espace disque.
Ainsi, lorsque le réplica doit être redémarré, Collections fiable permet de récupérer leur état avec point de contrôle et le Gestionnaire d’état fiable sont récupérer et lire les modifications d’état qui se sont produites depuis le point de contrôle.

>[AZURE.NOTE] Ajouter une autre valeur de point de contrôle est qu’elle améliore les performances de récupération dans les cas courants.
C’est parce que seules les dernières versions contiennent des points de contrôle.

## <a name="recommendations"></a>Recommandations

- Ne modifiez pas un objet de type personnalisé retourné par les opérations de lecture (par exemple, `TryPeekAsync` ou `TryGetValueAsync`). Collections fiables, comme les Collections simultanées, renvoient une référence à ces objets et non une copie.
- Copie complète le faire l’objet d’un type personnalisé avant de le modifier. Dans la mesure où les structures et certains types intégrés sont passés par valeur, vous n’avez pas besoin effectuer une copie complète dessus.
- N’utilisez pas `TimeSpan.MaxValue` d’attente. Délais d’expiration doivent être utilisés pour détecter des blocages.
- N’utilisez pas une transaction une fois qu’il a été validée, abandonnée ou supprimé.
- N’utilisez pas d’énumération en dehors de la portée de transaction dans qu'il a été créé.
- Ne créez pas de transaction au sein d’une autre transaction `using` instruction car il peut provoquer des blocages.
- Assurez-vous que votre `IComparable<TKey>` implémentation est correcte. Le système prend dépendance sur ce problème pour la fusion des points de contrôle.
- Utilisez verrouillage de mise à jour lors de la lecture d’un élément avec une intention pour mettre à jour pour empêcher une certaine catégorie de blocages.
- Envisagez d’utiliser sauvegarder et restaurer la fonctionnalité pour que sinistre.
- Évitez de mélanger les opérations de seule entité et entités multiples (par ex. `GetCountAsync`, `CreateEnumerableAsync`) dans la même transaction en raison des niveaux d’isolement différent.

Voici quelques éléments à garder à l’esprit :

- Le délai d’expiration par défaut est de 4 secondes pour toutes les fiable API de collection de sites. La plupart des utilisateurs ne doivent pas substituer ceci.
- Le jeton d’annulation par défaut est `CancellationToken.None` dans toutes les API Collections fiable.
- Le paramètre de type de clé (*TKey*) pour un dictionnaire fiable doit implémenter correctement `GetHashCode()` et `Equals()`. Clés doivent être immuables.
- Pour obtenir une haute disponibilité pour les Collections fiable, chaque service doit avoir au moins une cible et le réplica minimale définir la taille de 3.
- Opérations de lecture sur le moniteur secondaire peuvent lire les versions qui ne sont pas quorum validée.
Cela signifie qu’une version des données sont en lecture à partir d’un secondaire unique peut-être être faux progression.
Bien entendu, lectures principale sont toujours stables : false peut être jamais progression.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide Services fiable](service-fabric-reliable-services-quick-start.md)
- [Travailler avec des Collections fiables](service-fabric-work-with-reliable-collections.md)
- [Notifications de Services fiables](service-fabric-reliable-services-notifications.md)
- [Services fiables sauvegarder et restaurer (reprise)](service-fabric-reliable-services-backup-restore.md)
- [Configuration du Gestionnaire d’état fiable](service-fabric-reliable-services-configuration.md)
- [Mise en route des services Web API tissu de Service](service-fabric-reliable-services-communication-webapi.md)
- [L’utilisation des Services fiable modèle de programmation avancée](service-fabric-reliable-services-advanced-usage.md)
- [Référence du développeur pour les Collections fiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
