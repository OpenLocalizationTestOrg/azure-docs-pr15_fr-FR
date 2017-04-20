<properties 
    pageTitle="Comment utiliser les thèmes du Service Bus avec Python | Microsoft Azure" 
    description="Découvrez comment utiliser les abonnements à partir de Python et rubriques Bus des services Azure." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment utiliser des abonnements et les rubriques de Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser des abonnements et les rubriques de Bus des services. Les exemples sont écrits dans Python et utilisent le [package Python Azure][]. Les scénarios présentés incluent **Création rubriques et abonnements**, **Création de filtres de l’abonnement**, **envoyant des messages à un sujet**, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et abonnements**. Pour plus d’informations sur les abonnements et les rubriques, consultez la section [Étapes suivantes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Remarque :** Si vous avez besoin d’installer Python ou le [package Python Azure][], consultez le [Guide d’Installation Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Créer un sujet

L’objet **ServiceBusService** vous permet d’utiliser des thèmes. Ajoutez le code suivant dans la partie supérieure de n’importe quel fichier Python dans laquelle vous souhaitez accéder par programme Bus des services :

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Le code suivant crée un objet **ServiceBusService** . Remplacer `mynamespace`, `sharedaccesskeyname`, et `sharedaccesskey` avec votre espace de noms réel, Signature accès partagé (sa) clé de la valeur nom et clée.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Vous pouvez obtenir les valeurs pour nom de la clé associations de sécurité et la valeur à partir du [portail Azure][].

```
bus_service.create_topic('mytopic')
```

**créer\_rubrique** prend également en charge les options supplémentaires vous permettent de remplacer les paramètres de rubrique par défaut tels que la durée de vie du message ou la taille maximale rubrique. L’exemple suivant définit la taille maximale rubrique à 5 Go, puis une heure live valeur (TTL) d’une minute :

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnements à des rubriques sont également créés avec l’objet **ServiceBusService** . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

> [AZURE.NOTE] Abonnements sont permanentes et continuent à existe tant qu’ils, soit la rubrique à laquelle elles si vous avez souscrit, sont supprimés.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier les messages envoyés à un sujet doivent figurer au sein d’un abonnement sujet spécifique.

Le type de filtre pris en charge par les abonnements plus souple est un **SqlFilter**, qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisés avec un filtre SQL, consultez la syntaxe [SqlFilter.SqlExpression][] .

Vous pouvez ajouter des filtres à un abonnement à l’aide de la **créer\_règle** méthode de l’objet **ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE] Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou la **MatchAll** remplace tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut à l’aide de la **Supprimer\_règle** méthode de l’objet **ServiceBusService** .

L’exemple suivant crée un abonnement nommé `HighMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété personnalisée **format** égal à 3 :

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

De même, l’exemple suivant crée un abonnement nommé `LowMessages` avec un **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **format** inférieur ou égal à 3 :

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

À présent, lorsqu’un message est envoyé à `mytopic` il est toujours livré aux récepteurs d’abonné à l’abonnement de rubrique **AllMessages** et sélectivement remis aux récepteurs d’abonné à l’abonnement à une rubrique **HighMessages** et **LowMessages** (selon le contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application doit utiliser la **envoyer\_rubrique\_message** méthode de l’objet **ServiceBusService** .

L’exemple suivant montre comment envoyer cinq tester les messages à `mytopic`. Notez que la valeur de propriété **format** de chaque message varie selon l’itération de la boucle (détermine les abonnements reçoivent) :

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. La taille de cette rubrique est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, voir [les quotas de Bus des services][].

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages provenant d’un abonnement à l’aide du **recevoir\_abonnement\_message** méthode sur l’objet **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de l’abonnement dès qu’elles sont lues lorsque le paramètre **Aperçu\_verrouillage** est définie sur **False**. Vous pouvez lire (aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **Aperçu\_verrouillage** **true**.

Le comportement de lire et supprimer le message dans le cadre de l’opération de réception est le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

Si la **Aperçu\_verrouillage** paramètre est défini sur **True**, la réception devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant méthode **delete** sur l’objet du **Message** . La méthode **delete** marque le message comme étant consommées et supprime de l’abonnement.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **déverrouiller** sur l’objet du **Message** . Cela entraînera Bus des services déverrouiller le message au sein de l’abonnement et le rendre disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Service Bus déverrouiller automatiquement le message et rend disponible pour être reçus à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant l’appel de la méthode **delete** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue à l’aide de la propriété **MessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

Rubriques d’abonnements sont permanentes et doivent être explicitement supprimés à partir du [portail Azure][] ou par programme. L’exemple suivant montre comment supprimer la rubrique nommée `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Suppression d’un sujet entraîne celle de tous les abonnements qui sont enregistrés avec la rubrique. Abonnements peuvent également être supprimés séparément. Le code suivant montre comment supprimer un abonnement nommé `HighMessages` à partir de la `mytopic` rubrique :

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des rubriques Bus des services, suivez ces liens pour en savoir plus.

-   Voir [files d’attente, de sujets et abonnements][].
-   Référence pour [SqlFilter.SqlExpression][].

[Portail Azure]: https://portal.azure.com
[Package Python Azure]: https://pypi.python.org/pypi/azure  
[Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Quotas de Bus des services]: service-bus-quotas.md 
