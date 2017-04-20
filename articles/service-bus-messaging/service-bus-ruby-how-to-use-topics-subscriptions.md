<properties
    pageTitle="Comment utiliser les thèmes du Service Bus (Ruby) | Microsoft Azure"
    description="Découvrez comment utiliser des rubriques Bus des services et abonnements dans Azure. Exemples de code sont écrits pour les applications prononciation."
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

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Comment utiliser le Service Bus rubriques/abonnements

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser rubriques Bus des services et abonnements à partir d’applications prononciation. Les scénarios présentés incluent la **Création de rubriques et abonnements, création de filtres d’abonnement, envoi de messages** à un sujet, **recevoir des messages à partir d’un abonnement**et **suppression des rubriques et abonnements**. Pour plus d’informations sur des sujets et abonnements, consultez la section [Étapes suivantes](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Abonnements et les rubriques de Bus des services

Abonnements et les rubriques de Service Bus prennent en charge une *publication et d’abonnement* modèle de communication de messagerie. Lorsque vous utilisez des abonnements et les rubriques, des composants d’une application distribuée ne pas communiquent directement entre eux ; au lieu de cela, ils échangent des messages via une rubrique qui se comporte comme un intermédiaire.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement à des files d’attente Bus des services, où chaque message est traité par un seul consommateur, rubriques et abonnements fournissent un formulaire **un-à-plusieurs** de communication, à l’aide d’un modèle de publication et d’abonnement. Il est possible d’enregistrer plusieurs abonnements à un sujet. Lorsqu’un message est envoyé à un sujet, puis le rend disponible à chaque abonnement pour traiter séparément.

Un abonnement rubrique ressemble à une file d’attente virtuelle qui reçoit une copie des messages qui ont été envoyés à la rubrique. Vous pouvez enregistrer le filtre règles pour une rubrique dans une base par abonnement, qui vous permet de restreindre/filtre les messages à un sujet sont reçus par les abonnements rubrique.

Abonnements et les rubriques de Service Bus permettent de mettre à l’échelle de traiter un grand nombre de messages au sein d’un grand nombre d’utilisateurs et d’applications.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser des files d’attente Bus des services dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application. Vous devez créer l’espace de noms via l’interface de ligne de commande, car le [portail Azure][] ne crée pas de l’espace de noms avec une connexion ACS.

Pour créer un espace de noms :

1. Ouvrez une fenêtre de la console Powershell Azure.

2. Tapez la commande suivante pour créer un espace de noms. Indiquez la valeur de votre propre espace de noms ainsi la même région que votre application.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Créer Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obtenir des informations d’identification de gestion de l’espace de noms par défaut

Pour effectuer les opérations de gestion, telles que la création d’une file d’attente sur le nouvel espace de noms, vous devez obtenir les informations d’identification de gestion de l’espace de noms.

L’applet de commande PowerShell que vous avez exécuté pour créer l’espace de noms Bus des services affiche la clé que vous pouvez utiliser pour gérer l’espace de noms. Copiez la valeur **DefaultKey** . Vous utiliserez cette valeur dans votre code, plus loin dans ce didacticiel.

![Copie de clé](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> Vous trouverez également cette clé si vous ouvrez une session sur le [portail Azure][] et accédez aux informations de connexion pour votre espace de noms.

## <a name="create-a-ruby-application"></a>Créer une application prononciation

Pour plus d’informations, voir [créer une Application Ruby sur Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurez votre application pour utiliser le Bus des services

Pour utiliser le Service Bus, téléchargez et utilisez le package Ruby Azure, qui inclut un ensemble de bibliothèques de commodité communiquer avec les services reste de stockage.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems permet d’obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal Server** (Mac) ou **Bash** (Unix).

2. Tapez « azure d’installation marque » dans la fenêtre de commande pour installer la marque et les dépendances.

### <a name="import-the-package"></a>Importer le package

À l’aide de votre éditeur de texte, ajoutez le code suivant en haut du fichier prononciation dans lequel vous envisagez d’utiliser le stockage :

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurer une connexion de Service Bus

Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_espace de noms** et **AZURE\_SERVICEBUS\_ACCESS\_clé** pour les informations requises pour vous connecter à votre espace de noms. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** avec le code suivant :

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Définissez la valeur de l’espace de noms avec la valeur que vous avez créé plutôt que l’URL complète. Par exemple, utilisez **« yourexamplenamespace »**, pas « yourexamplenamespace.servicebus.windows.net ».

## <a name="create-a-topic"></a>Créer un sujet

L’objet **Azure::ServiceBusService** vous permet d’utiliser des thèmes. Le code suivant crée un objet **Azure::ServiceBusService** . Pour créer un sujet, utilisez la **créer\_topic()** méthode. L’exemple suivant crée un sujet ou imprime les erreurs si tel est le cas.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Vous pouvez également passer un objet **Azure::ServiceBus::Topic** avec des options supplémentaires, qui permettent de modifier les paramètres de rubrique par défaut tels que le temps de message à live ou une taille maximale de file d’attente. L’exemple suivant illustre la définition de la taille maximale de file d’attente à 5 Go et l’heure en direct et une minute :

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Créer des abonnements

Abonnement à une rubrique est également créés avec l’objet **Azure::ServiceBusService** . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages remis à la file d’attente virtuelle de l’abonnement.

Abonnements sont permanentes et continueront à exister qu’après l’avoir ou la rubrique elles sont associées, sont supprimés. Si votre application contienne une logique pour créer un abonnement, il doit tout d’abord vérifier si l’abonnement existe déjà en utilisant la méthode getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre par défaut qui est utilisé si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « tous les messages » et utilise le filtre de **MatchAll** par défaut.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier les messages envoyés à un sujet doivent figurer au sein d’un abonnement spécifique.

Le type de filtre pris en charge par les abonnements plus souple est **Azure::ServiceBus::SqlFilter**, qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisées avec un filtre SQL, passez en revue la syntaxe de la [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Vous pouvez ajouter des filtres à un abonnement à l’aide de la **créer\_rule()** méthode de l’objet **Azure::ServiceBusService** . Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut, ou la **MatchAll** remplacera tous les autres filtres que vous pouvez spécifier. Vous pouvez supprimer la règle par défaut à l’aide de la **Supprimer\_rule()** méthode sur l’objet **Azure::ServiceBusService** .

L’exemple suivant crée un abonnement nommé « haute-messages » avec une **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages qui ont un personnalisé **message\_nombre** la propriété supérieure à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

De même, l’exemple suivant crée un abonnement nommé « faible-messages » avec une **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **numéro_de_message** inférieur ou égal à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Lorsqu’un message est maintenant envoyé à « test de sujets », il est toujours être remis aux récepteurs d’abonné à l’abonnement de la rubrique « tous les messages » et sélectivement remis aux récepteurs d’abonné pour les abonnements rubrique « haute-messages » et « faible- » (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application doit utiliser la **envoyer\_rubrique\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Les messages envoyés vers des rubriques Bus des services sont instances des objets **Azure::ServiceBus::BrokeredMessage** . **Azure::ServiceBus::BrokeredMessage** objets comportent un ensemble de propriétés standard (par exemple **d’étiquette** et **heure\_à\_live**), un dictionnaire est utilisé pour inclure des propriétés spécifiques application personnalisée et un corps de données de type chaîne. Une application peut définir le corps du message en passant une valeur de chaîne à la **envoyer\_rubrique\_à message()** méthode et tout obligatoire propriétés standard seront remplies par les valeurs par défaut.

L’exemple suivant montre comment envoyer cinq tester les messages à « rubrique de test ». Notez que la valeur de propriété personnalisée **numéro_de_message** de chaque message varie selon l’itération de la boucle (détermine type d’abonnement reçoit votre) :

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. La taille de cette rubrique est définie au moment de la création, avec un maximum de 5 Go.

## <a name="receive-messages-from-a-subscription"></a>Recevoir des messages à partir d’un abonnement

Les messages provenant d’un abonnement à l’aide du **recevoir\_abonnement\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Par défaut, les messages sont read(peak) et verrouillé sans supprimer de l’abonnement. Vous pouvez lire et supprimer le message de l’abonnement en définissant la **Aperçu\_verrouillage** option **false**.

Le comportement par défaut facilite la lecture et la suppression d’une opération de deux étages, qui permet également en charge les applications qui ne peuvent tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant **Supprimer\_abonnement\_à message()** méthode et fournir le message à supprimer en tant que paramètre. La **Supprimer\_abonnement\_à message()** méthode marquer le message comme étant consommées et supprimer de l’abonnement.

Si la **: aperçu\_verrouillage** paramètre est défini sur **false**, lecture et suppression du message devient le modèle le plus simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Bus des services sera avez marqué le message comme occupé, puis lorsque l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

L’exemple suivant montre comment les messages pouvant être reçus et traitées en utilisant **recevoir\_abonnement\_à message()**. L’exemple reçoit tout d’abord et supprime un message de l’abonnement « messages faible » à l’aide de **: aperçu\_verrouillage** définie sur **false**, il reçoit un autre message à partir de « haute-messages » et puis supprime le message à l’aide **Supprimer\_abonnement\_à message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application récepteur est impossible de traiter le message pour une raison quelconque, alors qu’il peut appeler la **déverrouiller\_abonnement\_à message()** méthode sur l’objet **Azure::ServiceBusService** . Dans ce cas Bus des services déverrouiller le message au sein de l’abonnement et le rendre disponible perçu à nouveau, par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Vous trouverez également un délai d’expiration associée à un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant le délai d’expiration de verrouillage expire (par exemple, si l’application se bloque), puis Bus des services sera déverrouiller automatiquement le message et rendez-le disponible perçu à nouveau.

Dans le cas où l’application se bloque après le traitement mais avant que le message le **Supprimer\_abonnement\_à message()** méthode est appelée, puis le message est redistribué à l’application lorsqu’il redémarre. Cette option est souvent appelée **Au moins une fois traitement**; Autrement dit, chaque message sera traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Cette logique est souvent obtenue à l’aide de la **message\_id** propriété du message, ce qui reste constante au sein de tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

Rubriques d’abonnements sont permanentes et doivent être explicitement supprimés à partir du [portail Azure][] ou par programme. L’exemple ci-dessous montre comment supprimer la rubrique nommée « rubrique de test ».

```
azure_service_bus_service.delete_topic("test-topic")
```

Suppression d’un sujet entraîne celle de tous les abonnements qui sont enregistrés avec la rubrique. Abonnements peuvent également être supprimés séparément. Le code suivant montre comment supprimer l’abonnement nommé « haute-messages » à partir de la rubrique « test de sujets » :

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des rubriques Bus des services, suivez ces liens pour en savoir plus.

- Voir [files d’attente, de sujets et abonnements](service-bus-queues-topics-subscriptions.md).
- Référence des API pour [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visitez le référentiel [Azure SDK pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.
 
[Portail Azure]: https://portal.azure.com
