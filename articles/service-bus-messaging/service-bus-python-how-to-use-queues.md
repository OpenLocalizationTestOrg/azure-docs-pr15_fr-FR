<properties 
    pageTitle="Comment utiliser des files d’attente Bus des services avec Python | Microsoft Azure" 
    description="Découvrez comment utiliser les files d’attente de Bus des services Azure à partir de Python." 
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
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Comment utiliser les files d’attente Bus des services

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit comment utiliser les files d’attente Bus des services. Les exemples sont écrits dans Python et utilisent le [package Bus des services Azure Python][]. Les scénarios présentés incluent la **Création de files d’attente, l’envoi et réception de messages**et la **suppression des files d’attente**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Pour installer Python ou le [package Bus des services Azure Python][], voir le [Guide d’Installation Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Créer une file d’attente

L’objet **ServiceBusService** vous permet d’utiliser des files d’attente. Ajoutez le code suivant dans la partie supérieure de n’importe quel fichier Python dans laquelle vous souhaitez accéder par programme Bus des services :

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Le code suivant crée un objet **ServiceBusService** . Remplacer `mynamespace`, `sharedaccesskeyname`, et `sharedaccesskey` avec votre espace de noms, le nom de la clé accès partagé signature (sa) et la valeur.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Les valeurs pour le nom de la clé associations de sécurité et la valeur se trouvent dans les informations de connexion [Azure portal classique][] , ou dans le volet Visual Studio **Propriétés** lors de la sélection de l’espace de noms Bus des services dans l’Explorateur de serveur (comme illustré dans la section précédente).

```
bus_service.create_queue('taskqueue')
```

**create_queue** prend également en charge les options supplémentaires vous permettent de remplacer les paramètres de file d’attente par défaut tels que la durée de vie (TTL) du message ou la taille maximale de file d’attente. L’exemple suivant définit la taille maximale de file d’attente à 5 Go et la valeur TTL à une minute :

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente Bus des services, votre application appelle la **envoyer\_file d’attente\_message** méthode sur l’objet **ServiceBusService** .

L’exemple suivant montre comment envoyer un message de test dans la file nommée *à l’aide de taskqueue* **envoyer\_file d’attente\_message**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Files d’attente Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une file d’attente, mais il existe une lettrine sur la taille totale des messages émanant d’une file d’attente. Cette taille file d’attente est définie au moment de la création, avec un maximum de 5 Go. Pour plus d’informations sur les quotas, voir [les quotas de Bus des services][].

## <a name="receive-messages-from-a-queue"></a>Recevoir des messages à partir d’une file d’attente

Les messages provenant d’une file d’attente à l’aide du **recevoir\_file d’attente\_message** méthode sur l’objet **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés à partir de la file d’attente dès qu’elles sont lues lorsque le paramètre **Aperçu\_verrouillage** est définie sur **False**. Vous pouvez lire (aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **Aperçu\_verrouillage** **true**.

Le comportement de lire et supprimer le message dans le cadre de l’opération de réception est le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

Si la **Aperçu\_verrouillage** paramètre est défini sur **True**, la réception devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant la méthode **delete** sur l’objet du **Message** . La méthode **Supprimer** marquer le message comme étant consommées et supprimer de la file d’attente.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode **déverrouiller** sur l’objet du **Message** . Cela entraînera Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant l’appel de la méthode **delete** , puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois que traitement**, c'est-à-dire que chaque message sera traité au moins une fois mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue à l’aide de la propriété **MessageId** du message, ce qui reste constante au sein de tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, suivez ces liens pour en savoir plus.

-   Voir [files d’attente, de sujets et abonnements][].

[Portail classique Azure]: https://manage.windowsazure.com
[Package Bus des services Python Azure]: https://pypi.python.org/pypi/azure-servicebus  
[Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
[Quotas de Bus des services]: service-bus-quotas.md
 
