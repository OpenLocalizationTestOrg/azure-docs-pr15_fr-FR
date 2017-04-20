<properties 
    pageTitle="Vue d’ensemble des exemples de Bus des services de messagerie | Microsoft Azure"
    description="Attribuer une catégorie et décrit Bus de Service d’exemples de liens vers chacun d’échanges."
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
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-messaging-samples"></a>Exemples de messagerie Bus des services

Les exemples de messagerie Service Bus illustrent des fonctionnalités clés de [Bus des services de messagerie](https://azure.microsoft.com/services/service-bus/) (service cloud) et du [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Cet article attribuer une catégorie et décrit les exemples disponibles, avec des liens vers chacun.

>[AZURE.NOTE] Exemples de Bus des services ne sont pas installés avec le Kit de développement. Pour obtenir les exemples, visitez la [page des exemples Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Par ailleurs, il est un ensemble mis à jour de Service Bus exemples d’échanges [ici](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (à ce jour, ils ne sont pas décrites dans cet article).  

Pour des exemples de relais, consultez [exemples de relais Bus des services](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Bus des services de messagerie

Les exemples suivants illustrent comment écrire des applications qui utilisent Bus des services de messagerie.

Notez que les exemples de messagerie doivent être une chaîne de connexion pour accéder à votre espace de noms Bus des services.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Pour obtenir une chaîne de connexion pour Bus des services Azure

1. Connectez-vous au [portail Azure](http://portal.azure.com).

1. Dans la colonne de gauche, cliquez sur **Bus des services**.

1. Cliquez sur le nom de votre espace de noms dans la liste.

3. Dans la carte de l’espace de noms, cliquez sur **stratégies d’accès partagé**.

4. Dans la carte de **stratégies d’accès partagé** , cliquez sur **RootManageSharedAccessKey**.

6. Copiez la chaîne de connexion dans le Presse-papiers.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Pour obtenir une chaîne de connexion de Service Bus pour Windows Server

1. Exécutez l’applet de commande PowerShell suivante :

    ```
    get-sbClientConfiguration
    ```

2. Collez la chaîne de connexion dans le fichier App.config pour l’échantillon.

### <a name="getting-started-samples"></a>Prise en main exemples

Ces exemples décrivent les fonctionnalités de messagerie de base.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Mise en route : Messagerie avec files d’attente](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’une file d’attente.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Mise en route : Messagerie avec rubriques](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’un sujet avec plusieurs abonnements.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|

### <a name="exploring-features"></a>Exploration des fonctions

Les exemples suivants illustrent diverses fonctionnalités du Service Bus.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Fournisseurs de jeton HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Montre les différentes façons d’authentifier un client HTTP/reste à Service.|2.1|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Service Bus HTTP Client](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Comment envoyer des messages et recevoir des messages de Bus des services via HTTP/reste.|2.3|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Service Bus transfert automatique](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Comment transférer automatiquement des messages à partir d’une file d’attente, abonnement ou lettres mortes dans une autre file d’attente ou rubrique. Il montre également comment envoyer un message dans une file d’attente ou une rubrique via une file d’attente de transfert.|2.3|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Exemple de Session de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Montre comment utiliser Microsoft Azure Bus des services à l’aide des canaux Windows Communication Foundation (WCF). L’exemple illustre l’utilisation des canaux WCF pour envoyer et recevoir des messages via une file d’attente Bus des services. L’exemple indique la session et communication non session sur le Bus de Service.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Demandé de messagerie : Transactions](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Montre comment utiliser le Bus des services Microsoft Azure fonctionnalités au sein d’une étendue de transaction de messagerie afin de garantir lots d’opérations de messagerie sont validées automatiquement.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[La messagerie traité : Opérations de gestion utilisent reste](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Montre comment effectuer des opérations de gestion sur Bus des services à l’aide de reste.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Fournisseur de ressources API REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Montre comment utiliser les nouvelles reste API de Service Bus RDFE pour gérer les espaces de noms et entités de messagerie.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Exemple de Session de Service WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Montre comment utiliser Microsoft Azure Bus des services à l’aide du modèle de service WCF. L’exemple illustre l’utilisation du modèle de service WCF pour l’accomplissement de communication basé sur session via une file d’attente Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Réponse de demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Montre comment utiliser le Bus des services Microsoft Azure et la fonctionnalité de demande/réponse. L’exemple montre simples clients et les serveurs de communication via une file d’attente Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : inactive file d’attente](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de « file d’attente inactive lettre » messagerie. L’exemple montre un expéditeur simple et le destinataire communication via une file d’attente Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Des Messages différée](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Montre comment utiliser la fonctionnalité de report de message de Microsoft Azure Service Bus. L’exemple montre un expéditeur simple et le destinataire communication via une file d’attente Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Messages de Session](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Montre comment utiliser Microsoft Azure Service Bus et les fonctionnalités de la Session de messagerie. L’exemple montre simples expéditeurs et destinataires communication via une file d’attente Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Rubrique de réponse de demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Montre comment implémenter le modèle de requête/réponse à l’aide des abonnements et les rubriques de Bus des services Microsoft Azure. L’exemple montre simples clients et les serveurs de communication via une rubrique Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : File d’attente de réponse demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de demande/réponse. L’exemple montre simples clients et les serveurs de communication via deux files d’attente de Bus des services.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Détection des doublons](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Montre comment utiliser la détection de message en double Bus des services Microsoft Azure des files d’attente. Il crée deux files d’attente, un avec détection des doublons activée et autres un sans détection des doublons.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Messagerie asynchrone](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages en mode asynchrone à partir d’une file d’attente. La file d’attente fournit une communication découplée, asynchrone entre un expéditeur et le nombre de destinataires (ici, un seul récepteur).|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Montre comment utiliser Microsoft Azure Service Bus publication/abonnement filtres avancés. Il crée une rubrique et 3 abonnements avec les définitions de filtre différent, envoie des messages à la rubrique et reçoit tous les messages d’abonnements.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Messagerie traité : Récupération de Messages](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Montre comment utiliser la fonctionnalité de récupération de messages Bus des services Microsoft Azure. Il montre comment utiliser la fonctionnalité de récupération de messages lors de la réception.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|

## <a name="service-bus-reference-tools"></a>Outils de référence Bus des services

Les exemples suivants illustrent diverses fonctionnalités du service.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Service Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|L’Explorateur de Bus Service permet aux utilisateurs de se connecter à un espace de noms de service Service Bus et gérer les entités messageries d’une manière facile. L’outil fournit des fonctionnalités avancées telles que les fonctionnalités importer/exporter et la possibilité de tester entités messageries et services de relais.|1.8|Bus des services Microsoft Azure ; Bus des services pour Windows Server|
|[Autorisation : SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Cet exemple montre comment créer et gérer des identités de service Microsoft Azure Active Directory contrôle d’accès (également appelé Service de contrôle d’accès ou ACS) pour une utilisation avec Bus des services.|N/A|Bus des services Microsoft Azure|

## <a name="next-steps"></a>Étapes suivantes

Consultez les rubriques suivantes pour une vue d’ensemble conceptuelle de Bus des services.

- [Vue d’ensemble de la messagerie Bus des services](service-bus-messaging-overview.md)
- [Architecture de Bus de service](service-bus-architecture.md)
- [Principes de base de Bus des services](service-bus-fundamentals-hybrid-solutions.md)
