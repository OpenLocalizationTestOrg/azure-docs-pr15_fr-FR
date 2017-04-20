<properties
   pageTitle="Notifications de Services fiables | Microsoft Azure"
   description="Documentation conceptuelle pour les notifications de Service TISSU fiable Services"
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
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>Notifications de Services fiables

Les notifications permettent aux clients de suivre les modifications apportées à un objet qui les intéresse. Deux types d’objets prennent en charge les notifications : *Gestionnaire d’état fiable* et *Dictionnaire fiable*.

Raisons courantes pour l’utilisation de notifications sont :

- Construction réalisée affichages tels que des index secondaires ou agrégé affichages filtrés d’état du réplica. Un exemple est un index trié de toutes les clés de dictionnaire fiable.
- Données d’analyse envoi, telles que le nombre d’utilisateurs ajoutés dans la dernière heure.

Notifications sont déclenchées dans le cadre de l’application d’opérations. Pour cette raison, les notifications doivent être gérées aussi rapidement que possibles et synchrones événements ne doit pas inclure les opérations coûteuses.

## <a name="reliable-state-manager-notifications"></a>Notifications d’état fiable Manager

Gestionnaire d’état fiable envoie des notifications pour les événements suivants :

- Transaction
    - Valider
- Gestionnaire d’état
    - Reconstruire
    - Ajout d’un état fiable
    - Suppression d’un état fiable

Gestionnaire d’état fiable effectue le suivi des transactions METEOROLOGIQUE actuelles. La seule modification dans l’état de transaction entraînant une notification déclenchement est une transaction en cours de validation.

Gestionnaire d’état fiable gère une collection d’états fiables comme dictionnaire fiable et file d’attente fiable. Gestionnaire d’état fiable déclenche notifications lors de la modification de cette collection : un état fiable est ajouté ou supprimé, ou l’intégralité de la collection est reconstruit.
La collection fiable Gestionnaire d’état est reconstruite dans trois cas :

- Récupération : Lorsqu’un réplica démarre, il restaure son état précédent à partir du disque. À la fin de la récupération, elle utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États fiables récupérés.
- Copie complète : avant un réplica peut participer à l’ensemble de configuration, elle doit être créée. Parfois, cette fonctionnalité nécessite une copie complète de l’état du Gestionnaire d’état fiable à partir du réplica principal pouvant être appliqués à la copie secondaire inactif. Gestionnaire d’état de fiable du réplica secondaire utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États fiables qui proviennent de réplica principal.
- Restaurer : Dans récupération d’urgence scénarios, état du réplica peut être restauré à partir d’une sauvegarde via **RestoreAsync**. Dans ce cas, gestionnaire d’état fiable sur réplica principal utilise **NotifyStateManagerChangedEventArgs** pour déclencher un événement qui contient l’ensemble des États fiables qu’il restauré à partir de la sauvegarde.

Pour vous inscrire pour les notifications de transaction et/ou notifications Gestionnaire d’état, vous devez enregistrer avec les événements **TransactionChanged** ou **StateManagerChanged** sur fiable Gestionnaire d’état. Un emplacement commun inscrire auprès de ces gestionnaires d’événements est le constructeur de votre service avec état. Lorsque vous enregistrez dans le constructeur, vous ne ratez aucune notification provoquée par une modification pendant la durée de vie de **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Le Gestionnaire d’événements **TransactionChanged** utilise **NotifyTransactionChangedEventArgs** pour fournir des détails sur l’événement. Il contient la propriété action (par exemple, **NotifyTransactionChangedAction.Commit**) qui spécifie le type de modification. Elle contient également la propriété transaction qui fournit une référence à la transaction modifié.

>[AZURE.NOTE] Aujourd'hui, **TransactionChanged** sont déclenchés uniquement si la transaction est validée. L’action puis est égale à **NotifyTransactionChangedAction.Commit**. Mais à l’avenir, les événements peuvent être déclenchés pour d’autres types de modifications de l’état transaction. Nous vous recommandons de la vérification de l’action et de traitement de l’événement uniquement si elle fait partie d’une que vous attendez.

Voici un exemple de gestionnaire d’événements **TransactionChanged** .

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Le Gestionnaire d’événements **StateManagerChanged** utilise **NotifyStateManagerChangedEventArgs** pour fournir des détails sur l’événement.
**NotifyStateManagerChangedEventArgs** contient deux sous-classes : **NotifyStateManagerRebuildEventArgs** et **NotifyStateManagerSingleEntityChangedEventArgs**.
Vous utilisez la propriété action dans **NotifyStateManagerChangedEventArgs** cast **NotifyStateManagerChangedEventArgs** celle-ci correcte :

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add** et **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

Voici un exemple de gestionnaire de notification **StateManagerChanged** .

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Notifications de dictionnaire fiables

Dictionnaire fiable envoie des notifications pour les événements suivants :

- Reconstruction : Appelé lorsque **ReliableDictionary** a restauré son état à partir d’une sauvegarde ou récupéré ou copiée état local.
- Effacer : Appelée lorsque l’état de **ReliableDictionary** a été désactivée via la méthode **ClearAsync** .
- Ajouter : Appelé lorsqu’un élément a été ajouté à **ReliableDictionary**.
- Mise à jour : Appelée lorsqu’un élément dans **IReliableDictionary** a été mis à jour.
- Supprimer : Appelée lorsqu’un élément de **IReliableDictionary** a été supprimé.

Pour obtenir des notifications de dictionnaire fiable, vous devez enregistrer avec le Gestionnaire d’événements **DictionaryChanged** sur **IReliableDictionary**. Est un emplacement commun inscrire auprès de ces gestionnaires d’événements dans le **ReliableStateManager.StateManagerChanged** ajouter la notification.
Enregistrement lorsque **IReliableDictionary** est ajouté à **IReliableStateManager** garantit que vous ne manquez des notifications.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification** est la méthode d’exemple que l’exemple précédent **OnStateManagerChangedHandler** appelle.

Le code précédent définit l’interface **IReliableNotificationAsyncCallback** , ainsi que **DictionaryChanged**. Étant donné que **NotifyDictionaryRebuildEventArgs** contient une interface **IAsyncEnumerable** --eux ne devant être énuméré asynchrone--reconstruire notifications sont déclenchés par le biais **RebuildNotificationAsyncCallback** au lieu de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Dans le code précédent, dans le cadre de la notification de reconstruction de traitement tout d’abord l’état agrégé mise à jour est désactivée. Étant donné que la collection fiable est en cours de reconstruction avec un nouvel état, toutes les notifications sont sans importance.

Le Gestionnaire d’événements **DictionaryChanged** utilise **NotifyDictionaryChangedEventArgs** pour fournir des détails sur l’événement.
**NotifyDictionaryChangedEventArgs** contient cinq sous-classes. Utiliser la propriété action dans **NotifyDictionaryChangedEventArgs** cast **NotifyDictionaryChangedEventArgs** celle-ci correcte :

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add** et **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Recommandations

- *Effectuez les événements de notification aussi rapidement que possible.*
- *Ne pas* exécuter les opérations coûteuses (par exemple, les opérations e/s) dans le cadre d’événements synchrones.
- *Faire* vérifier le type d’action avant de traiter l’événement. Nouveaux types d’action peuvent être ajoutés à l’avenir.

Voici quelques éléments à garder à l’esprit :

- Notifications sont déclenchées dans le cadre de l’exécution d’une opération. Par exemple, une notification de restaurer se déclenche en tant que la dernière étape d’une opération de restauration. Une restauration se terminent pas jusqu'à ce que l’événement de notification est traité.
- Étant donné que les notifications sont déclenchées dans le cadre de l’application opérations, clients voir uniquement les notifications pour les opérations localement validées. Et parce que les opérations sont garanties uniquement localement validée (autrement dit, connecté), ils peuvent ou ne peuvent pas être annulées à l’avenir.
- Sur le chemin d’accès rétablir, une notification unique est lancée pour chaque opération appliquée. Cela signifie que si transaction T1 inclut Create(X), Delete(X) et Create(X), vous recevrez une notification pour la création de X, une pour la suppression et l’autre pour la création, dans l’ordre.
- Pour les transactions qui contiennent plusieurs opérations, les opérations sont appliquées dans l’ordre dans lequel elles ont été reçues sur réplica principal de l’utilisateur.
- Dans le cadre de traitement de l’avancement false, certaines opérations peuvent être annulées. Notifications sont déclenchées pour ces opérations Annuler, annulation de l’état du réplica à un point stable. Une différence importante des notifications Annuler est que les événements qui ont des clés en double sont regroupées. Par exemple, si transaction T1 est en cours d’annulation, vous verrez une seule notification à Delete(X).

## <a name="next-steps"></a>Étapes suivantes

- [Collections fiables](service-fabric-work-with-reliable-collections.md)
- [Démarrage rapide Services fiable](service-fabric-reliable-services-quick-start.md)
- [Services fiables sauvegarder et restaurer (reprise)](service-fabric-reliable-services-backup-restore.md)
- [Référence du développeur pour les Collections fiable](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
