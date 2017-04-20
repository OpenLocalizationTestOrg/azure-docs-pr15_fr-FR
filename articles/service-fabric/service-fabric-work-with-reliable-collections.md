<properties
    pageTitle="Travailler avec des Collections fiables | Microsoft Azure"
    description="Découvrez les meilleures pratiques pour travailler avec des Collections fiable."
    services="service-fabric"
    documentationCenter=".net"
    authors="JeffreyRichter"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="03/28/2016"
    ms.author="jeffreyr" />

# <a name="working-with-reliable-collections"></a>Travailler avec des Collections fiables

Service TISSU offre un modèle de programmation dynamique disponible pour les développeurs .NET via Collections fiable. Plus précisément, Service TISSU fournit dictionnaire fiable et classes file d’attente fiable. Lorsque vous utilisez ces classes, votre état est partition (pour une extensibilité), répliquée (pour la disponibilité) et traitée au sein d’une partition (pour la sémantique ACID). Nous allons examiner l’utilisation type d’un objet de dictionnaire fiable et déterminer quelles son réellement.

~~~
retry:
try {
   // Create a new Transaction object for this partition
   using (ITransaction tx = base.StateManager.CreateTransaction()) {
      // AddAsync takes key's write lock; if >4 secs, TimeoutException
      // Key & value put in temp dictionary (read your own writes),
      // serialized, redo/undo record is logged & sent to  
      // secondary replicas
      await m_dic.AddAsync(tx, key, value, cancellationToken);

      // CommitAsync sends Commit record to log & secondary replicas
      // After quorum responds, all locks released
      await tx.CommitAsync();
   }
   // If CommitAsync not called, Dispose sends Abort
   // record to log & all locks released
}
catch (TimeoutException) { 
   await Task.Delay(100, cancellationToken); goto retry; 
}
~~~

Toutes les opérations sur les objets de dictionnaire fiable (à l’exception des ClearAsync qui ne peut pas être annulée), nécessitent un objet ITransaction. Cet objet est associée à une et toutes les modifications que vous essayez de faire à n’importe quel dictionnaire fiable et/ou fiable file d’attente les objets dans une même partition. Vous achetez une ITransaction méthode de CreateTransaction de StateManager l’objet en appelant la partition.
 
Dans le code ci-dessus, l’objet ITransaction est passé à méthode de AddAsync d’un dictionnaire fiable. En interne, les méthodes de dictionnaire acceptant une clé de prennent un verrou lecture/écriture associé à la clé. Si la méthode modifie la valeur de clé, la méthode prend un verrou en écriture sur la touche et si la méthode lit uniquement à partir de la valeur de clé, un verrou en lecture est considérée sur la touche. Dans la mesure où AddAsync modifie la valeur de clé pour la nouvelle valeur transmise, verrou en écriture de la clé est considérée. Ainsi, si vous essaient d’ajouter des valeurs avec la même clé en même temps threads 2 (ou plus), un thread recevra le verrou d’écriture et les autres threads bloquera. Par défaut, les méthodes bloquent pour jusqu'à 4 secondes d’acquérir le verrou ; après 4 secondes, les méthodes lever une TimeoutException. Surcharges de méthodes existent qui vous permet de passer à une valeur du délai d’attente explicite si vous préférez.
 
En règle générale, vous rédigez votre code pour répondre à une TimeoutException par son interception et réessayez l’opération entière (comme illustré dans le code ci-dessus). Dans mon code simple, j’appelle simplement Task.Delay transmission 100 millisecondes chaque fois. Mais, en réalité, vous serez peut être préférable d’utiliser un type de retard recul exponentielle à la place.
 
Une fois le verrou est acquis, AddAsync ajoute les références d’objet clé et la valeur à un dictionnaire temporaire interne associé à l’objet ITransaction. Cela pour vous fournir sémantique en lecture-your-appartient-écrit. Autrement dit, après avoir appelé AddAsync, un appel ultérieur à TryGetValueAsync (en utilisant le même objet ITransaction) renvoie la valeur même si vous n’avez pas encore validé la transaction. Ensuite, AddAsync sérialise votre clé et valeur des objets dans les tableaux d’octets et ajoute ces tableaux d’octets dans un fichier journal sur le nœud local. Enfin, AddAsync envoie les tableaux d’octets à tous les réplicas secondaires afin qu’ils puissent les mêmes informations de clé/valeur. Même si les informations clé/valeur a été écrites dans un fichier journal, les informations ne faites pas partie du dictionnaire jusqu'à ce que l’opération qui leur sont associées aux a été validée. 

Dans le code ci-dessus, l’appel vers CommitAsync valide toutes les opérations de la transaction. En particulier, il vous ajoute des informations de validation dans le fichier journal sur le nœud local et envoie également l’enregistrement de validation à tous les réplicas secondaires. Une fois un quorum (majorité) réplica a répondu, toutes les modifications de données sont considérées comme permanentes et les verrous associés aux clés qui ont été manipuler via l’objet ITransaction sont publiées afin que d’autres threads/transactions pouvez manipuler les mêmes clés et leurs valeurs.

Si CommitAsync n’est pas appelée (généralement en raison d’une exception levée), puis l’objet ITransaction obtient supprimé. Lors de la suppression d’un objet ITransaction non validée, TISSU Service ajoute abandon informations au fichier journal du nœud local et rien ne doit être envoyée à n’importe quel réplica secondaire. Et ensuite, tous les verrous associés aux clés qui ont été manipuler via la transaction sont publiées.

## <a name="common-pitfalls-and-how-to-avoid-them"></a>Problèmes courants et comment les éviter
Maintenant que vous savez comment les collections fiables fonctionnent en interne, examinons quelques injuste courantes d'entre eux. Visualiser le code ci-dessous :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // AddAsync serializes the name/user, logs the bytes, 
   // & sends the bytes to the secondary replicas.
   await m_dic.AddAsync(tx, name, user);

   // The line below updates the property’s value in memory only; the
   // new value is NOT serialized, logged, & sent to secondary replicas.
   user.LastLogin = DateTime.UtcNow;  // Corruption!

   await tx.CommitAsync();
}
~~~

Lorsque vous travaillez avec un dictionnaire .NET normal, vous pouvez ajouter une valeur de clé/au dictionnaire et puis remplacez la valeur d’une propriété (par exemple, LastLogin). Toutefois, ce code ne fonctionnera pas correctement avec un dictionnaire fiable. N’oubliez pas de la discussion antérieure, l’appel vers AddAsync sérialise les objets de clé/valeur tableaux d’octets et puis enregistre les tableaux vers un fichier local et les envoie également aux réplicas secondaires. Si vous modifiez ultérieurement une propriété, cette opération modifie la valeur de propriété en mémoire uniquement ; Il n’influe pas sur le fichier local ou les données envoyées aux réplicas. Si le processus se bloque, ce qui se trouve en mémoire est levée absent (e). Démarrage d’un nouveau processus ou si un autre réplica devient principal, la valeur de propriété ancien est ce qui est disponible. 

Je ne peux pas souligner suffisamment comme il est facile pour vérifier le type d’erreur ci-dessus. Et, vous allez uniquement découvrir l’erreur if/quand que le processus se déroule vers le bas. La façon correcte pour écrire du code est simplement pour inverser les deux lignes :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   user.LastLogin = DateTime.UtcNow;  // Do this BEFORE calling AddAsync
   await m_dic.AddAsync(tx, name, user);
   await tx.CommitAsync(); 
}
~~~

Voici un autre exemple montrant une erreur courante :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> user = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (user.HasValue) {
      // The line below updates the property’s value in memory only; the
      // new value is NOT serialized, logged, & sent to secondary replicas.
      user.Value.LastLogin = DateTime.UtcNow; // Corruption!
      await tx.CommitAsync(); 
   }
}
~~~

Là encore, avec les dictionnaires .NET normales, le code ci-dessus fonctionne correctement et est un modèle courant : le développeur utilise une clé pour rechercher une valeur. Si la valeur existe, le développeur modifie la valeur d’une propriété. Toutefois, avec des collections fiables, ce code présente le même problème comme indiqué précédemment : __vous ne devez pas modifier un objet une fois que vous avez le donné à une collection fiable.__
 
Mettre à jour une valeur dans une collection fiable, la méthode correcte consiste à obtenir une référence à la valeur existante et prendre en compte l’objet désigné par cette référence immuable. Ensuite, créez un nouvel objet qui est une copie exacte de l’objet d’origine. À présent, vous pouvez modifier l’état de ce nouvel objet et écrire le nouvel objet dans la collection afin qu’il sérialisé dans les tableaux d’octets, ajoutées au fichier local et envoyés aux réplicas. Après avoir effectué l’ou les modifications, les objets en mémoire, le fichier local et tous les réplicas ont le même état exact. Tout est bon !

Le code ci-dessous montre la façon correcte pour mettre à jour une valeur dans une collection de sites fiable :

~~~
using (ITransaction tx = StateManager.CreateTransaction()) {
   // Use the user’s name to look up their data
   ConditionalValue<User> currentUser = 
      await m_dic.TryGetValueAsync(tx, name);

   // The user exists in the dictionary, update one of their properties.
   if (currentUser.HasValue) {
      // Create new user object with the same state as the current user object.
      // NOTE: This must be a deep copy; not a shallow copy. Specifically, only
      // immutable state can be shared by currentUser & updatedUser object graphs.
      User updatedUser = new User(currentUser);

      // In the new object, modify any properties you desire 
      updatedUser.LastLogin = DateTime.UtcNow;

      // Update the key’s value to the updateUser info
      await m_dic.SetValue(tx, name, updatedUser);

      await tx.CommitAsync(); 
   }
}
~~~

## <a name="define-immutable-data-types-to-prevent-programmer-error"></a>Définir les types de données immuables pour éviter d’erreur programmeur

Dans l’idéal, nous aimerions le compilateur pour signaler des erreurs lorsque vous produisez accidentellement un code transforme l’état d’un objet que vous êtes supposé à prendre en considération immuable. Toutefois, le compilateur c# n’a pas la possibilité pour effectuer cette action. Par conséquent, pour éviter les bogues programmeur potentiels, nous vous recommandons vivement que vous définissez les types que vous utilisez avec collections fiables pour être types immuables. Plus précisément, cela signifie que vous respectez les types de valeur core (par exemple, des nombres [Int32, UInt64, etc.], DateTime, Guid, TimeSpan, etc.). Et, bien entendu, vous pouvez également utiliser chaîne. Il est préférable d’éviter les propriétés de collection de sites en tant que sérialisation et désérialisant peut fréquemment peuvent réduire les performances. Toutefois, si vous voulez utiliser les propriétés de collection de sites, nous vous recommandons l’utilisation de. Bibliothèque de collections inaltérables de nette (System.Collections.Immutable). Cette bibliothèque est disponible au téléchargement à partir de http://nuget.org. Nous vous recommandons également fermeture votre cours et le rendre les champs en lecture seule dès que possible.

Le type de UserInfo ci-dessous montre comment définir un type immuable tirer parti des recommandations ci-dessus.

~~~
[DataContract]
// If you don’t seal, you must ensure that any derived classes are also immutable
public sealed class UserInfo {
   private static readonly IEnumerable<ItemId> NoBids = ImmutableList<ItemId>.Empty;
 
   public UserInfo(String email, IEnumerable<ItemId> itemsBidding = null) {
      Email = email;
      ItemsBidding = (itemsBidding == null) ? NoBids : itemsBidding.ToImmutableList();
   }

   [OnDeserialized]
   private void OnDeserialized(StreamingContext context) {
      // Convert the deserialized collection to an immutable collection
      ItemsBidding = ItemsBidding.ToImmutableList();
   }
 
   [DataMember]
   public readonly String Email;
 
   // Ideally, this would be a readonly field but it can't be because OnDeserialized 
   // has to set it. So instead, the getter is public and the setter is private.
   [DataMember]
   public IEnumerable<ItemId> ItemsBidding { get; private set; }

   // Since each UserInfo object is immutable, we add a new ItemId to the ItemsBidding
   // collection by creating a new immutable UserInfo object with the added ItemId.
   public UserInfo AddItemBidding(ItemId itemId) {
      return new UserInfo(Email, ((ImmutableList<ItemId>)ItemsBidding).Add(itemId));
   }
}
~~~

Le type de ItemId est également un type immuable comme illustré ici :

~~~
[DataContract]
public struct ItemId {

   [DataMember] public readonly String Seller;
   [DataMember] public readonly String ItemName;
   public ItemId(String seller, String itemName) {
      Seller = seller;
      ItemName = itemName;
   }
}
~~~

## <a name="schema-versioning-upgrades"></a>Contrôle de version de schéma (mises à jour)

En interne, Collections fiable sérialiser vos objets à l’aide. DataContractSerializer de filet. Les objets séries sont conservées sur le disque local du réplica principal et sont également transmises aux réplicas secondaires. Votre service arrivera à échéance, il est probable que vous souhaiterez modifier le type de données (schéma) votre service requiert. Vous devez aborder le contrôle de version de vos données avec une extrême prudence. Tout d’abord, vous devez toujours être capable de désérialiser anciennes données. Plus précisément, cela signifie que votre code désérialisation doit être infini descendante : 333 Version de votre code de service doit être en mesure de fonctionner sur les données placées dans une collection de sites fiable par la version 1 de votre code de service 5 ans.

En outre, code de service est mis à niveau un domaine de mise à niveau à la fois. Par conséquent, pendant une mise à niveau, vous avez deux versions différentes de votre code de service en cours d’exécution simultanément. Vous devez éviter la nouvelle version de votre code de service utiliser le nouveau schéma comme anciennes versions de votre code de service n’est peut-être pas en mesure de gérer le schéma de nouveau. Si possible, vous devez concevoir chaque version de votre service pour être compatibles transférer par 1 version. Plus précisément, cela signifie que V1 de votre code de service doit être en mesure d’ignorer simplement les éléments de schéma qu'il ne traite pas explicitement. Toutefois, il doit être en mesure d’enregistrer toutes les données qu’il ne connaît explicitement et simplement enregistrer à nouveau lors de la mise à jour d’une clé de dictionnaire ou une valeur. 

>[AZURE.WARNING] Pendant que vous pouvez modifier le schéma d’une clé, vous devez vous assurer que le code de hachage de votre touche et algorithmes est égal à sont stables. Si vous changez le fonctionnement d’une de ces algorithmes, vous ne pourrez pas rechercher la clé dans le dictionnaire fiable jamais à nouveau.
  
Par ailleurs, vous pouvez effectuer ce qui est généralement appelé une mise à niveau 2 phases. Avec une mise à niveau 2 phases, vous passez au service de la vue V1 V2 : V2 contient le code qui sait comment traiter le nouveau changement de schéma mais ce code ne s’exécute. Lorsque le code V2 lit les données de V1, il fonctionne dessus et écrit des données V1. Puis, une fois la mise à niveau terminée mise à niveau de tous les domaines, vous pouvez signaler aux instances V2 en cours d’exécution que la mise à niveau est terminée. (Une façon de signal il s’agit à déployer une mise à niveau de la configuration ; c’est ce qui en fait une mise à niveau phase 2). À présent, les instances V2 peuvent lire les données V1, convertir en données V2, exploiter et écrivez-le en tant que données V2. Lorsque les autres instances lire données V2, ils n’avez pas besoin pour le convertir, leur fonctionnement sur celui-ci, simplement et écrivent les données V2.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la création de contrats de données compatible transférer, voir [Transférer Compatible des contrats de données](https://msdn.microsoft.com/library/ms731083.aspx).

Pour découvrir les meilleures pratiques sur le contrôle de version des contrats de données, voir [Versions des contrats de données](https://msdn.microsoft.com/library/ms731138.aspx). 

Pour découvrir comment implémenter le contrats de données à tolérance de version, voir [Rappels sérialisation avec tolérance de Version](https://msdn.microsoft.com/library/ms733734.aspx). 

Pour apprendre à fournir une structure de données qui peut interagir entre plusieurs versions, voir [IExtensibleDataObject](https://msdn.microsoft.com/library/system.runtime.serialization.iextensibledataobject.aspx).
