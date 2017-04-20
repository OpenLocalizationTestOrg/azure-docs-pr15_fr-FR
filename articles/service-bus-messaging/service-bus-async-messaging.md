<properties 
    pageTitle="Bus des services de messagerie asynchrone | Microsoft Azure"
    description="Description de la messagerie asynchrone Bus des services."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Modèles de messagerie asynchrones et la disponibilité

Messagerie asynchrone peut être implémentée de différentes manières différentes. Avec files d’attente, rubriques et abonnements, Bus des services Azure prend en charge le comportement asynchrone via un magasin et mécanisme de transfert. Normalement (synchrone), vous envoyez des messages à des files d’attente et rubriques et recevez des messages des files d’attente et abonnements. Applications que vous rédigez dépendent de ces entités toujours en cours disponibles. Lorsque l’état de l’entité change, en raison d’une variété de cas, vous devez fournir une entité fonction limitée répondant à la plupart des besoins.

Les applications utilisent les modèles de messagerie asynchrones pour activer un certain nombre de scénarios de communication. Vous pouvez créer des applications dans lequel les clients peuvent envoyer des messages aux services, même si le service n’est pas en cours d’exécution. Pour les applications de cette expérience rupture des communications, une file d’attente peut vous aider niveau la charge en fournissant un emplacement pour les communications tampon. Enfin, vous pouvez obtenir un équilibrage de charge simple mais efficace pour distribuer des messages sur plusieurs ordinateurs.

Pour maintenir la disponibilité d’une de ces entités, envisagez d’un certain nombre de différentes façons dans lequel ces entités peuvent apparaître indisponibles pour un système de messagerie résistant. En règle générale, nous voyons l’entité deviennent indisponibles pour les applications que nous écrire dans les différentes façons suivantes :

- Impossible d’envoyer des messages.

- Impossible de recevoir des messages.

- Impossible de gérer les entités (créer, récupérer, mettre à jour ou supprimer des entités).

- Impossible de contacter le service.

Pour chacun de ces erreurs, modes de panne différents existent qui permettent à une application de continuer à effectuer le travail à un niveau de la fonction limitée. Par exemple, un système qui peut envoyer des messages mais pas en recevoir pouvez continuent à recevoir les commandes des clients, mais ne peut pas traiter les commandes. Cette rubrique décrit les problèmes potentiels qui peuvent se produire, et comment sont atténuer ces problèmes. Bus des services a présenté un certain nombre de mesures d’atténuation, vous devez accepter de participer, et cette rubrique présente également les modalités de l’utilisation de ces réductions cesser d’utiliser dans.

## <a name="reliability-in-service-bus"></a>Fiabilité dans marché de Service

Il existe plusieurs manières de traiter les problèmes de message et entité et il existe des instructions relatifs à l’utilisation de ces réductions appropriée. Pour mieux comprendre les instructions, vous devez d’abord comprendre que peut échouer dans Service marché. En raison de la conception des systèmes Azure, tous ces problèmes ont tendance à être éphémères. À un niveau élevé, les différentes causes de l’indisponibilité s’affichent comme suit :

-   La limitation d’un système externe dont dépend le Bus des services. La limitation se produit à partir des interactions avec les ressources de stockage et de calcul.

-   Problème pour un système dont dépend le Bus des services. Par exemple, un composant de stockage donné peut rencontrer des problèmes.

-   Échec du Service Bus sur le seul sous-système. Dans ce cas, un nœud de calcul accessible incohérente et devez redémarrer lui-même, à l’origine de toutes les entités qu'il sert à équilibrer à d’autres nœuds. Cela peut provoquer une courte période de traitement des messages lente.

-   Échec du Service Bus dans un centre de données Azure. Il s’agit d’une « panne » pendant laquelle le système est inaccessible pour le nombre de minutes ou quelques heures.

> [AZURE.NOTE] Le terme **stockage** peut entraîner le stockage Azure et SQL Azure.

Service Bus contient un certain nombre de contournement pour ces problèmes. Les sections suivantes décrivent chaque problème et leur minimisation correspondantes.

### <a name="throttling"></a>La limitation

À Service, la limitation permet de gestion des messages conjoint de taux. Chaque nœud Bus des services héberge de nombreuses entités. Chacune de ces entités effectue des demandes de sur le système en termes de processeur, la mémoire, stockage et autres aspects utiles. Quand un de ces facettes détecte l’utilisation qui dépasse les seuils définis, Service Bus pouvez refuser une demande donnée. L’appelant reçoit une [ServerBusyException][] et de tentatives après 10 secondes.

Comme une atténuation, le code doit l’erreur de lecture et s’arrêter les tentatives du message au moins 10 secondes. Dans la mesure où l’erreur peut se produire dans l’ensemble des composants de l’application client, il est prévu que chaque morceau séparément s’exécute la logique de nouvelle tentative. Le code permet de réduire la probabilité de limitée en activant partition sur une file d’attente ou une rubrique.

### <a name="issue-for-an-azure-dependency"></a>Problème pour une dépendance Azure

Autres composants dans Azure peuvent parfois avoir des problèmes de service. Par exemple, quand un système qui utilise Bus des services est mis à niveau, ce système peut rencontrer temporairement fonctionnalités réduites. Pour contourner ces types de problèmes, Service Bus examine régulièrement et mettent en œuvre, réductions. Effets secondaires de ces réductions s’affichent. Par exemple, pour gérer les problèmes temporaires avec stockage, Bus des services met en œuvre un système qui permet les opérations d’envoi de message travailler de manière cohérente à travers. En raison de la nature atténuation, un message envoyé peut prendre jusqu'à 15 minutes s’affiche dans la file d’attente affecté ou d’abonnement et vous êtes prêt pour une opération de réception. En règle générale, la plupart des entités pas ce problème se produit. Toutefois, étant donné le nombre d’entités dans Service marché dans Azure, cette opération est parfois nécessaire pour un petit sous-ensemble de clients Bus des services.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Échec de Bus des services sur un seul sous-système

Avec n’importe quelle application circonstances peuvent entraîner un composant interne du Service Bus incohérences. Lorsque le Service Bus détecte cela, il collecte des données à partir de l’application pour vous aider à diagnostiquer où est passée. Une fois que les données recueillies, l’application est redémarrée lors d’une tentative de revenir à un état cohérent. Le processus est assez rapidement, et les résultats dans une entité soient visibles pour ne pas être disponible pour jusqu'à quelques minutes, bien que classique vers le bas heures sont beaucoup plus courts.

Dans ce cas, l’application cliente génère une exception [System.TimeoutException][] ou [MessagingException][] . Service Bus contient une atténuation pour ce problème dans le formulaire client automatisée de logique de recherche. Une fois la saturation de la période de nouvelle tentative et le message n’est pas remis, vous pouvez Explorer à l’aide d’autres fonctionnalités telles que des [espaces de noms pour][]. Espaces de noms pour disposer d’autres restrictions qui sont décrites dans cet article.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Échec du Service Bus dans un centre de données Azure

La raison pour laquelle plus probable d’une défaillance dans un centre de données Azure est un échec du déploiement mise à niveau de Service Bus ou un système dépendant. Comme la plateforme a été améliorée, la probabilité de ce type de panne diminue. Échec de centre de données peut se produire également pour des raisons qui incluent les éléments suivants :

-   Panne électrique (alimentation et générer power disparaissent).
-   Connectivité (saut internet entre vos clients et Azure).

Dans les deux cas, un incident d’origine naturel ou l’origine du problème. Pour contourner ce problème et vérifiez que vous pouvez toujours envoyer des messages, vous pouvez utiliser les [espaces de noms pour][] activer les messages à être envoyé à un second emplacement tandis que l’emplacement principal redevient correct. Pour plus d’informations, voir [meilleures pratiques pour les applications isolantes contre défaillances Bus des services et des incidents][].

## <a name="paired-namespaces"></a>Espaces de noms appariés

La fonctionnalité [d’espaces de noms pour][] prend en charge les scénarios dans lesquels une entité Bus de Service ou le déploiement au sein d’un centre de données n’est plus disponible. Alors que cet événement se produit rarement, systèmes distribués toujours doivent être préparés à gérer pire des cas. En règle générale, cet événement se produit car certains éléments dont dépend Bus de Service rencontre un problème à court terme. Pour conserver la disponibilité des applications durant une panne, les utilisateurs de Bus des services peuvent utiliser des deux espaces de noms distincts, préférence dans les centres de données distinct pour héberger leurs entités de messagerie. Le reste de cette section utilise la terminologie suivante :

-   Espace de noms principal : l’espace de noms avec lesquels votre application interagit, pour envoyer et recevoir des opérations.

-   Espace de noms secondaire : l’espace de noms qui se comporte comme une sauvegarde pour l’espace de noms principal. Logique de l’application n’interagit avec cet espace de noms.

-   Basculement intervalle : le nombre d’heures pour accepter les échecs normales avant que l’application bascule à partir de l’espace de noms principal vers l’espace de noms secondaire.

Par paires espaces de noms prise en charge *Envoyer la disponibilité*. Envoyer la disponibilité conserve la possibilité d’envoyer des messages. Pour utiliser envoyer disponibilité, votre application doit remplir les conditions suivantes :

1.  Les messages sont reçus uniquement à partir de l’espace de noms principal.

2.  Les messages envoyés à une file d’attente donnée ou rubrique arriver dans le désordre.

3.  Si votre application utilise les sessions, les messages d’une session arriver dans le désordre. Il s’agit d’un saut de fonctionnalités normales de sessions. Cela signifie que votre application utilise des sessions aux messages du groupe logiquement. État de session est conservé uniquement sur l’espace de noms principal.

4.  Messages au sein d’une session arriver dans le désordre. Il s’agit d’un saut de fonctionnalités normales de sessions. Cela signifie que votre application utilise des sessions aux messages du groupe logiquement.

5.  État de session est conservé uniquement sur l’espace de noms principal.

6.  La file d’attente principale peut être en ligne et commencez à accepter les messages avant la file d’attente secondaire fournit tous les messages dans la file d’attente principale.

Les sections suivantes décrivent les API, comment les API sont implémentées et affiche des exemples de code qui utilisent la fonctionnalité. Notez qu’il existe des implications de facturation associées à cette fonctionnalité.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>L’API MessagingFactory.PairNamespaceAsync

La fonctionnalité d’espaces de noms pour inclut la méthode [PairNamespaceAsync][] sur la classe [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Lorsque la tâche est terminée, l’espace de noms le jumelage est également terminé et prêt agir sur les pour n’importe quel [MessageReceiver][], [QueueClient][], ou [TopicClient][] créées avec l’instance [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] est la classe de base pour les différents types de correspondance disponibles avec un objet [MessagingFactory][] . Pour l’instant, la seule classe dérivée est une nommée [SendAvailabilityPairedNamespaceOptions][], qui mettent en œuvre, les exigences de disponibilité envoyer. [SendAvailabilityPairedNamespaceOptions][] possède un ensemble de constructeurs empiler les uns des autres. Prenons le constructeur avec les paramètres de la plupart des, vous pouvez comprendre le comportement des autres constructeurs.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ces paramètres ont la signification suivante :

-   *secondaryNamespaceManager*: une instance [NamespaceManager][] initialisée pour l’espace de noms secondaire la méthode [PairNamespaceAsync][] peut utiliser pour configurer l’espace de noms secondaire. Le Gestionnaire d’espace de noms est utilisé pour obtenir la liste des files d’attente dans l’espace de noms et pour vous assurer que les files d’attente en retard requis existent. Si les files d’attente n’existent pas, ils sont créés. [NamespaceManager][] doit être en mesure de créer un jeton avec la demande de **Gérer** .

-   *messagingFactory*: l’instance [MessagingFactory][] pour l’espace de noms secondaire. L’objet [MessagingFactory][] est utilisé pour envoyer et, si la propriété [EnableSyphon][] est définie sur **true**, recevoir des messages dans les files d’attente en retard.

-   *backlogQueueCount*: le nombre de files d’attente file d’attente à créer. Cette valeur doit être au moins 1. Lorsque vous envoyez des messages à la file d’attente, une de ces files d’attente est sélectionnée de manière aléatoire. Si vous définissez la valeur sur 1, seule file d’attente peut jamais utilisé. Lorsque cela se produit et que la file d’attente une file d’attente génère des erreurs, le client est de ne pas pouvoir essayer une file d’attente autre file d’attente et risquent de ne pas envoyer votre message. Nous vous recommandons de définir cette valeur sur certains plus grande valeur et par défaut la valeur 10. Vous pouvez le modifier à une valeur supérieure ou inférieure en fonction de la quantité de données votre application envoie par jour. Chaque file d’attente en retard peut contenir jusqu'à 5 Go de messages.

-   *failoverInterval*: le nombre d’heures pendant lesquelles vous acceptez les échecs sur l’espace de noms principal avant de basculer une seule entité vers l’espace de noms secondaire. Basculement se produire sur une base entité par entité. Entités dans un espace de noms live fréquemment dans différents nœuds de Bus des services. Une erreur dans une entité n’implique pas une défaillance dans un autre. Vous pouvez définir cette valeur à [System.TimeSpan.Zero][] à basculement vers le secondaire immédiatement après votre échec tout d’abord, non transitoires. Échecs de déclencher le minuteur de basculement sont tout [MessagingException][] dans lequel la propriété [IsTransient][] est faux, ou un [System.TimeoutException][]. Autres exceptions, par exemple [UnauthorizedAccessException][] n’entraînent pas basculement, car ils indiquent que le client est incorrectement configuré. Un [ServerBusyException][] n’entraîne pas basculement car le modèle approprié est peut-être patienter 10 secondes, puis envoyez le message à nouveau.

-   *enableSyphon*: indique que le jumelage particulier soit doit syphon également les messages à partir de l’espace de noms secondaire revenir à l’espace de noms principal. En règle générale, les applications qui envoient des messages doivent définissez cette valeur sur **false**. applications qui reçoivent les messages doivent définir cette valeur **Vrai**. La raison est que fréquemment, il existe des destinataires de messages moins que les expéditeurs de messages. Selon le nombre de destinataires, vous pouvez soit utiliser une instance d’application unique pour gérer les droits syphon. À l’aide de nombreux destinataires implications facturation pour chaque file d’attente en retard.

Pour utiliser le code, créez une instance de [MessagingFactory][] principale, une instance de [MessagingFactory][] secondaire, une instance [NamespaceManager][] secondaire et une instance de [SendAvailabilityPairedNamespaceOptions][] . L’appel peut être aussi simple que ce qui suit :

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Une fois la tâche retournée par la méthode [PairNamespaceAsync][] terminée, tout est configuré et vous êtes prêt à utiliser. Avant de la tâche est renvoyée, il est possible que pas terminé tout le travail d’arrière-plan nécessaire pour le jumelage pour travailler vers la droite. Par conséquent, vous devez commencer pas envoyer des messages jusqu'à ce que la tâche retourne. Cas d’échec, telles que les informations d’identification incorrectes ou Échec de créer les files d’attente en retard, ces exceptions seront levées une fois que la tâche se termine. Une fois que la tâche est retournée, vérifiez que les files d’attente ont été trouvés ou créées en examinant la propriété [BacklogQueueCount][] sur votre instance [SendAvailabilityPairedNamespaceOptions][] . Pour le code précédent, cette opération se présente comme suit :

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base de la messagerie asynchrone dans Service marché, lisez plus d’informations sur les [espaces de noms appariés][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Meilleures pratiques pour isolant applications contre défaillances Bus des services et d’urgence]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [espaces de noms appariés]: service-bus-paired-namespaces.md