<properties
    pageTitle="Comment utiliser des files d’attente Bus des services avec Ruby | Microsoft Azure"
    description="Découvrez comment utiliser les files d’attente Bus des services dans Azure. Exemples de code écrites en Ruby."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Comment utiliser les files d’attente Bus des services

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce guide décrit l’utilisation des files d’attente Bus des services. Les exemples sont écrits en Ruby et utilisent la marque Azure. Les scénarios présentés incluent la **Création de files d’attente, l’envoi et réception de messages**et la **suppression des files d’attente**. Pour plus d’informations sur les files d’attente Bus des services, consultez la section [Étapes suivantes](#next-steps) .

## <a name="what-are-service-bus-queues"></a>Que sont les files d’attente Bus des services ?

Files d’attente Bus des services prend en charge un modèle de communication *traité de messagerie* . Des files d’attente, les composants d’une application distribuée ne pas communiquent directement entre eux ; à la place qu’ils échangent des messages via une file d’attente, qui se comporte comme un intermédiaire. Un producteur de message (expéditeur) transmet un message dans la file d’attente et puis continue son traitement.
En mode asynchrone, un consommateur de message (récepteur) extrait le message à partir de la file d’attente et la traite. Le producteur n’a pas à attendre une réponse du consommateur afin de continuer à traiter et envoyer des messages. Files d’attente offrent **First In, première absence (FIFO)** remise des messages à un ou plusieurs consommateurs concurrents. Autrement dit, les messages sont généralement reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par consommateur qu’un seul message.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Files d’attente Bus des services sont une technologie à usage général qui peut être utilisée pour un large éventail de scénarios :

-   Communication entre les rôles web et de travail dans une [application Azure multi-niveaux](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Communication entre les applications en local et Azure hébergé applications dans une [solution hybride](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Communication entre les composants d’une application distribuée en cours d’exécution en local dans différentes organisations ou départements d’une organisation.

À l’aide de files d’attente peut vous permettent d’évoluer vos applications mieux et activer la résilience plus à votre architecture.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser des files d’attente Bus des services dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application. Vous devez créer l’espace de noms via l’interface de ligne de commande, car le portail Azure ne crée pas de l’espace de noms avec une connexion ACS.

Pour créer un espace de noms :

1. Ouvrez une console Powershell Azure.

2. Tapez la commande suivante pour créer un espace de noms Bus des services. Indiquez la valeur de votre propre espace de noms ainsi la même région que votre application.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obtenir des informations d’identification de gestion de l’espace de noms

Pour effectuer les opérations de gestion, telles que la création d’une file d’attente sur le nouvel espace de noms, vous devez obtenir les informations d’identification de gestion de l’espace de noms.

L’applet de commande PowerShell que vous avez exécuté pour créer l’espace de noms bus service Azure affiche la clé que vous pouvez utiliser pour gérer l’espace de noms. Copiez la valeur **DefaultKey** . Vous utiliserez cette valeur dans votre code, plus loin dans ce didacticiel.

![Copie de clé](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] Vous trouverez également cette clé si vous ouvrez une session sur le [portail Azure](https://portal.azure.com/) et accédez aux informations de connexion pour votre espace de noms Bus des services.

## <a name="create-a-ruby-application"></a>Créer une application prononciation

Créer une application prononciation. Pour plus d’informations, voir [créer une Application Ruby sur Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser Bus des services

Pour utiliser Bus des services Azure, téléchargez et utilisez le package Ruby Azure, qui inclut un ensemble de bibliothèques de commodité communiquer avec les services reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems permet d’obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation marque » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier prononciation dans laquelle vous souhaitez utiliser le stockage :

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurer une connexion Bus des services Azure

Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_espace de noms** et **AZURE\_SERVICEBUS\_ACCESS_KEY** pour plus d’informations requises pour vous connecter à votre espace de noms Bus des services. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** avec le code suivant :

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Définissez la valeur de l’espace de noms à la valeur que vous avez créé, plutôt que l’URL complète. Par exemple, utilisez **« yourexamplenamespace »**, pas « yourexamplenamespace.servicebus.windows.net ».

## <a name="how-to-create-a-queue"></a>Comment créer une file d’attente

L’objet **Azure::ServiceBusService** vous permet d’utiliser des files d’attente. Pour créer une file d’attente, utilisez la méthode **create_queue()** . L’exemple suivant crée une file d’attente ou imprime des erreurs.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Vous pouvez également passer un objet **Azure::ServiceBus::Queue** avec des options supplémentaires, qui permet de remplacer les paramètres de file d’attente par défaut, telles que la durée de vie du message ou la taille maximale de file d’attente. L’exemple suivant montre comment définir la taille maximale de file d’attente à 5 Go et l’heure en direct et une minute :

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Comment envoyer des messages à une file d’attente

Pour envoyer un message à une file d’attente Bus des services, vous application appelle le **envoyer\_file d’attente\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Messages envoyés à (et reçus à partir de) files d’attente de Bus des services sont des objets **Azure::ServiceBus::BrokeredMessage** et disposent d’un ensemble de propriétés standard (tels que des **étiquettes** et **heure\_à\_live**), un dictionnaire est utilisé pour inclure des propriétés spécifiques application personnalisée et corps des données d’application arbitraire. Une application peut définir le corps du message en passant une valeur de chaîne en tant que le message et les propriétés requises standards seront remplies avec les valeurs par défaut.

L’exemple suivant montre comment envoyer un message de test dans la file nommée « file de test » à l’aide **envoyer\_file d’attente\_à message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Files d’attente Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une file d’attente, mais il existe une lettrine sur la taille totale des messages émanant d’une file d’attente. Cette taille file d’attente est définie au moment de la création, avec un maximum de 5 Go.

## <a name="how-to-receive-messages-from-a-queue"></a>Comment faire pour recevoir des messages à partir d’une file d’attente

Les messages provenant d’une file d’attente à l’aide du **recevoir\_file d’attente\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Par défaut, les messages sont lire et verrouillés sans les supprimer de la file d’attente. Toutefois, vous pouvez supprimer des messages de la file d’attente dès qu’elles sont lues en définissant la **: peek_lock** option sur **false**.

Le comportement par défaut facilite la lecture et la suppression d’une opération de deux étages, qui permet également en charge les applications qui ne peuvent tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer\_file d’attente\_à message()** méthode et fournir le message à supprimer en tant que paramètre. La **Supprimer\_file d’attente\_à message()** méthode marquer le message comme étant consommées et supprimer de la file d’attente.

Si la **: aperçu\_verrouillage** paramètre est défini sur **false**, lecture et suppression du message devient le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme étant consommées, lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide **recevoir\_file d’attente\_à message()**. L’exemple reçoit tout d’abord et supprime un message à l’aide de **: aperçu\_verrouillage** définie sur **false**, il reçoit un autre message et puis supprime le message à l’aide **Supprimer\_file d’attente\_à message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, alors qu’il peut appeler la **déverrouiller\_file d’attente\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Dans ce cas Bus des services déverrouiller le message dans la file d’attente et rendez-le disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé dans la file d’attente, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Service Bus déverrouiller automatiquement le message et rend disponible pour être reçus à nouveau.

Dans le cas où l’application se bloque après le traitement mais avant que le message le **Supprimer\_file d’attente\_à message()** méthode est appelée, puis le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois traitement**; Autrement dit, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Cette opération est souvent réalisée à l’aide de la **message\_id** propriété du message, ce qui reste constante pour tous les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des files d’attente Bus des services, suivez ces liens pour en savoir plus.

-   Vue d’ensemble des [files d’attente, de sujets et abonnements](service-bus-queues-topics-subscriptions.md).
-   Visitez le référentiel [Azure SDK pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.

Pour une comparaison entre les files d’attente Bus des services Azure mentionnés dans cet article et files d’attente Azure mentionnés dans l’article [comment utiliser le stockage de file d’attente de Ruby](../storage/storage-ruby-how-to-use-queue-storage.md) , voir [files d’attente Azure et Azure Service Bus files d’attente - comparaison et Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
