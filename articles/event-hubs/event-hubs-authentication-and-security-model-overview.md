<properties 
    pageTitle="Vue d’ensemble du modèle de sécurité et d’authentification Hubs événement | Microsoft Azure"
    description="Événement Hubs d’authentification et de sécurité présentation du modèle."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Événement Hubs d’authentification et de sécurité présentation du modèle

Le modèle de sécurité événement Hubs remplit les conditions suivantes :

- Seuls les périphériques qui présentent des informations d’identification valides peuvent envoyer des données à un concentrateur de l’événement.
- Un périphérique ne peut pas représenter un autre périphérique.
- Un périphérique non fiables peut également être bloqué d’envoyer des données à un concentrateur de l’événement.

## <a name="device-authentication"></a>Authentification de l’appareil

Le modèle de sécurité événement Hubs est basé sur une combinaison de jetons [Signature accès partagé (sa)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) et les *éditeurs d’événements*. Un éditeur d’événements définit un point de terminaison virtuel pour un concentrateur de l’événement. L’éditeur peut uniquement servir à envoyer des messages à un concentrateur de l’événement. Il n’est pas possible de recevoir des messages à partir d’un éditeur.

En règle générale, un concentrateur événement utilise un éditeur par périphérique. Tous les messages sont envoyés à aucun des éditeurs d’un concentrateur événement sont en attente dans ce concentrateur de l’événement. Éditeurs activer le contrôle d’accès précis et la limitation.

Un jeton unique, qui est téléchargé sur l’appareil est affecté à chaque périphérique. Les jetons sont produits tels que chaque jeton unique accorde l’accès à un autre éditeur unique. Un périphérique qui possède un jeton ne peut envoyer à un éditeur, mais aucune autre publisher. Si plusieurs périphériques partagent le même jeton, chacun de ces dispositifs partage un éditeur.

Non recommandé, mais il est possible d’appareils avec des jetons qui accorder un accès direct à un concentrateur événement d’équipement. N’importe quel appareil qui conserve ce jeton peut envoyer des messages directement dans ce concentrateur de l’événement. Un tel dispositif ne sera pas soumis à la limitation. En outre, le périphérique ne peut pas être liste de blocage d’envoyer à ce concentrateur de l’événement.

Tous les jetons sont signés avec une clé associations de sécurité. En règle générale, tous les jetons sont signés avec la même clé. Appareils ne connaissent pas de la clé ; Cela empêche les périphériques de jetons de fabrication.

### <a name="create-the-sas-key"></a>Créer la clé associations de sécurité

Lorsque vous créez un espace de noms événement Hubs, Azure événement Hubs génère une clé de sa 256 bits nommée **RootManageSharedAccessKey**. Ce accorde clés envoyer, écoute et gérer les droits relatifs à l’espace de noms. Vous pouvez créer des touches supplémentaires. Il est recommandé d’obtenu une clé qu’accorde envoyer des autorisations pour le Hub événement spécifique. Pour le reste de cette rubrique, il est supposé que vous avez nommé cette touche `EventHubSendKey`.

L’exemple suivant crée une clé envoyer uniquement lorsque vous créez le Hub de l’événement :

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Générer des jetons

Vous pouvez générer des jetons à l’aide de la touche associations de sécurité. Vous devez produire qu’un seul jeton par périphérique. Jetons peuvent ensuite être présentés à l’aide de la méthode suivante. Tous les jetons sont générés à l’aide de la touche **EventHubSendKey** . Chaque jeton est affectée à un URI unique.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Lorsque vous appelez cette méthode, l’URI doit être spécifié comme `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pour tous les jetons, l’URI est identique à l’exception de `PUBLISHER_NAME`, qui doivent être différent pour chaque jeton. Dans l’idéal, `PUBLISHER_NAME` représente l’ID de l’appareil qui reçoit ce jeton.

Cette méthode génère un jeton avec la structure suivante :

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Le délai d’expiration jeton spécifié dans secondes à partir du 1er janvier 1970. Voici un exemple d’un jeton :

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

En règle générale, les jetons ont une durée de vie qui ressemble à ou dépasse la durée de vie de l’appareil. Si le périphérique possède la fonctionnalité pour obtenir un nouveau jeton, jetons avec une durée de vie plus courte et peuvent être utilisés.

### <a name="devices-sending-data"></a>Envoyer des données de périphériques

Une fois que les jetons ont été créées, chaque périphérique est configuré avec sa propre jeton unique.

Lorsque le périphérique envoie des données à un concentrateur de l’événement, l’appareil balises son jeton avec la demande d’envoi. Pour empêcher un intrus d’écoute et vol le jeton, la communication entre l’appareil et le Hub de l’événement doit avoir lieu sur un canal chiffré.

### <a name="blacklisting-devices"></a>Mise en liste noire appareils

Si un jeton de vol par un intrus, le pirate peut imiter le périphérique dont le jeton a été vol. Mise en liste noire un appareil rend le périphérique inutilisable jusqu'à ce que l’appareil est donné un nouveau jeton qui utilise un éditeur différent.

## <a name="authentication-of-back-end-applications"></a>Authentification des applications du serveur principal

Pour l’authentification des applications du serveur principal qui utilisent les données générées par les périphériques, événement Hubs utilise un modèle de sécurité semblable au modèle qui est utilisé pour les rubriques Bus des services. Un groupe de consommateur événement Hubs équivaut à un abonnement à un sujet de Bus des services. Un client peut créer un groupe consommateurs si la demande pour créer le groupe consommateur est accompagnée un jeton qu’accorde gérer les privilèges pour le Hub de l’événement, ou pour l’espace de noms auquel appartient le Hub de l’événement. Un client est autorisé à utiliser des données d’un groupe consommateurs si la demande de réception est accompagnée d’un jeton qui attribue des droits de réception sur ce groupe consommateur, le Hub de l’événement ou l’espace de noms auquel appartient le Hub de l’événement.

La version actuelle du Bus des services ne prend pas en charge règles associations de sécurité pour les abonnements individuels. La même chose pour les consommateurs Hubs événement. Prise en charge des associations de sécurité est ajouté pour les deux fonctions à l’avenir.

En l’absence d’authentification associations de sécurité des groupes de consommateurs individuels, vous pouvez utiliser les touches associations de sécurité pour sécuriser tous les groupes consommateur avec une clé commune. Cette approche permet à une application utiliser des données depuis n’importe quel des groupes grand public d’un concentrateur de l’événement.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’événement Hubs, consultez les rubriques suivantes :

- [Vue d’ensemble des Hubs événement]
- Une [solution de messagerie la file d’attente] à l’aide de files d’attente Bus des services.
- Un [exemple d’application qui utilise l’événement Hubs]complète.

[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solution de messagerie la file d’attente]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
