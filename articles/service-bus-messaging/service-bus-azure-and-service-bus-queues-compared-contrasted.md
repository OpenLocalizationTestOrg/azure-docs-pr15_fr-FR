<properties 
    pageTitle="Azure files d’attente et Bus des services en file d’attente - comparaison et contraste | Microsoft Azure"
    description="Analyse les différences et les ressemblances entre deux types de files d’attente offertes par Azure."
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
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure files d’attente et Bus des services en file d’attente - comparaison et contraste

Cet article analyse les différences et les similitudes entre les deux types de files d’attente offertes par Microsoft Azure aujourd'hui : files d’attente files d’attente Azure et Bus des services. À l’aide de ces informations, vous pouvez comparer et comparez les technologies correspondantes et pourrez prendre des décisions mieux informées sur solution qui conviennent le mieux à vos besoins.

## <a name="introduction"></a>Introduction

Microsoft Azure prend en charge les deux types de mécanismes de file d’attente : **Files d’attente Azure** et **Files d’attente de Bus de Service**.

**Files d’attente azure**qui font partie de l’infrastructure de [stockage Azure](https://azure.microsoft.com/services/storage/) , dotés d’une interface basée sur REST place/Get/aperçu simple, fournir une fiable, permanente messagerie dans et entre les services.

**Files d’attente Bus des services** font partie d’une plus large infrastructure [Azure de messagerie](https://azure.microsoft.com/services/service-bus/) qui prend en charge queuing ainsi que de publication/abonnement distante de service Web et des modèles d’intégration. Pour plus d’informations sur les files d’attente Bus des services, les rubriques/abonnements et de relais, consultez la [vue d’ensemble des Bus des services de messagerie](service-bus-messaging-overview.md).

Bien que les deux files d’attente technologies existent simultanément, files d’attente Azure introduits tout d’abord, comme mécanisme de stockage de file d’attente dédiée basé sur les services de stockage Azure. Files d’attente Bus des services sont construits en haut de l’infrastructure « demandée messagerie » plus large conçue pour intégrer des applications ou des composants d’application qui s’étend sur plusieurs protocoles de communication, des contrats de données, approuvent des domaines et/ou environnements réseau.

Cet article compare les technologies de deux files d’attente offertes par Azure en expliquant les différences entre le comportement et l’implémentation des fonctionnalités fournies par chacun. Cet article présente également les conseils relatifs au choix quelles fonctionnalités correspond le mieux à vos besoins de développement d’applications.

## <a name="technology-selection-considerations"></a>Considérations relatives à la sélection d’assistance

Files d’attente files d’attente Azure et Bus des services sont mises en œuvre du message queuing service actuellement proposé sur Microsoft Azure. Chacune possède un ensemble de fonctionnalités légèrement différente, ce qui signifie que vous pouvez choisir l’un ou l’autre, ou utilisez les deux, selon les besoins de votre solution particulier ou un problème d’entreprise/technique à résoudre.

Pour déterminer quelle est la technologie files d’attente s’adapte l’objectif d’une solution donnée, les développeurs et les architectes de solution devraient prendre en compte les recommandations ci-dessous. Pour plus d’informations, voir la section suivante.

En tant que solution architecte/développeur, **envisagez d’utiliser des files d’attente Azure** lorsque :

- Votre application doit stocker supérieure à 80 Go de messages dans une file d’attente, où les messages ont une durée de vie plus courte et plus de 7 jours.

- Votre application souhaite suivre la progression de traitement d’un message dans la file d’attente. Ceci est utile si le traitement d’un message de collaborateur se bloque. Un collaborateur ultérieur pouvez ensuite utiliser ces informations pour continuer à partir de l’endroit où le travail préalable étiez arrêté.

- Vous avez besoin les journaux côté serveur de toutes les transactions exécutées vos files d’attente.

En tant que solution architecte/développeur, **envisagez d’utiliser des files d’attente Service Bus** lorsque :

- Votre solution doit pouvoir recevoir des messages sans avoir à interroger la file d’attente. À Service, il est possible par le biais d’interrogation longue opération à l’aide des protocoles TCP qui prend en charge Bus des services de réception.

- Votre solution requiert la file d’attente pour fournir une garantie premier-in-first-out (FIFO) classées remise.

- Vous voulez une expérience symétrique dans Azure et sur Windows Server (cloud privé). Pour plus d’informations, voir [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Votre solution doit être en mesure de prendre en charge la détection automatique des doublons.

- Vous souhaitez que votre application pour traiter les messages sous forme de flux longue parallèles (messages sont associés à un flux de données à l’aide de la propriété [ID de session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) sur le message). Dans ce modèle, chaque nœud dans l’application consommateur concurrence pour les flux, contrairement à des messages. En fonction d’un flux de données à un nœud beaucoup, le nœud peut examiner l’état de l’état de flux d’application à l’aide de transactions.

- Votre solution requiert comportement transactions et atomicité pour l’envoi ou réception de plusieurs messages à partir d’une file d’attente.

- Caractéristique time to live (TTL) de la charge de travail spécifique à l’application peut dépasser le délai de 7 jours.

- Votre application gère les messages pouvant être supérieur à 64 Ko mais limite approche probablement pas le 256 Ko.

- Vous travaillez avec un besoin pour fournir un modèle d’accès basé sur un rôle aux files d’attente et droits/autorisations différentes pour les expéditeurs et destinataires.

- Taille de votre file d’attente croissance n’est pas supérieure à 80 Go.

- Vous souhaitez utiliser le protocole de messagerie normalisée AMQP 1.0. Pour plus d’informations sur AMQP, voir [Vue d’ensemble de Service Bus AMQP](./service-bus-amqp-overview.md).

- Vous pouvez avoir une idée une migration éventuelle de communication de point à point file d’attente vers un modèle d’échange de message qui permet l’intégration transparente des destinataires supplémentaires (abonnés), chacun d'entre eux reçoit des copies indépendantes de certains ou tous les messages envoyés à la file d’attente. Le second fait référence à la fonctionnalité de publication et d’abonnement en mode natif fournie par Bus des services.

- Votre solution de messagerie doit être en mesure de prendre en charge la garantie de livraison « Plus-simultanément » sans qu’il soit nécessaire pour générer les composants d’infrastructure supplémentaires.

- Vous voulez être en mesure de publier et l’utilisation des lots de messages.

- Vous avez besoin d’intégration complète avec la pile de communication Windows Communication Foundation (WCF) dans le .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Comparaison des files d’attente files d’attente Azure et Bus des services

Les tables dans les sections suivantes fournissent un regroupement logique des fonctionnalités de file d’attente et vous permettent de comparer, en un clin de œil, les fonctionnalités disponibles dans les deux Azure files d’attente et Bus des services.

## <a name="foundational-capabilities"></a>Ajouter aux fonctionnalités

Cette section compare certaines fonctionnalités files d’attente fondamentales fournies par files d’attente files d’attente Azure et Bus des services.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Classement de garantie technologique|**N°** <br/><br>Pour plus d’informations, voir la première note dans la section « Plus d’informations ».</br>|**Oui --In-First-Out (FIFO)**<br/><br>(au moyen de sessions de messagerie)|
|Garantie de livraison|**Au moins une**|**Au moins une**<br/><br/>**La plupart-simultanément**|
|Prise en charge de l’opération atomique|**N°**|**Oui**<br/><br/>|
|Comportement de réception|**Non bloquant**<br/><br/>(se termine immédiatement si aucun message n’est trouvé)|**Blocage avec/sans délai d’expiration**<br/><br/>(offres long l’interrogation ou la [« technique Comet »](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Non bloquant**<br/><br/>(au moyen de .NET API managée uniquement)|
|API de type push|**N°**|**Oui**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) et **OnMessage** sessions API .NET.|
|Mode de réception|**Aperçu et location**|**Aperçu et verrou**<br/><br/>**Recevoir et supprimer**|
|En mode exclusif|**En fonction de location**|**Verrouillage**|
|Durée Location/verrou|**30 secondes (par défaut)**<br/><br/>**7 jours (maximum)** (Vous pouvez renouveler ou libérer une location de message à l’aide de l’API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) .)|**60 secondes (par défaut)**<br/><br/>Vous pouvez renouveler un verrou de message à l’aide de l’API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .|
|Location/verrouillage précision|**Niveau de message**<br/><br/>(chaque message peut avoir une valeur de délai d’expiration différent, vous pouvez ensuite à jour en fonction des besoins pendant le traitement du message, à l’aide de l’API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) )|**Niveau file d’attente**<br/><br/>(chaque file d’attente a une précision de verrouillage s’applique à toutes ses messages, mais vous pouvez renouveler le verrou à l’aide de l’API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) ).|
|Ayez regroupé recevoir|**Oui**<br/><br/>(spécifiant explicitement du nombre de messages lors de la récupération des messages, avec un maximum de 32 messages)|**Oui**<br/><br/>(implicitement activant une propriété lecture préalable ou explicitement par le biais de transactions)|
|Envoi par lots|**N°**|**Oui**<br/><br/>(au moyen de transactions ou le traitement par lots côté client)|

### <a name="additional-information"></a>Informations supplémentaires

- Messages dans les files d’attente Azure sont généralement first-in-first-out, mais elles peuvent parfois être désordonnés ; par exemple, lorsque durée de délai d’expiration d’un message visibilité expire (par exemple, d’une application cliente cesse-t-il de fonctionner pendant le traitement). Lorsque le délai d’expiration de visibilité arrive à expiration, le message devient visible à nouveau dans la file d’attente pour un autre collaborateur il la file d’attente. À ce stade, le message que vous venez visible peut être placé dans la file d’attente (retiré à nouveau) après un message qui se trouvait en file d’attente après celui-ci.

- Si vous utilisez déjà des objets BLOB Azure stockage ou Tables et que vous démarrez à l’aide de files d’attente, vous êtes certain 99,9 %. Si vous utilisez des objets BLOB ou des tableaux avec files d’attente Bus des services, vous devrez disponibilité inférieure.

- Le modèle FIFO garanti dans files d’attente Bus des services nécessite l’utilisation de sessions de messagerie. Dans le cas où l’application se bloque pendant le traitement d’un message reçu en mode **Aperçu & Verrouiller** , la prochaine fois qu'un destinataire file d’attente accepte une session de messagerie, il commence par le message qui a échoué après sa durée de vie (TTL) expiration.

- Files d’attente Azure sont conçues pour prendre en charge les scénarios files d’attente standards, tels que les composants d’application découplage pour augmenter la tolérance de pannes et extensibilité élevées chargent nivellement et la création de processus de flux de travail.

- Files d’attente Bus des services prend en charge la garantie de livraison *Au moins une* . En outre, *La plupart-simultanément* sémantique peut être pris en charge en utilisant l’état de session pour stocker l’état de l’application et à l’aide de transactions atomique recevoir des messages et mettre à jour l’état de session.

- Files d’attente Azure procurent un modèle de programmation uniform et cohérent files d’attente, des tableaux et des objets BLOB – à la fois pour les développeurs et les équipes opérationnelles.

- Files d’attente Service Bus prennent en charge les transactions locales dans le contexte d’une seule file d’attente.

- Le mode de **recevoir et supprimer** pris en charge par Bus des services fournit la possibilité de réduire le nombre d’opérations messagerie (et le coût associé) en échange d’assurance remise exposant.

- Files d’attente Azure constituent location avec la possibilité d’étendre location pour les messages. Ainsi, les travailleurs de maintenir courte location dans les messages. Par conséquent, si un collaborateur se bloque, le message peut être rapidement traité à nouveau par un autre collaborateur. En outre, un collaborateur peut étendre la location sur un message s’il faut traiter plus de la durée actuelle.

- Files d’attente Azure offrent un délai de visibilité que vous pouvez définir lors de la fait ou retrait d’un message. En outre, vous pouvez mettre à jour un message avec des valeurs différentes Location au moment de l’exécution et mettre à jour les différentes valeurs dans l’ensemble des messages dans la même file d’attente. Délais de verrouillage Bus des services sont définis dans les métadonnées file d’attente ; Toutefois, vous pouvez renouveler le verrou en appelant la méthode [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- Le délai maximal pour un blocage de réception opération dans files d’attente Bus des services est 24 jours. Toutefois, des délais d’expiration basée sur REST ont une valeur maximale de 55 secondes.

- Le traitement par lots de côté client fournies par Bus des services permet à un client file d’attente pour une sélection de plusieurs messages dans une opération d’envoi unique. Le traitement par lots n’est disponible pour les opérations d’envoi asynchrone.

- Fonctionnalités telles que le plafond 200 to Azure files d’attente (plus lorsque vous virtualisez de comptes) et illimités rendent une solution idéale pour les fournisseurs de SaaS.

- Files d’attente Azure constituent un flexible et performant délégué mécanisme de contrôle d’accès.

## <a name="advanced-capabilities"></a>Fonctionnalités avancées

Cette section compare les fonctionnalités avancées fournies par files d’attente files d’attente Azure et Bus des services.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Remise planifiée|**Oui**|**Oui**|
|Lettres inactive automatique|**N°**|**Oui**|
|Valeur de durée de vie croissante de file d’attente|**Oui**<br/><br/>(via la mise à jour de la place de délai d’expiration de visibilité)|**Oui**<br/><br/>(fourni par le biais une fonction API dédiée)|
|Empoisonner prise en charge du message|**Oui**|**Oui**|
|Mise à jour sur place|**Oui**|**Oui**|
|Journal des transactions côté serveur|**Oui**|**N°**|
|Métriques de stockage|**Oui**<br/><br/>**Métrique minute**: fournit les mesures en temps réel pour API de disponibilité, pt appellent nombre, le nombre d’erreur et plus d’informations, tout en temps réel (agrégé par minute et signalé pendant quelques minutes de simplement où est passée en production. Pour plus d’informations, voir [à propos des indicateurs de stockage Analytique](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Oui**<br/><br/>(en bloc des requêtes en appelant [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Gestion des États|**N°**|**Oui**<br/><br/>De [Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), groupes de [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Transfert automatique des messages|**N°**|**Oui**|
|Vider fonction file d’attente|**Oui**|**N°**|
|Groupes de messages|**N°**|**Oui**<br/><br/>(au moyen de sessions de messagerie)|
|État de l’application par groupe de messages|**N°**|**Oui**|
|Détection des doublons|**N°**|**Oui**<br/><br/>(configurable sur le côté de l’expéditeur)|
|Intégration de WCF|**N°**|**Oui**<br/><br/>(offre les liaisons de prédéfinies WCF)|
|Intégration de WF|**Personnalisé**<br/><br/>(nécessite la création d’une activité personnalisée WF)|**Natif**<br/><br/>(propose des activités WF de prédéfinies)|
|Parcourir les groupes de messages|**N°**|**Oui**|
|L’extraction des sessions de messagerie par code|**N°**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Les deux files d’attente technologies permettent à un message et peut être planifiée pour la remise ultérieurement.

- Transfert de file d’attente automatique permet des milliers de files d’attente automatique-transférer leurs messages à une seule file d’attente, à partir de laquelle la réception application consomme le message. Vous pouvez utiliser ce mécanisme pour obtenir une sécurité, contrôler le flux et isoler stockage entre chaque éditeur de message.

- Files d’attente Azure prennent en charge pour mettre à jour le contenu du message. Vous pouvez utiliser cette fonctionnalité pour conserver les informations état et mises à jour de l’avancement dans le message afin qu’elles puissent être traitées à partir du dernier point de contrôle connu, au lieu de démarrer à partir de zéro. Avec files d’attente Bus des services, vous pouvez activer le même scénario au moyen de sessions de messagerie. Sessions permettent d’enregistrer et récupérer l’état de traitement de l’application (en utilisant [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Lettres inactive](service-bus-dead-letter-queues.md), qui est uniquement prise en charge par les files d’attente Bus des services, peut être utile pour isoler des messages qui ne peut pas être analysés avec succès par l’application destinataire ou messages ne peut pas atteindre sa destination en raison d’une propriété qui a expiré (TTL) de durée de vie. La valeur TTL indique la durée pendant laquelle un message reste dans la file d’attente. À Service, le message va être déplacé vers une file d’attente spécial appelé $DeadLetterQueue à la fin de la durée de vie.

- Pour trouver des messages « poison » dans files d’attente Azure, lorsque le retrait d’un message l’application examine la propriété **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** du message. Si **DequeueCount** est supérieure à un certain seuil, l’application déplace le message vers une file d’attente défini par l’application « inactive lettre ».

- Azure files d’attente permettent d’obtenir un journal détaillé de toutes les transactions exécutées la file d’attente, comme métrique bien comme agrégé. Ces deux options sont utiles pour le débogage et comprendre la manière dont votre application utilise les files d’attente Azure. Ils sont également utiles pour votre application réglage des performances et de réduire les coûts d’utilisation des files d’attente.

- La notion de « sessions message « pris en charge par le Service Bus permet aux messages qui appartiennent à un certain groupe logique à associer à un destinataire donné, qui à son tour crée une affinité j’aime session entre les messages et leurs récepteurs correspondantes. Vous pouvez activer cette fonctionnalité avancée dans Service marché en définissant la propriété [ID de session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) dans un message. Récepteurs ensuite un ID de session spécifique écoutent et recevoir des messages qui partagent l’identificateur de session spécifié.

- La fonctionnalité de détection de doublons pris en charge par les files d’attente Service Bus automatiquement supprime les doublons de messages envoyés à une file d’attente ou la rubrique, en fonction de la valeur de la propriété [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacité et des quotas

Cette section compare files d’attente files d’attente Azure et Bus des services du point de vue de [capacité et des quotas](service-bus-quotas.md) qui peuvent s’appliquer.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Taille maximale de file d’attente|**200 TO**<br/><br/>(limité à une capacité de compte de stockage unique)|**1 Go à 80 Go**<br/><br/>(défini lors de la création d’une file d’attente et [l’activation de partition](service-bus-partitioning.md) – voir la section « Plus d’informations »)|
|Taille maximale des messages|**64 KO**<br/><br/>(48 Ko lors de l’utilisation de codage **en base 64** )<br/><br/>Azure prend en charge les messages volumineux en combinant des files d’attente et des objets BLOB – à partir de laquelle vous pouvez file d’attente jusqu'à 200 Go pour un seul élément.|**256 Ko** ou **1 Mo**<br/><br/>(y compris en-tête et le corps, la taille maximale d’en-tête : 64 Ko).<br/><br/>Dépend du [niveau de service](service-bus-premium-messaging.md).|
|Durée de vie maximale des messages|**7 jours**|**`TimeSpan.Max`**|
|Nombre maximal de files d’attente|**Illimité**|**10 000**<br/><br/>(par espace de noms de service, peut être augmentée)|
|Nombre maximal de clients simultanées|**Illimité**|**Illimité**<br/><br/>(100 limite de connexion simultanée s’applique uniquement aux communications basées sur le protocole TCP)|

### <a name="additional-information"></a>Informations supplémentaires

- Bus des services applique les limites de taille de file d’attente. La taille maximale de file d’attente spécifiée lors de la création de la file d’attente et peut avoir une valeur comprise entre 1 et 80 Go. Si la valeur de taille de la file d’attente définie sur la création de la file d’attente est atteint, les messages entrants supplémentaires seront rejetés et une exception sera reçue par le code d’appel. Pour plus d’informations sur les quotas dans Service marché, voir [Quotas Bus du Service](service-bus-quotas.md).

- Vous pouvez créer des files d’attente Bus des services dans les tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Avec partition activé (qui est la valeur par défaut), Bus des services crée 16 partitions pour chaque Go que vous spécifiez. Dès lors, si vous créez une file d’attente de 5 Go taille, avec 16 partitions la taille maximale de file d’attente devient (5 * 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente partitionnée ou une rubrique en consultant son entrée sur le [portail Azure][].

- Avec files d’attente Azure, si le contenu du message n’est pas adaptée XML, puis elle doit être **en base 64** codé. Si vous **en base 64**-coder le message, la charge utile utilisateur peut être jusqu'à 48 Ko, au lieu de 64 Ko.

- Des files d’attente Bus des services, chaque message stocké dans une file d’attente se compose de deux parties : un en-tête et un corps. La taille totale du message ne peut pas dépasser la taille maximale des messages pris en charge par le niveau de service.

- Lorsque les clients à communiquer avec files d’attente Bus des services via le protocole TCP, le nombre maximal de connexions simultanées à une seule file d’attente Bus des services est limité à 100. Ce nombre est partagé entre les expéditeurs et destinataires. Si ce quota est atteint, les demandes suivantes pour les connexions supplémentaires seront rejetés et une exception sera reçue par le code d’appel. Cette limite pas sur les clients qui se connectent les files d’attente à l’aide de l’API basée sur REST.

- Si vous avez besoin de plus de 10 000 files d’attente dans un espace de noms Bus des services, vous pouvez contacter l’équipe de support Azure et demander une augmentation. Pour mettre à l’échelle au-delà de 10 000 files d’attente à Service, vous pouvez également créer des espaces de noms supplémentaires à l’aide du [portail Azure][].

## <a name="management-and-operations"></a>Gestion et opérations

Cette section compare les fonctionnalités de gestion fournies par files d’attente files d’attente Azure et Bus des services.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Protocole de gestion|**Le pointeur sur HTTP/HTTPS**|**Le pointeur sur HTTPS**|
|Protocole Runtime|**Le pointeur sur HTTP/HTTPS**|**Le pointeur sur HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP avec TLS)**|
|API managée .NET|**Oui**<br/><br/>(Espace de stockage Client API managée .NET)|**Oui**<br/><br/>(Managé .NET demandée API de messagerie)|
|C++ natif|**Oui**|**N°**|
|API Java|**Oui**|**Oui**|
|API PHP|**Oui**|**Oui**|
|Node.js API|**Oui**|**Oui**|
|Support de métadonnées arbitraire|**Oui**|**N°**|
|Règles d’appellation file d’attente|**Jusqu'à 63 caractères**<br/><br/>(Lettres d’un nom de file d’attente doivent être en minuscules.)|**Jusqu'à 260 caractères**<br/><br/>(Les noms et les chemins d’accès de la file d’attente respectent la casse.)|
|Obtenir la fonction de longueur file d’attente|**Oui**<br/><br/>(Valeur approchée si expiration au-delà de la durée de vie des messages sans être supprimées.)|**Oui**<br/><br/>(Valeur exacte, point-à-temps).|
|Fonction Aperçu|**Oui**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Files d’attente Azure prennent en charge les attributs arbitraires qui peuvent être appliqués à la description de la file d’attente, sous la forme de paires nom/valeur.

- Les deux technologies file d’attente offrent la possibilité de lire un message sans avoir à verrouiller, qui peut être utile lors de l’implémentation d’un outil de navigateur/explorer file d’attente.

- Service Bus .NET demandée messagerie API Exploitez bidirectionnelle TCP connexions pour améliorer les performances par rapport au reste sur HTTP, et ils prennent en charge le protocole standard AMQP 1.0.

- Les noms des files d’attente Azure peut être de 3 à 63 caractères long, peut contenir toutes les lettres minuscules, des nombres et des traits d’union. Pour plus d’informations, voir [appellation files d’attente et les métadonnées](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Les noms de file d’attente Bus des services peuvent être 260 caractères au maximum et possèdent des règles d’appellation moins restrictifs. Noms des files d’attente de Bus des services peuvent contenir des lettres, des nombres, des périodes, des traits d’union et des traits de soulignement.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Cette section décrit les fonctionnalités d’autorisation et d’authentification pris en charge par les files d’attente files d’attente Azure et Bus des services.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Authentification|**Clé symétrique**|**Clé symétrique**|
|Modèle de sécurité|Accès délégué via jetons associations de sécurité.|ASSOCIATIONS DE SÉCURITÉ|
|Fédération fournisseur d’identité|**N°**|**Oui**|

### <a name="additional-information"></a>Informations supplémentaires

- Chaque demande pour une des technologies files d’attente doit être authentifié. Files d’attente publiques avec l’accès anonyme ne sont pas pris en charge. À l’aide des [associations de sécurité](service-bus-sas-overview.md), vous pouvez également traiter ce scénario en publiant un sa écriture seule, associations de sécurité en lecture seule ou même une sa accès complet.

- Le schéma d’authentification fourni par files d’attente Azure implique l’utilisation d’une clé symétrique, qui est un hachage HMAC-based Message Authentication Code (), calculée avec l’algorithme ça-256 et codée sous forme de chaîne **en base 64** . Pour plus d’informations sur le protocole respectif, consultez [authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Files d’attente Bus des services prend en charge un modèle similaire à l’aide des touches symétriques. Pour plus d’informations, voir [Partagés Signature authentification de l’accès à Service](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Coût

Cette section compare files d’attente files d’attente Azure et Bus des services du point de vue coût.

|Critères de comparaison|Azure files d’attente|Service Bus files d’attente|
|---|---|---|
|Coût des transactions file d’attente|**$0.0036**<br/><br/>(par 100 000 transactions)|**Niveau de base**: **0,05 $**<br/><br/>(par millions d’opérations)|
|Opérations facturables|**Tous les**|**Envoi/réception uniquement**<br/><br/>(sans frais pour d’autres opérations)|
|Transactions inactives|**Facturables**<br/><br/>(Interroger une file d’attente vide est considérée comme une transaction facturable.)|**Facturables**<br/><br/>(Une réception par rapport à une file d’attente vide est considéré comme un message facturable).|
|Coût de stockage|**$0.07**<br/><br/>(par Go par mois)|**0,00 f**|
|Frais de transfert de données sortantes|**$0,12 - $0.19**<br/><br/>(Selon la géographie.)|**$0,12 - $0.19**<br/><br/>(Selon la géographie.)|

### <a name="additional-information"></a>Informations supplémentaires

- Transferts de données sont facturés selon le montant total des données sans modifier les centres de données Azure via internet d’une période de facturation donnée.

- Transferts de données entre des services Azure situés dans la même région ne sont pas facturés.

- À ce jour, tous les transferts de données entrant ne sont pas facturés.

- Étant donné la prise en charge pour l’interrogation longue, à l’aide de files d’attente Bus de Service peut être rentable dans les situations où remise faible latence est requise.

>[AZURE.NOTE] Tous les coûts peuvent être modifiées. Ce tableau reflète les tarifs actuels et n’inclut pas les offres promotionnelles peuvent être actuellement disponibles. Pour obtenir des informations récentes sur Azure tarifs, consultez la page [Azure tarifs](https://azure.microsoft.com/pricing/) . Pour plus d’informations sur les tarifs Bus des services, voir [tarifs Bus des services](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusion

En acquérir une compréhension des deux technologies, vous ne pourrez pas prendre une décision mieux informée sur technologie file d’attente à utiliser et quand. La décision à quel moment utiliser files d’attente files d’attente Azure ou Service Bus dépend clairement un certain nombre de facteurs. Ces facteurs peuvent intensément dépendent des besoins de votre application et son architecture. Si votre application utilise déjà les principales fonctionnalités des Microsoft Azure, vous peut être préférable de choisir les files d’attente Azure, surtout si vous avez besoin de communication de base et la messagerie entre des services ou des files d’attente qu’il soit nécessaire qui peuvent être supérieures à 80 Go.

Étant donné que files d’attente Bus des services fournissent un certain nombre de fonctionnalités avancées, telles que des sessions, transactions, détection, automatique des doublons fonctionnalités inactive lettres et résistant publication/abonnement, ils peuvent être un choix si vous créez une application hybride ou si votre application dans le cas contraire requiert ces fonctionnalités.

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants fournissent des instructions et des informations sur l’utilisation des files d’attente files d’attente Azure ou Service Bus plus.

- [Comment utiliser les files d’attente de Bus de Service](service-bus-dotnet-get-started-with-queues.md)
- [Comment utiliser le Service de stockage file d’attente](../storage/storage-dotnet-how-to-use-queues.md)
- [Meilleures pratiques pour améliorer les performances à l’aide du Service Bus demandé de messagerie](service-bus-performance-improvements.md)
- [Présentation des files d’attente et rubriques dans Azure Service marché](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guide du développeur Bus des services](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Au moyen du Service files d’attente dans Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Présentation de stockage Azure facturation – bande passante, Transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Portail Azure]: https://portal.azure.com
 
