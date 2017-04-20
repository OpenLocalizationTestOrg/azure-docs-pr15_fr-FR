<properties 
    pageTitle="Comment enregistrer des événements dans Azure événement Hubs gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment enregistrer des événements dans Azure événement Hubs gestion des API Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Comment enregistrer des événements dans Azure événement Hubs gestion des API Azure

Azure événement Hubs est un service de pénétration de données extensibles hautement qui peut traiter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données obtenues par vos périphériques connectés et les applications. Événement Hubs sert de la valeur « porte » pour un pipeline des événements, et une fois que les données sont collectées à un concentrateur de l’événement, il peut être transformé et stockées à l’aide de n’importe quel fournisseur analytique en temps réel ou les cartes de stockage/le traitement par lots. Événement Hubs sépare la production d’un flux d’événements à partir de la consommation de ces événements, afin que les consommateurs d’événements peuvent accéder aux événements selon leur propre calendrier.

Cet article est associée à la vidéo [Intégrer Azure API gestion avec Hubs événement](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) et explique comment enregistrer des événements de gestion des API utilisant centraux et Azure événement.

## <a name="create-an-azure-event-hub"></a>Créer un concentrateur événement Azure

Pour créer un nouveau concentrateur événement, se connecter au [portail classique Azure](https://manage.windowsazure.com) , cliquez sur **Nouveau**->**Application Services**->**Service Bus**->**Événement concentrateur**->**Création rapide**. Entrez un nom de l’événement concentrateur, région, sélectionnez un abonnement, puis sélectionnez un espace de noms. Si vous n’avez pas déjà créé un espace de noms, vous pouvez créer un en tapant un nom dans la zone de texte **Namespace** . Une fois que toutes les propriétés sont configurées, cliquez sur **créer un nouveau concentrateur événement** pour créer le Hub de l’événement.

![Créer le hub de l’événement][create-event-hub]

Ensuite, accédez à l’onglet **configurer** pour votre nouveau concentrateur événement et créez deux **stratégies d’accès partagé**. Nommez le premier **envoi** et attribuez-lui les autorisations **Envoyer** .

![Stratégie d’envoi][sending-policy]

Nommez le second **réception**, attribuez-lui les autorisations **écouter** , puis cliquez sur **Enregistrer**.

![Stratégie de réception][receiving-policy]

Chaque stratégie d’accès partagé permet aux applications d’envoyer et recevoir des événements vers et depuis le Hub de l’événement. Pour accéder aux chaînes de connexion pour ces politiques, accédez à l’onglet **tableau de bord** du concentrateur événement, puis cliquez sur **informations de connexion**.

![Chaîne de connexion][event-hub-dashboard]

La chaîne de connexion **d’envoi** est utilisée lors de la journalisation des événements, et la chaîne de connexion **réception** est utilisée lorsque vous téléchargez des événements à partir du Hub de l’événement.

![Chaîne de connexion][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Créer un journal de gestion des API

Maintenant que vous avez un concentrateur de l’événement, l’étape suivante consiste à configurer un [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx) dans votre service de gestion de l’API afin qu’il peut enregistrer des événements dans le Hub de l’événement.

Journaux de gestion des API sont configurés à l’aide de l' [API REST de gestion des API](http://aka.ms/smapi). Avant d’utiliser l’API REST pour la première fois, passez en revue les [conditions préalables](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) et vous assurer que vous avez [activé l’accès à l’API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Pour créer un journal, effectuer une demande HTTP placer à l’aide du modèle d’URL suivant.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Remplacer `{your service}` avec le nom de votre instance de service de gestion de l’API.
-   Remplacer `{new logger name}` avec le nom souhaité pour votre nouvel enregistreur d’événements. Vous allez référencer ce nom lorsque vous configurez la stratégie de [journal-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Ajoutez les en-têtes suivants à la demande.

-   Type de contenu : application/json
-   Autorisation : SharedAccessSignature uid =...
    -   Pour obtenir des instructions sur la génération de la `SharedAccessSignature` voir [Azure API gestion reste API d’authentification](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Spécifiez le corps de la requête en utilisant le modèle suivant.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`doit être définie sur `AzureEventHub`.
-   `description`Fournit une description facultative du journal et peut être une chaîne de longueur nulle si vous le souhaitez.
-   `credentials`contient la `name` et `connectionString` de votre plateforme d’événement Azure.

Lorsque vous effectuez la demande, si le journal est créé un code d’état de `201 Created` est renvoyée. 

>[AZURE.NOTE] Pour d’autres codes de retour possibles et leurs raisons, voir [créer un journal](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Pour voir comment effectuer d’autres opérations, tels que list, mise à jour et supprimer, voir la documentation d’entité [journal](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurer des stratégies de journal-eventhubs

Une fois votre journal est configurée dans la gestion des API, vous pouvez configurer vos stratégies de journal à eventhubs afin de vous connecter les événements souhaités. La stratégie de journal-eventhubs peut être utilisée dans la section stratégie entrante ou la section stratégie sortante.

Pour configurer des stratégies, se connecter au [portail classique Azure](https://manage.windowsazure.com), naviguez votre service de gestion de l’API, puis cliquez sur le **portail publisher** ou **Gérer** pour accéder au portail de publisher.

![Portail de Publisher][publisher-portal]

Cliquez sur **stratégies** dans le menu de gestion de l’API sur la gauche, sélectionnez l’API et le produit de votre choix, puis cliquez sur **Ajouter une stratégie**. Dans cet exemple, nous ajoutons une stratégie à l' **API écho** du produit **illimité** .

![Ajouter une stratégie][add-policy]

Placez votre curseur dans le `inbound` stratégie de section, puis cliquez sur la stratégie de **journal à EventHub** pour insérer la `log-to-eventhub` modèle déclaration de stratégie.

![Éditeur de stratégie][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Remplacer `logger-id` avec le nom du journal Gestion des API que vous avez configuré dans l’étape précédente.

Vous pouvez utiliser toute expression qui renvoie une chaîne comme valeur pour le `log-to-eventhub` élément. Dans cet exemple, une chaîne contenant la date et heure, nom du service, id de la demande, demander l’adresse ip et nom de l’opération est enregistrée.

Cliquez sur **Enregistrer** pour enregistrer la configuration de stratégie mis à jour. Dès qu’il est enregistré la stratégie est active et événements sont enregistrés dans le Hub événement désigné.

## <a name="next-steps"></a>Étapes suivantes

-   En savoir plus sur Azure événement Hubs
    -   [Prise en main Azure événement Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recevoir des messages avec EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Événement Hubs guide de programmation](../event-hubs/event-hubs-programming-guide.md)
-   Pour plus d’informations sur l’intégration de la gestion de l’API et Hubs d’événement
    -   [Référence d’entité journal](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [référence de la stratégie de journal-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Surveiller votre API de gestion des API Azure, concentrateur d’événement et Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Regarder une vidéo procédure pas à pas

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






