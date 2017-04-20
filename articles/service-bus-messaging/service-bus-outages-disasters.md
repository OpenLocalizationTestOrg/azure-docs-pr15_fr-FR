<properties 
    pageTitle="Isolant des applications de Service Bus contre les interruptions et incidents | Microsoft Azure"
    description="Décrit les techniques que vous permettent de protéger les applications contre une panne de Service Bus potentielle."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Meilleures pratiques pour isolant applications contre défaillances Bus des services et d’urgence

Les applications critiques doivent fonctionner en continu, même en présence d’arrêts ou incidents. Cette rubrique décrit les techniques que vous pouvez utiliser pour protéger des applications de Service Bus par rapport à une interruption de service potentiel ou un incident.

Une panne est définie comme l’indisponibilité temporaire de Bus des services Azure. L’interruption peut affecter certains composants de Bus des services, par exemple une banque de messagerie, ou encore le centre de données entier. Une fois que le problème a été résolu, Bus de Service redevient disponible. En règle générale, une panne n’entraîne pas la perte de messages ou d’autres données. Exemple d’une défaillance de composant est l’indisponibilité d’un magasin de messagerie particulier. Exemple d’une panne du centre de données à l’échelle est une coupure de centre de données ou un commutateur réseau défectueux centre de données. Une interruption peut durer de quelques minutes à quelques jours.

Un incident est défini comme la perte définitive d’une unité d’échelle de Bus de Service ou d’un centre de données. Le centre de données peut ou ne peut devenir disponible à nouveau. En règle générale un incident entraîne la perte de certains ou tous les messages ou d’autres données. Exemples d’incidents sont fire, saturation ou séisme.

## <a name="current-architecture"></a>Architecture actuelle

Service Bus utilise plusieurs magasins de messagerie pour stocker les messages qui sont envoyés à files d’attente ou rubriques. Un non partitions file d’attente ou une rubrique est affecté à une seule banque de messagerie. Si cette banque de messagerie n’est pas disponible, toutes les opérations sur cette file d’attente ou une rubrique échouera.

Toutes les entités de messagerie Bus des services (files d’attente, rubriques, relais) se trouvent dans un espace de noms de service, qui est affilié à un centre de données. Service Bus ne permet pas de réplication geo automatique des données, ni autorise un espace de noms s’étalent sur plusieurs centres de données.

## <a name="protecting-against-acs-outages"></a>Protection contre les défaillances ACS

Si vous utilisez des informations d’identification ACS et ACS n’est plus disponible, les clients peuvent-ils n’est plus jetons. Clients ayant un jeton en temps QU'ACS s’arrête peuvent continuer à utiliser Bus des services jusqu'à ce que les jetons arriver à expiration. La durée de vie jeton par défaut est de 3 heures.

Pour vous protéger contre les défaillances ACS, utiliser des jetons Signature accès partagé (sa). Dans ce cas, le client s’authentifie directement à Service en vous connectant un jeton minted automatique avec une clé secrète. Les appels vers ACS ne sont plus requis. Pour plus d’informations sur les associations de sécurité jetons, voir [Bus de Service d’authentification][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Protection des files d’attente et rubriques par rapport à la messagerie échecs magasin

Un non partitions file d’attente ou une rubrique est affecté à une seule banque de messagerie. Si cette banque de messagerie n’est pas disponible, toutes les opérations sur cette file d’attente ou une rubrique échouera. Une file d’attente partitionnée, quant à eux, se compose de plusieurs fragments. Chaque fragment est stocké dans un magasin de messagerie différent. Lorsqu’un message est envoyé à un partitionnée file d’attente ou une rubrique, Bus des services affecte le message à un des fragments. Si la banque d’informations correspondante n’est pas disponible, Service Bus écrit le message vers un autre fragment, si possible. Pour plus d’informations sur les entités partitionnées, voir [partition entités de messagerie][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Protection contre les défaillances du centre de données ou de sinistre

Pour autoriser le basculement entre deux centres de données, vous pouvez créer un espace de noms de service Service Bus dans chaque centre de données. Par exemple, l' espace de noms du service Service Bus **contosoPrimary.servicebus.windows.net** peuvent se trouver dans la région des États-Unis d’Amérique du Nord/centrale et **contosoSecondary.servicebus.windows.net** peuvent se trouver dans la région Sud/centrale contactez-nous. Si un Bus de Service de messagerie entité doit rester accessible en présence d’une panne du centre de données, vous pouvez créer cette entité dans les deux espaces de noms.

Pour plus d’informations, voir la section « Échec de Bus des services dans un centre de données Azure » dans [les modèles de messagerie asynchrones et la disponibilité][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protection des points de terminaison de relais contre les défaillances du centre de données ou de sinistre

Geo-réplication de points de terminaison de relais permet à un service qui expose un point de terminaison relais est accessible en présence de défaillances Bus des services. Pour atteindre geo réplication, le service doit créer deux points de terminaison relais différents espaces de noms. Les espaces de noms doivent se trouver dans différents centres de données et les deux extrémités doivent avoir des noms différents. Par exemple, un point de terminaison primaire sont accessibles sous **contosoPrimary.servicebus.windows.net/myPrimaryService**, tandis que son équivalent secondaire pouvez être contacté sous **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Le service écoute puis sur les deux extrémités et un client puisse appeler le service via un point de terminaison. Une application cliente aléatoirement sélectionne l’une des relais comme point de terminaison principal et envoie sa demande au point de terminaison active. Si l’opération échoue avec un code d’erreur, cette erreur indique que le point de terminaison relais n’est pas disponible. L’application ouvre un canal au point de terminaison sauvegarde et émet de nouveau la demande. À ce stade actif et les points de terminaison sauvegarde changer de rôle : l’application cliente considère le point de terminaison active ancien le nouveau point de terminaison de sauvegarde et le point de terminaison sauvegarde ancien soit le point de terminaison active nouvelle. Si les deux opérations Échec d’envoi, les rôles des deux entités restent inchangées et une erreur est renvoyée.

L’exemple [Geo-réplication avec Service Bus relais Messages][] montre comment répliquer relais.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Protection des files d’attente et rubriques contre les défaillances du centre de données ou de sinistre

Pour obtenir la résilience contre les défaillances du centre de données lors de l’aide dans la messagerie, Bus des services prend en charge deux approches : réplication *actif* et *passif* . Pour chaque approche, si un file d’attente ou une rubrique doit rester accessible en présence d’une panne du centre de données, vous pouvez le créer dans les deux espaces de noms. Les deux entités peuvent avoir le même nom. Par exemple, une file d’attente principale sont accessibles sous **contosoPrimary.servicebus.windows.net/myQueue**, tandis que son équivalent secondaire pouvez être contacté sous **contosoSecondary.servicebus.windows.net/myQueue**.

Si l’application ne nécessite pas de communication expéditeur-destinataire permanente, l’application peut implémenter un résistant file d’attente côté client pour éviter toute perte de message et à protéger l’expéditeur à partir des erreurs de Bus des services transitoires.

## <a name="active-replication"></a>Réplication Active

La réplication Active utilise entités dans les deux espaces de noms pour chaque opération. N’importe quel client qui envoie un message envoie deux copies du même message. La première copie est envoyée à l’entité principale (par exemple, **contosoPrimary.servicebus.windows.net/sales**) et la deuxième copie du message est envoyée à l’entité secondaire (par exemple, **contosoSecondary.servicebus.windows.net/sales**).

Un client reçoit des messages dans les deux files d’attente. Le combiné traite la première copie d’un message, et la deuxième copie est supprimée. Pour supprimer les doublons de messages, l’expéditeur doit ajouter des balises à chaque message avec un identificateur unique. Les deux copies du message doivent être marqués avec le même identificateur. Vous pouvez utiliser les propriétés [BrokeredMessage.MessageId][] ou [BrokeredMessage.Label][] , ou une propriété personnalisée pour marquer le message. Le destinataire doit gérer une liste des messages qu’il a déjà reçus.

[Geo-réplication avec Service Bus demandée Messages][] illustre la réplication de la messagerie entités active.

> [AZURE.NOTE] L’approche réplication active double le nombre d’opérations, par conséquent, cette approche peut affecter les coût plus élevé.

## <a name="passive-replication"></a>Réplication passive

Dans le cas abri des pannes, réplication passive utilise un seul des deux entités de messagerie. Un client envoie le message à l’entité active. Si l’opération sur l’entité active échoue avec un code d’erreur qui indique le centre de données qui héberge l’entité active ne soit pas disponible, le client envoie une copie du message à l’entité de sauvegarde. À ce stade actif et les entités sauvegarde changer de rôle : le client d’envoi considère l’ancienne entité active soit la nouvelle entité de sauvegarde, et l’entité de sauvegarde anciens est la nouvelle active. Si les deux opérations Échec d’envoi, les rôles des deux entités restent inchangées et une erreur est renvoyée.

Un client reçoit des messages dans les deux files d’attente. Car il est possible que le destinataire reçoit deux copies du même message, le destinataire doit supprimer les messages en double. Vous pouvez supprimer les doublons de la même manière décrite dans la réplication active.

En règle générale, réplication passive est plus économique que la réplication active, car dans la plupart des cas qu’une seule opération est effectuée. Latence, le débit et coût monétaire sont identiques pour le scénario non répliquée.

Lorsque vous utilisez réplication passive, dans les scénarios suivants messages peuvent être perdus ou reçus à deux reprises :

-   **Message délai ou une perte**: part du principe que l’expéditeur a été envoyé un message m1 à la file d’attente principale et la file d’attente deviendrait indisponible avant que le destinataire ne reçoive m1. L’expéditeur envoie un m2 de message qui s’affiche dans la file d’attente secondaire. Si la file d’attente principal est temporairement indisponible, le destinataire reçoit m1 dès que la file d’attente redevienne disponible. En cas de sinistre, le destinataire peut recevoir jamais m1.

-   **Dupliquer réception**: part du principe que l’expéditeur envoie un message m à la file d’attente principale. Service Bus traite m correctement mais ne parvient pas à envoyer une réponse. Une fois l’opération d’envoi arrive à expiration, l’expéditeur envoie une copie de m dans la file d’attente secondaire. Si le destinataire est en mesure de recevoir la première copie de m avant la file d’attente principal n’est plus disponible, le destinataire reçoit les deux copies du m environ simultanément. Si le destinataire n’est pas en mesure de recevoir la première copie de m avant la file d’attente principal n’est plus disponible, le destinataire reçoit initialement uniquement la deuxième copie du m, mais puis reçoit une deuxième copie de m quand la file d’attente primaire devient disponible.

[Geo-réplication avec Service Bus demandée Messages][] illustre passive réplication de la messagerie entités.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur sinistre, voir les articles suivants :

- [Continuité des activités de base de données SQL Azure][]
- [Conseils techniques résilience Azure][]

  [Authentification Bus du service]: service-bus-authentication-and-authorization.md
  [Entités messageries partitionnées]: service-bus-partitioning.md
  [Modèles de messagerie asynchrones et la disponibilité]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [Geo-réplication avec Service Bus relais des Messages]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Geo-réplication avec Service Bus demandé de Messages]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuité des activités de base de données SQL Azure]: ../sql-database/sql-database-business-continuity.md
  [Conseils techniques résilience Azure]: ../resiliency/resiliency-technical-guidance.md
