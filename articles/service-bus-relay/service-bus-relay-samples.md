<properties 
    pageTitle="Vue d’ensemble des exemples de relais Bus des services | Microsoft Azure"
    description="Attribuer une catégorie et présente des exemples de relais Bus des services avec des liens vers chacun."
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

# <a name="service-bus-relay-samples"></a>Exemples de relais Bus des services

Les exemples de relais Bus des services illustrent des fonctionnalités clés dans [relais Bus des services](https://azure.microsoft.com/services/service-bus/). Cet article attribuer une catégorie et décrit les exemples disponibles, avec des liens vers chacun.

>[AZURE.NOTE] Exemples de Bus des services ne sont pas installés avec le Kit de développement. Pour obtenir les exemples, visitez la [page des exemples Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Par ailleurs, il est un ensemble d’échantillons de relais Bus des services mis à jour [ici](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (à ce jour, ils ne sont pas décrites dans cet article).  

Pour des exemples de messagerie, voir [exemples d’échanges Bus des services](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Relais Bus des services

Les exemples suivants illustrent comment écrire des applications qui utilisent le service de relais Bus des services.

Notez que les exemples de relais doivent être une chaîne de connexion pour accéder à votre espace de noms Bus des services.

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

## <a name="service-bus-relay"></a>Relais Bus des services

Exemples qui illustrent le relais Bus des services.

### <a name="getting-started"></a>Prise en main

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Relais de messagerie : Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Montre comment exécuter un Bus de Service client et le service sur Azure. Cet exemple configure Bus des services par programme. Seules les informations environnement et de sécurité sont stockées dans les fichiers de configuration.|1.8|Bus des services Microsoft Azure|
|[Authentification de messagerie relayée : Secret partagé](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Montre comment utiliser un nom de l’émetteur et le code secret de l’émetteur s’authentifier à Service.|1.8|Bus des services Microsoft Azure|
|[Relais d’authentification messagerie : WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Montre comment exposer un service HTTP qui ne nécessite pas d’authentification utilisateur du client.|1.8|Bus des services Microsoft Azure|
|[Relais liaisons de messagerie : WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Montre comment utiliser la liaison **WebHttpRelayBinding** pour renvoyer des données binaires à l’aide du modèle de programmation Web.|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : NetTcp relayé](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Montre comment utiliser la liaison **NetTcpRelayBinding** .|1.8|Bus des services Microsoft Azure|

### <a name="exploring-features"></a>Exploration des fonctions

Exemples qui illustrent les différentes fonctionnalités de relais Bus des services.

|Exemple de nom|Description|Version minimale SDK|Disponibilité|
|---|---|---|---|
|[Relayée authentification messagerie : WebToken Simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Montre comment utiliser les informations d’identification web simple jeton s’authentifier à Service. L’exemple est semblable à l’exemple écho, avec quelques modifications. Plus précisément, cet exemple ajoute un comportement dans les applications de ChannelFactory (client) et ServiceHost (service).|1.8|Bus des services Microsoft Azure|
|[Relayés messagerie : Équilibrage de charge](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Montre comment utiliser Microsoft Azure Service Bus pour acheminer les messages vers plusieurs destinataires. Il affiche plusieurs instances d’un service simple communiquer avec un client via la liaison **NetTcpRelayBinding**|1.8|Bus des services Microsoft Azure|
|[Relayés liaisons de messagerie : Événement Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Montre l’utilisation de la liaison de **NetEventRelayBinding** sur Microsoft Azure Service Bus.|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : WS2007Http Session](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Montre l’utilisation de la liaison de **WS2007HttpRelayBinding** avec des sessions fiables activées. Il indique également comment spécifier des informations d’identification Bus de Service dans le fichier de configuration plutôt que par programme.|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Montre comment utiliser la liaison de **WS2007HttpRelayBinding** avec la sécurité des messages pour sécuriser les messages de bout en bout pour tout en nécessitant toujours aux clients s’authentifier à Service.|1.8|Bus des services Microsoft Azure|
|[Relayés messagerie : Échange de métadonnées](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Montre comment exposer un point de terminaison de métadonnées qui utilise la liaison de relais. **MetadataExchange** est pris en charge dans les liaisons de relais suivants : **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**et **WS2007HttpRelayBinding**.|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Comment configurer la liaison **NetTcpRelayBinding** pour prendre en charge le mode de connexion **hybride** qui établit une connexion relayée d’abord et si possible, bascule automatiquement vers une connexion directe entre un client et un service.|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : NetTcp MsgSec nom d’utilisateur](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Montre comment utiliser la liaison de **NetTcpRelayBinding** avec la sécurité des messages.|1.8|Bus des services Microsoft Azure|
|[Relayés liaisons de messagerie : Unidirectionnelle Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Montre comment exposer et à utiliser un point de terminaison du service à l’aide de la liaison **NetOnewayRelayBinding** .|1.8|Bus des services Microsoft Azure|
|[Liaisons messageries relayés : WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Montre l’utilisation de la liaison **WS2007HttpRelayBinding** . Il montre un service simple qui n’utilise aucune option de sécurité et ne nécessite pas aux clients s’authentifier.|1.8|Bus des services Microsoft Azure|

## <a name="next-steps"></a>Étapes suivantes

Consultez les rubriques suivantes pour une vue d’ensemble conceptuelle de Bus des services.

- [Vue d’ensemble de relais Bus des services](service-bus-relay-overview.md)
- [Architecture de Bus de service](../service-bus-messaging/service-bus-architecture.md)
- [Principes de base de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)