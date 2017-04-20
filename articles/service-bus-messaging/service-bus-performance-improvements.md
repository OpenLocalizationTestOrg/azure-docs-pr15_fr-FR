<properties 
    pageTitle="Meilleures pratiques pour améliorer les performances à l’aide du Service Bus | Microsoft Azure"
    description="Décrit comment utiliser Bus des services Azure pour optimiser les performances lors de l’échange de messages traités."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="sethm" />

# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Meilleures pratiques pour améliorer les performances à l’aide du Service de messagerie Bus

Cette rubrique décrit comment utiliser la messagerie de Bus de Service Azure pour optimiser les performances lors de l’échange de messages traités. La première partie de cette rubrique décrit les différents mécanismes qui sont proposés pour vous aider à améliorer les performances. La deuxième partie fournit des conseils sur l’utilisation de Bus de Service d’une manière qui peut proposer les meilleures performances dans un scénario donné.

Dans l’ensemble de cette rubrique, le terme « client » fait référence à une entité qui accède à Bus des services. Un client peut prendre le rôle d’un expéditeur ou un destinataire. Le terme « expéditeur » est utilisé pour un client de file d’attente ou rubrique Bus des services qui envoie des messages à une file d’attente Bus de Service ou d’une rubrique. Le terme « récepteur » fait référence à un client de file d’attente ou d’abonnement Bus des services qui reçoit les messages d’une file d’attente Bus de Service ou d’abonnement.

Ces sections présentent plusieurs concepts Bus des services utilise pour vous aider optimisation des performances.

## <a name="protocols"></a>Protocoles

Service Bus permet aux clients d’envoyer et recevoir des messages via trois protocoles

1. Message avancés Queuing Protocol (AMQP)

2. Bus des services de messagerie Protocol (SBMP)

3. HTTP

AMQP et SBMP sont plus efficaces, car ils maintient la connexion au Service Bus tant que la messagerie usine existe. Il met également en œuvre le traitement par lots et la lecture préalable. Sauf mention explicite, tout le contenu de cette rubrique suppose l’utilisation de AMQP ou SBMP.

## <a name="reusing-factories-and-clients"></a>Réutilisation de références et des clients

Objets de client Bus des services, tels que [QueueClient][] ou [MessageSender][], sont créés via un objet [MessagingFactory][] , qui comprend également un gestion interne des connexions. Vous ne devez pas fermer références messageries ou file d’attente, le sujet et clients de l’abonnement après l’envoi d’un message et recréer ensuite lorsque vous envoyez le message suivant. Fermeture d’une usine de messagerie supprime la connexion au service Bus des services, et une nouvelle connexion est établie lorsque vous recréez usine. Établissement d’une connexion est une opération coûteuse que vous pouvez éviter à l’aide de nouveau la même usine et les objets client pour plusieurs opérations. Vous pouvez utiliser en toute sécurité de l’objet [QueueClient][] pour envoyer des messages à partir d’opérations asynchrones simultanées et plusieurs threads. 

## <a name="concurrent-operations"></a>Opérations simultanées

Effectuer une opération (envoyer, recevoir, supprimer, etc.) prend un certain temps. Ce temps inclut le traitement de l’opération par le service Bus des services en plus de la latence de la demande et la réponse. Pour augmenter le nombre d’opérations par heure, opérations doivent exécuter simultanément. Vous pouvez le faire de différentes manières :

-   **Opérations asynchrones**: le client planifie les opérations en effectuant les opérations asynchrones. La requête suivante est démarrée avant la fin de la demande précédente. Voici un exemple d’une opération d’envoi asynchrone :

    ```
    BrokeredMessage m1 = new BrokeredMessage(body);
    BrokeredMessage m2 = new BrokeredMessage(body);
    
    Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #1");
      });
    Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #2");
      });
    Task.WaitAll(send1, send2);
    Console.WriteLine("All messages sent");
    ```

    Il s’agit d’un exemple d’opération de réception asynchrone :
    
    ```
    Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    
    Task.WaitAll(receive1, receive2);
    Console.WriteLine("All messages received");
    
    async void ProcessReceivedMessage(Task<BrokeredMessage> t)
    {
      BrokeredMessage m = t.Result;
      Console.WriteLine("{0} received", m.Label);
      await m.CompleteAsync();
      Console.WriteLine("{0} complete", m.Label);
    }
    ```

-   **Plusieurs sites**: tous les clients (expéditeurs outre récepteurs) qui sont créés par la même usine partagent une connexion TCP. Le débit maximale des messages est limité par le nombre d’opérations que vous pouvez accéder à cette connexion TCP. Le débit qui peut être obtenu avec la même usine varie considérablement avec la durée des boucles TCP et la taille des messages. Pour obtenir des niveaux du débit plus élevés, vous devez utiliser plusieurs sites de messagerie.

## <a name="receive-mode"></a>Mode de réception

Lorsque vous créez un client file d’attente ou d’abonnement, vous pouvez spécifier un mode de réception : *Aperçu verrouiller* ou *recevoir et supprimer*. La valeur par défaut recevoir le mode est [PeekLock][]. Lorsqu’il fonctionne dans ce mode, le client envoie une demande de recevoir un message de Bus des services. Une fois que le client a reçu le message, il envoie une demande pour terminer le message.

Lorsque vous définissez le mode de réception à [ReceiveAndDelete][], ces deux étapes sont combinées dans une seule demande. Cela réduit le nombre total d’opérations et peut améliorer la vitesse de message globale. Ce gain de performances est fourni au risque de perdre des messages.

Service Bus ne reconnaît pas les transactions pour les opérations de réception et supprimer. En outre, aperçu verrouiller sémantique est requise pour les scénarios dans lesquels le client souhaite différer ou [suspendue](service-bus-dead-letter-queues.md) un message.

## <a name="client-side-batching"></a>Le traitement par lots côté client

Le traitement par lots côté client permet à un client file d’attente ou rubrique retarder l’envoi d’un message pour une période donnée. Si le client envoie des messages supplémentaires pendant cette période, il transmet les messages dans un seul lot. Le traitement par lots côté client entraîne également un client file d’attente/abonnement pour une sélection de plusieurs requêtes **achevé** dans une seule demande. Le traitement par lots est uniquement disponible pour les opérations asynchrones **Envoyer** et **achevé** . Opérations synchrones sont immédiatement envoyées au service Bus des services. Le traitement par lots ne se produire pour aperçu ni les opérations de réception, ni le traitement par lots se produit-il sur les clients.

Si le lot dépasse la taille maximale des messages, le dernier message est supprimé à partir du lot et le client envoie immédiatement le lot. Le dernier message devient le premier message de la feuille suivante. Par défaut, un client utilise un intervalle de traitement par lots de 20 ms. Vous pouvez modifier l’intervalle de traitement par lots en définissant la propriété [BatchFlushInterval][] avant de créer l’usine de messagerie. Ce paramètre affecte tous les clients créés par cette usine.

Pour désactiver le traitement par lots, définissez la propriété [BatchFlushInterval][] sur **TimeSpan.Zero**. Par exemple :

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Le traitement par lots n’affecte pas le nombre d’opérations de messagerie facturables et est disponible uniquement pour le protocole client Bus des services. Le protocole HTTP ne prend pas en charge le traitement par lots.

## <a name="batching-store-access"></a>Le traitement par lots accès au magasin

Pour augmenter le débit d’un file d’attente/rubrique/abonnement, Service Bus lots plusieurs messages lors de l’écriture dans son magasin interne. Si activé sur une rubrique ou file d’attente, écriture de messages dans le magasin est regroupé. Si activé sur un abonnement ou file d’attente, suppression des messages à partir du magasin est regroupée. Si l’accès par lots store est activé pour une entité, Service Bus diffère une opération d’écriture magasin concernant cette entité en jusqu'à 20 ms. Opérations supplémentaires store qui se produisent pendant cet intervalle sont ajoutées au lot. Accès au magasin par lots affecte uniquement les opérations **Envoyer** et **achevé** ; recevoir des opérations ne sont pas affectées. Accès au magasin par lots est une propriété sur une entité. Le traitement par lots se produit à toutes les entités qui permettent l’accès par lots store.

Lorsque vous créez une nouvelle file d’attente, sujet ou d’abonnement, accès au magasin par lots est activée par défaut. Pour désactiver l’accès au magasin groupées, définissez la propriété [EnableBatchedOperations][] sur **false** avant de créer l’entité. Par exemple :

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Accès au magasin par lots n’affecte pas le nombre d’opérations de messagerie facturables et est une propriété d’une file d’attente, sujet ou d’abonnement. Il est indépendant du mode réception et le protocole utilisé entre un client et le service Bus des services.

## <a name="prefetching"></a>Lecture anticipée

Lecture anticipée permet au client file d’attente ou d’abonnement charger des messages supplémentaires à partir du service lorsqu’elle effectue une opération de réception. Le client stocke ces messages dans un cache local. La taille du cache est déterminée par les propriétés [QueueClient.PrefetchCount][] ou [SubscriptionClient.PrefetchCount][] . Chaque client qui permet de lecture anticipée met à jour son propre cache. Un cache n’est pas partagé entre les clients. Si le client lance une opération de réception et son cache est vide, le service transmet un lot de messages. La taille du lot est égal à la taille de la mémoire cache ou 256 Ko, selon ce qui est inférieure. Si le client lance une opération de réception et le cache contient un message, le message est considérée à partir du cache.

Lorsqu’un message est préalablement, le service verrouille le message préalablement lues. En procédant ainsi, le message préalablement lues ne peuvent pas être reçu par un autre destinataire. Si le destinataire ne pourrez pas terminer le message avant du verrou arrive à expiration, le message devient disponible pour les autres destinataires. La copie préalablement extraites du message est conservé dans le cache. Le combiné qui utilise la copie mise en cache qui a expiré recevront une exception lorsqu’il tente de terminer ce message. Par défaut, le verrou message expire après 60 secondes. Cette valeur peut être prolongée à 5 minutes. Pour éviter la consommation de messages qui a expiré, la taille de cache doit toujours être plus petite que le nombre de messages qui peuvent être utilisés par un client dans l’intervalle de délai d’expiration de verrouillage.

Lorsque vous utilisez l’expiration de verrouillage par défaut de 60 secondes, une bonne valeur pour [SubscriptionClient.PrefetchCount][] est 20 fois la maximale vitesse de traitement de tous les récepteurs d’usine. Par exemple, une usine crée 3 récepteurs et chaque destinataire peut traiter jusqu'à 10 messages par seconde. Le nombre de lecture préalable ne doit pas dépasser 20\*3\*10 = 600. Par défaut, [QueueClient.PrefetchCount][] est défini à 0, ce qui signifie qu’aucun message supplémentaire n’est lue à partir du service.

Lecture anticipée messages augmente le débit global pour un abonnement ou file d’attente, car elle réduit le nombre total d’opérations sur les messages ou aller-retour. Extraire le premier message, cependant, prendra plus de temps (en raison de la taille du message accrue). Recevoir des messages préalablement lues sera plus rapide, car ces messages ont déjà été téléchargés par le client.

La propriété (TTL) durée de vie d’un message est activée par le serveur au moment où que le serveur envoie le message vers le client. Le client ne vérifie pas la propriété du message TTL lors de la réception du message. En revanche, le message pouvant être reçu même si TTL du message est passée tandis que le message a été mis en cache par le client.

Lecture anticipée n’affecte pas le nombre d’opérations de messagerie facturables et est disponible uniquement pour le protocole client Bus des services. Le protocole HTTP ne prend pas en charge la lecture anticipée. Lecture anticipée n’est disponible pour les opérations de réception synchrone et asynchrone.

## <a name="express-queues-and-topics"></a>Express files d’attente et rubriques

Entités Express activer haut débit et réduction des scénarios de latence. Avec express entités, si un message est envoyé à un file d’attente ou une rubrique, le message n'est pas immédiatement stocké dans le magasin de messagerie. Au lieu de cela, il est mis en cache en mémoire. Si un message reste dans la file d’attente pendant plus de quelques secondes, il est automatiquement écrite de stockage, afin de le protéger contre la perte en raison d’une panne stable. Rédiger le message dans un cache de mémoire augmente le débit et réduit la latence, car il n’est pas accès au stockage stable au moment où que le message est envoyé. Messages qui sont consommées quelques secondes ne sont pas écrits dans la banque d’informations. L’exemple suivant crée une rubrique expresse.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Si un message contenant des informations importantes qui ne doivent pas être perdues est envoyé à une entité expresse, l’expéditeur pouvez forcer Bus des services pour conserver immédiatement le message à stockage stable en définissant la propriété [ForcePersistence][] sur **true**.

## <a name="use-of-partitioned-queues-or-topics"></a>Utilisation des files d’attente partitionnées ou rubriques

En interne, Bus des services utilise le même nœud et messagerie stocker pour traiter et stocker tous les messages d’une entité messagerie (file d’attente ou rubrique). Un partitionnée file d’attente ou une rubrique, quant à eux, est réparti entre plusieurs nœuds et banques de messagerie. Rubriques et files d’attente partitionnées rendement non seulement un débit plus élevé que files d’attente normales et les rubriques, ils présentent également une disponibilité supérieure. Pour créer une entité partitionnée, définissez la propriété [EnablePartitioning][] sur **true**, comme illustré dans l’exemple suivant. Pour plus d’informations sur les entités partitionnées, voir [partition entités de messagerie][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Utilisation de plusieurs files d’attente

Si elle n’est pas possible d’utiliser un partitionnée file d’attente ou une rubrique ou la charge attendue ne peut pas être gérée par une seule file d’attente partitionnée ou une rubrique, vous devez utiliser plusieurs entités de messagerie. Lorsque vous utilisez plusieurs entités, créez un client dédié pour chaque entité, au lieu d’utiliser le même client pour toutes les entités.

## <a name="development--testing-features"></a>Fonctionnalités de test et de développement

Bus des services possède une fonctionnalité qui est utilisé spécifiquement pour le développement qui **ne doivent jamais être utilisés dans les configurations de production**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)
- Lorsque les nouvelles règles ou des filtres sont ajoutés à la rubrique, EnableFilteringMessagesBeforePublishing peuvent servir à vérifier que la nouvelle expression de filtre fonctionne comme prévu.

## <a name="scenarios"></a>Scénarios

Les sections suivantes décrivent les scénarios de messagerie par défaut et hiérarchique les paramètres de Service Bus par défaut. Taux de débit sont classés comme petite (moins de 1 message/seconde), modérer (1 message/seconde ou supérieur, mais moins de 100 messages/seconde) seuils (100 messages/deuxième ou supérieur). Le nombre de clients est considéré comme petites modéré (5 ou moins), (plus de 5, mais inférieur ou égal à 20) et volumineux (plus de 20).

### <a name="high-throughput-queue"></a>Haut débit file d’attente

Objectif : Optimiser le débit d’une seule file d’attente. Le nombre d’expéditeurs et destinataires est petit.

-   Utiliser une file d’attente partitionnée de disponibilité et de performances améliorées.

-   Pour augmenter le taux d’envoi global dans la file d’attente, utiliser plusieurs sites message pour créer des expéditeurs approuvés. Pour chaque expéditeur, utilisez les opérations asynchrones ou plusieurs threads.

-   Pour augmenter la vitesse de réception globale à partir de la file d’attente, utiliser plusieurs sites message pour créer les récepteurs.

-   Opérations asynchrones permet de tirer parti des traitement par lots côté client.

-   Définir l’intervalle de traitement par lots pour l’aller-retour de 50 millisecondes pour réduire le nombre de transmission de protocole Bus des services client. Si plusieurs expéditeurs sont utilisés, augmentez l’intervalle de traitement par lots de 100 millisecondes.

-   Laissez l’accès par lots store. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la file d’attente.

-   Définissez le nombre de récupération à 20 fois la maximale vitesse de traitement de tous les récepteurs d’une usine. Cela permet de réduire le nombre de transmission de protocole Bus des services client.

### <a name="multiple-high-throughput-queues"></a>Plusieurs files d’attente haut débit

Objectif : Optimiser le débit global de plusieurs files d’attente. Le débit d’une file d’attente individuelle est modéré ou élevé.

Pour obtenir le débit maximal sur plusieurs files d’attente, utilisez les paramètres sous forme de plan pour maximiser le débit d’une seule file d’attente. En outre, utiliser différentes références pour créer des clients qui envoyer ou recevoir de différentes files d’attente.

### <a name="low-latency-queue"></a>File d’attente de latence faible

Objectif : Réduire la latence de bout en bout d’un file d’attente ou une rubrique. Le nombre d’expéditeurs et destinataires est petit. Le débit de la file d’attente est petite ou modéré.

-   Utiliser une file d’attente partitionnée pour améliorer la disponibilité.

-   Désactiver le traitement par lots côté client. Le client envoie immédiatement un message.

-   Désactiver l’accès par lots store. Le service écrit immédiatement le message dans le magasin.

-   Si vous utilisez un seul client, définissez le nombre lecture préalable à 20 fois le taux de traitement du destinataire. Si plusieurs messages arrivent dans la file d’attente en même temps, le protocole client Service Bus les transmet tous en même temps. Lorsque le client reçoit le message suivant, il n’est déjà dans le cache local. Le cache doit être petit.

-   Si vous utilisez plusieurs clients, définissez le nombre de récupération à 0. En procédant ainsi, le second client peut recevoir le second message alors que le premier client traite toujours le premier message.

### <a name="queue-with-a-large-number-of-senders"></a>Avec un grand nombre d’expéditeurs en file d’attente

Objectif : Optimiser le débit d’une rubrique avec un grand nombre d’expéditeurs ou file d’attente. Chaque expéditeur envoie des messages avec un taux modéré. Le nombre de destinataires est petit.

Service Bus permet jusqu'à 1000 connexions simultanées à une entité messagerie (ou 5000 à l’aide de AMQP). Cette limite s’applique au niveau de l’espace de noms et files d’attente/rubriques/abonnements sont limitées par la limite de connexions simultanées par espace de noms. Des files d’attente, ce nombre est partagé entre les expéditeurs et destinataires. Si toutes les connexions de 1000 sont requises pour les expéditeurs, vous devez remplacer la file d’attente avec une rubrique et un seul abonnement. Une rubrique accepte jusqu'à 1000 connexions simultanées provenant d’expéditeurs, alors que l’abonnement accepte un connexions simultanées 1000 supplémentaires à partir de récepteurs. Si vous avez besoin de plus de 1000 expéditeurs simultanées, les expéditeurs doivent envoyer des messages du protocole Bus des services via le protocole HTTP.

Pour maximiser le débit, procédez comme suit :

-   Utiliser une file d’attente partitionnée de disponibilité et de performances améliorées.

-   Si chaque expéditeur se trouve dans un autre processus, utilisez uniquement même usine par processus.

-   Opérations asynchrones permet de tirer parti des traitement par lots côté client.

-   Utilisez la valeur par défaut le traitement par lots intervalle de 20 ms pour réduire le nombre de transmission de protocole Bus des services client.

-   Laissez l’accès par lots store. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la file d’attente ou une rubrique.

-   Définissez le nombre de récupération à 20 fois la maximale vitesse de traitement de tous les récepteurs d’une usine. Cela permet de réduire le nombre de transmission de protocole Bus des services client.

### <a name="queue-with-a-large-number-of-receivers"></a>File d’attente avec un grand nombre de destinataires

Objectif : Optimiser la vitesse de réception d’un abonnement avec un grand nombre de destinataires ou file d’attente. Chaque destinataire reçoit des messages à un rythme modéré. Le nombre d’expéditeurs est petit.

Bus des services Active jusqu'à 1000 connexions simultanées à une entité. Si une file d’attente requiert plus de 1000 récepteurs, vous devez remplacer la file d’attente avec une rubrique et plusieurs abonnements. Chaque abonnement peut prendre en charge jusqu'à 1000 connexions. Récepteurs peuvent également accéder à la file d’attente via le protocole HTTP.

Pour maximiser le débit, procédez comme suit :

-   Utiliser une file d’attente partitionnée de disponibilité et de performances améliorées.

-   Si chaque destinataire se trouve dans un autre processus, utilisez uniquement même usine par processus.

-   Récepteurs permet d’opérations asynchrones ou synchrones. Étant donné le taux de réception modéré d’un récepteur individuel, côté client le traitement par lots de la demande complète n’affecte pas débit récepteur.

-   Laissez l’accès par lots store. Cela permet de réduire la charge globale de l’entité. Il permet également de réduire le taux global auquel les messages puissent être écrits dans la file d’attente ou une rubrique.

-   Définir le nombre prefetch à une petite valeur (par exemple, PrefetchCount = 10). Cela empêche récepteurs d’inactivité autres récepteurs ont grand nombre de messages mis en cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Rubrique avec un petit nombre d’abonnements

Objectif : Optimiser le débit d’une rubrique avec un petit nombre d’abonnements. Un message est reçu par le nombre d’abonnements, ce qui signifie que le taux de réception combinées sur tous les abonnements est plus grand que le taux d’envoi. Le nombre d’expéditeurs est petit. Le nombre de destinataires par abonnement est petit.

Pour maximiser le débit, procédez comme suit :

-   Utilisez une rubrique partitionnée de disponibilité et de performances améliorées.

-   Pour augmenter le taux d’envoi global dans la rubrique, utiliser plusieurs sites message pour créer des expéditeurs approuvés. Pour chaque expéditeur, utilisez les opérations asynchrones ou plusieurs threads.

-   Pour augmenter la vitesse de réception globale à partir d’un abonnement, utiliser plusieurs sites message pour créer les récepteurs. Pour chaque destinataire, utilisez les opérations asynchrones ou plusieurs threads.

-   Opérations asynchrones permet de tirer parti des traitement par lots côté client.

-   Utilisez la valeur par défaut le traitement par lots intervalle de 20 ms pour réduire le nombre de transmission de protocole Bus des services client.

-   Laissez l’accès par lots store. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la rubrique.

-   Définissez le nombre de récupération à 20 fois la maximale vitesse de traitement de tous les récepteurs d’une usine. Cela permet de réduire le nombre de transmission de protocole Bus des services client.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Rubrique avec un grand nombre d’abonnements

Objectif : Optimiser le débit d’une rubrique avec un grand nombre d’abonnements. Un message est reçu par le nombre d’abonnements, c'est-à-dire que le taux de réception combinées sur tous les abonnements est beaucoup plus grand que le taux d’envoi. Le nombre d’expéditeurs est petit. Le nombre de destinataires par abonnement est petit.

Rubriques avec un grand nombre d’abonnements exposent généralement un faible débit global si tous les messages sont routés vers tous les abonnements. Cela est dû au fait que chaque message est reçu autant de fois, et tous les messages qui figurent dans une rubrique et toutes ses abonnements sont stockés dans le même magasin. Il est supposé que le nombre d’expéditeurs et le nombre de destinataires par abonnement sont petits. Bus des services prend en charge jusqu'à 2 000 abonnements par sujet.

Pour maximiser le débit, procédez comme suit :

-   Utilisez une rubrique partitionnée de disponibilité et de performances améliorées.

-   Opérations asynchrones permet de tirer parti des traitement par lots côté client.

-   Utilisez la valeur par défaut le traitement par lots intervalle de 20 ms pour réduire le nombre de transmission de protocole Bus des services client.

-   Laissez l’accès par lots store. Cela permet d’augmenter le taux global auquel les messages puissent être écrits dans la rubrique.

-   Définir le nombre de récupération à 20 fois le taux de réception attendue dans secondes. Cela permet de réduire le nombre de transmission de protocole Bus des services client.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’optimisation des performances de Bus des services, consultez [partitionnées messagerie entités][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entités messageries partitionnées]: service-bus-partitioning.md
  