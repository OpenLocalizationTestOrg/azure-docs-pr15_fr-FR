<properties 
    pageTitle="Relais Azure hybride connexions protocole | Microsoft Azure"
    description="zure relais hybride connexions protocole Guide."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Protocole de connexions de relais Azure hybride

Relais Azure est un des piliers des fonctionnalités clés de la plateforme Bus des services Azure. Nouvelle fonctionnalité de « Connexions hybride » de relais est une évolution sécurisée, ouvrir protocole HTTP et WebSocket.

Il annule et remplace l’ancienne, également appelée fonctionnalité « Services BizTalk » qui a été créée dans une base de protocole propriétaire. L’intégration des connexions hybride dans Azure application Services continuent à fonctionner comme-est.

« Connexions hybride » permet d’établir une communication bidirectionnelle, binaire flux entre deux applications en réseau, dans laquelle une des deux parties peuvent résider derrière NAT ou un pare-feu.

Ce document décrit les interactions côté client avec le relais hybride connexions pour la connexion des clients aux rôles récepteur et de l’expéditeur et comment récepteurs acceptent de nouvelles connexions.

## <a name="interaction-model"></a>Modèle d’interaction

Le relais de connexions hybride connecte deux parties en fournissant un point de rendez-vous dans le cloud Azure que les deux parties pouvant découvrir et connectez-vous à partir du point de vue de leur propre réseau. Ce point de rendez-vous est appelé « Délai de connexion hybride » dans ce guide et autres documents dans les API, ainsi que dans le portail Azure. Le point de terminaison du service connexions hybride est appelée « service » pour le reste de ce document.

Le modèle d’interaction utilise la nomenclature établie par de nombreuses autres API de mise en réseau :

Il existe un récepteur qui indique tout d’abord est prêt à gérer les connexions entrantes et par la suite les accepte qu’ils arrivent. Sur l’autre côté, il existe un client qui se connecte qui se connecteront le récepteur, digne cette connexion accepté pour établir un chemin d’accès de la communication bidirectionnelle. « Connexion », « Écouter », « Accepter » est les mêmes termes que se trouve sur la plupart des socket API.

N’importe quel modèle de communication relayés comporte deux parties établir des connexions sortantes vers un point de terminaison du service, qui est le « récepteur » également un « client » utilisation langage commun et peuvent également entraîner autres surcharges terminologie ; la terminologie précise que nous utilisons par conséquent, pour les connexions hybride est la suivante :

Les programmes des deux côtés d’une connexion sont appelées « client », dans la mesure où ils sont des clients au service. Le client qui attend et accepte les connexions est le « récepteur » ou considérée comme étant dans le rôle « récepteur ». Le client qui démarre une nouvelle connexion vers un récepteur via le service est appelé « émetteur » ou « du rôle de l’expéditeur ».

## <a name="listener-interactions"></a>Interactions récepteur

Le récepteur inclut quatre interactions avec le service ; tous les détails des fils sont décrites plus loin dans ce document dans la section référence.

### <a name="listen"></a>Écouter

Pour indiquer la préparation du service un récepteur est prêt à accepter les connexions, qu’il crée une connexion de socket web sortante. La liaison de connexion porte le nom d’une connexion hybride définis dans l’espace de noms relais et un jeton de sécurité qui donne l’écoute » » droit sur ce nom. Quand le socket web est accepté par le service, l’enregistrement est terminée et le socket établie web reste actif en tant que le canal « contrôle » pour activer toutes les interactions résultantes. Le service permet jusqu'à 25 récepteurs simultanés sur une connexion hybride. S’il existe récepteurs 2 ou plus actifs, les connexions entrantes seront être équilibrées entre eux dans un ordre aléatoire ; distribution juste n’est pas garantie.

### <a name="accept"></a>Accepter

Chaque fois qu’un expéditeur ouvre une nouvelle connexion sur le service, le service sélectionnez et avertir parmi les récepteurs actives sur la connexion hybride. La notification est envoyée à l’écoute sur le canal de contrôle pour l’ouvrir en tant que message JSON contenant l’URL du point de terminaison socket Web que l’auditeur doit se connecter à pour accepter la connexion.

L’URL peut et doit être utilisée directement par le récepteur sans travail supplémentaire ; les informations au format ne sont plus valides pour un laps de temps, pour l’essentiel pour tant que l’expéditeur est prêt à attendre pour la connexion est établie de bout en bout, mais avec un maximum de 30 secondes. L’URL peut être utilisée uniquement à tentative d’une connexion réussie. Dès que la connexion de socket Web avec l’URL de rendez-vous est établie, toutes les activités supplémentaires sur ce socket Web sont transmises à partir d’et à l’expéditeur, sans intervention ou interprétation par le service.

### <a name="renew"></a>Renouveler 

Le jeton de sécurité qui doit être utilisé pour enregistrer le récepteur et mettre à jour le canal de contrôle peut expirer alors que le récepteur est actif. L’expiration du jeton n’affecte pas les connexions en cours, mais il entraînera le canal de contrôle peuvent être supprimées par le service au ou peu de temps après le moment de l’expiration. Le mouvement « renouveler » est un message JSON que l’auditeur peut envoyer à remplacer le jeton associé au canal de contrôle, de sorte que le canal de contrôle peut être conservé pendant une période prolongée.

### <a name="ping"></a>Ping 

Si le canal de contrôle reste inactif pendant une longue période, intermédiaires sur la manière, par exemple charge programmes d’équilibrage ou NAT peut supprimer la connexion TCP. Le mouvement « ping » qui évite en envoyant une petite quantité de données sur le canal de rappel tous les membres de l’itinéraire réseau que la connexion est destinée à être actif, et il sert également un test d’activité pour que l’auditeur. Si le test ping échoue, le canal de contrôle doit être considérées comme inutilisable et que l’auditeur doit vous reconnecter.

## <a name="sender-interaction"></a>Interaction de l’expéditeur

L’expéditeur comporte uniquement une interaction avec le service, il se connecte.

### <a name="connect"></a>Se connecter

Le mouvement « connecter » ouvre une socket Web sur le service, en fournissant le nom de la connexion hybride et d’une (facultatif, mais requis par défaut) du jeton de sécurité donnant l’autorisation « Envoyer » dans la chaîne de requête. Le service puis interagir avec le récepteur de la manière décrite ci-dessus et que le récepteur crée une connexion de rendez-vous qui est joints avec ce socket Web. Une fois que le socket Web a été accepté, toutes les autres interactions sur le socket Web sera donc un récepteur connecté.

## <a name="interaction-summary"></a>Interaction résumée

Le résultat de ce modèle d’interaction est que le client de l’expéditeur est fourni se déconnecter de la poignée de main avec un socket Web « nettoyer » qui est connecté à un récepteur et qui doit être aucun PRÉAMBULES supplémentaire ou la préparation. Cela permet de pratiquement n’importe quel implémentation de client socket Web existante facilement tirer parti du service connexions hybride uniquement en fournissant une URL correctement conçue dans couche Web socket client.

La connexion de rendez-vous Socket Web qui obtient que l’auditeur via l’interaction accepter est également nettoyer et peut être transmise à toute implémentation du serveur socket Web existante avec certaines abstraction supplémentaire minimale qui fait la distinction entre « accepter » opérations sur les ports d’écoute de leur infrastructure réseau local et les connexions hybride à distance « accepter ».

## <a name="protocol-reference"></a>Référence de protocole

Cette section décrit les détails des interactions protocole décrites ci-dessus.

Toutes les connexions de socket Web sont effectuées sur le port 443 comme une mise à niveau HTTPS 1.1, qui est généralement extraits par certains framework socket Web ou API. La description ici est conservée implémentation indépendant, sans suggérant une infrastructure spécifique.

## <a name="listener-protocol"></a>Protocole récepteur

Le protocole récepteur se compose de deux mouvements de connexion et trois opérations sur les messages.

### <a name="listener-control-channel-connection"></a>Connexion au canal écoute contrôle

Le canal de contrôle est ouvert à la création d’une connexion de socket Web à :

*wss : / / {espace de noms-address} /* *$servicebus* */* *hybridconnection /**{path} ? petite-hc-action =... & petite-hc-id =... & jeton de petite hc =... »*

L' *adresse de l’espace de noms* est le nom de domaine complet de l’espace de noms relais Azure qui héberge la connexion hybride, en général du formulaire {*NomUtilisateur}. servicebus.windows.net.*

Voici les options de paramètre de chaîne de requête

| Paramètre        | Obligatoire ? | Description                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| petite-hc-action | Oui       | Pour que le rôle de récepteur le paramètre doit être **petite-hc-action = écouter**                                                                                                                                |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms codée au format URL de la connexion hybride préconfigurés pour enregistrer ce récepteur sur. Cette expression est ajoutée à la fixe * **$servicebus**/**hybridconnection /*** partie du chemin d’accès. |
| jeton de hc petite  | Oui\*     | Le récepteur doit fournir une valide, codée au format URL Service Bus partagés jeton d’accès pour l’espace de noms ou connexion hybride qui donne le droit **d’écouter** .                                           |
| petite-hc-id     | N°        | Cet identifiant facultatif fournis par le client permet de bout en bout pour le suivi de diagnostic.                                                                                                                             |

Si la connexion Socket Web échoue en raison du chemin d’accès de connexion hybride ne pas enregistré, ou un jeton non valide ou manquant ou une autre erreur, les commentaires de l’erreur seront fournies en utilisant le modèle de commentaires de statut HTTP 1.1 normal. Description de l’état contiendra un suivi-id d’erreur qui peut être communiqué au Support Azure :

| Code | Erreur          | Description                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Introuvable      | Le **chemin d’accès** de connexion hybride n’est pas valide ou l’URL de base est incorrect |
| 401  | Non autorisé   | Le jeton de sécurité est manquant ou incorrect ou non valide                  |
| 403  | Interdit      | Le jeton de sécurité n’est pas valide pour ce chemin d’accès pour cette action          |
| 500  | Erreur interne | Un problème est survenu dans le service                                    |

Si la connexion de socket Web est arrêtée intentionnellement par le service après que qu’il a été initialement configuré, la raison pour cela sera communiqué à l’aide d’un code d’erreur protocole du approprié Web socket ainsi qu’un message d’erreur descriptif qui inclut également un id de suivi. Le service arrêtera pas le canal de contrôle sans rencontrer une condition d’erreur. Tout arrêt correct est client contrôlée.

| État Web | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Le chemin d’accès de connexion hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et par conséquent violation de la stratégie d’autorisation. |
| 1011      | Un problème est survenu à l’intérieur du service.                                           |

### <a name="accept-handshake"></a>Accepter négociation 

La notification accepter est envoyée par le service au récepteur sur le canal contrôle précédemment établie en tant que message JSON dans un bloc de texte socket Web. Il n’existe pas de réponse à ce message.

Le message contient un objet JSON nommé « accepter », qui définit les propriétés suivantes pour le moment :

-   **adresse** – la chaîne d’URL à utiliser pour établir le Socket Web au service pour accepter une connexion entrante.

-   **id** – identificateur unique pour cette connexion. Si l’id a été fourni par le client de l’expéditeur, c’est la valeur de l’expéditeur fournie, sinon il est une valeur générée par le système.

-   **connectHeaders** – tous les en-têtes HTTP qui ont été fournis au point de terminaison relais par l’expéditeur, qui comprend également le protocole WebSocket Sec et les en-têtes Sec-WebSocket-Extensions.

| Accepter de Message                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

L’adresse URL fournie dans le message JSON est utilisé par le récepteur pour établir le Socket Web pour accepter ou rejeter la prise expéditeur.

#### <a name="accepting-the-socket"></a>Acceptation du socket

Pour accepter, que l’auditeur établit une connexion WebSocket à l’adresse fournie.

À l’esprit que pour la période d’aperçu, l’adresse URI peut utiliser une adresse IP sans système d’exploitation et le certificat TLS fourni par le serveur échouera validation sur cette adresse. Cela sera supprimée lors de la visualisation.

Si le message « accepter » comporte un en-tête « Sec-WebSocket-Protocol », il est prévu que le récepteur acceptera uniquement la WebSocket si elle prend en charge ce protocole et qu’il définit l’en-tête de la WebSocket est établie.

Ces conditions s’appliquent à l’en-tête « Sec-WebSocket-Extensions ». Si l’infrastructure prend en charge d’extension, il doit définir l’en-tête pour la réponse du côté *serveur* de la négociation « Sec-WebSocket-Extensions » requise pour l’extension.

L’URL doit être utilisée comme-est pour établir le socket accepter, mais contient les paramètres suivants :

| Paramètre        | Obligatoire ? | Description                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| petite-hc-action | Oui       | Pour accepter un socket le paramètre doit être **petite-hc-action = accepter**                                                                                                                                                                                                                          |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms codée au format URL de la connexion hybride préconfigurés pour enregistrer ce récepteur sur. Cette expression est ajoutée à la fixe * **$servicebus**/**hybridconnection /*** partie du chemin d’accès.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| petite-hc-id | No        | Voir description des **id** .                                                                                                                                                                                                                                                              |

Si une erreur est générée, le service peut répondre comme suit :

| Code | Erreur          | Description                         |
|------|----------------|-------------------------------------|
| 403  | Interdit      | L’URL n’est pas valide.               |
| 500  | Erreur interne | Un problème est survenu dans le service |

Une fois la connexion établie, le serveur s’arrête le socket Web lorsque le socket Web expéditeur arrête, ou avec l’état suivant

| État Web | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Le client de l’expéditeur arrête la connexion                                        |
| 1001      | Le chemin d’accès de connexion hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et par conséquent violation de la stratégie d’autorisation. |
| 1011      | Un problème est survenu à l’intérieur du service.                                           |

#### <a name="rejecting-the-socket"></a>En refusant le socket

En refusant le socket après inspectant le message « accepter » nécessite une négociation similaire de sorte que le code d’état et la description de l’état communiquer le motif du rejet peuvent être transmis à l’expéditeur.

Le choix de conception de protocole ici consiste à utiliser un protocole de transfert WebSocket (qui est conçu pour mettre fin à un état d’erreur défini) afin que les mises en œuvre du client récepteur peuvent continuer à compter sur un client WebSocket et n’avez pas besoin utiliser un supplémentaire, sans système d’exploitation client HTTP.

Pour refuser le socket, le client accepte l’adresse URI à partir du message « accepter » et insère des deux paramètres de chaîne de requête :

| Paramètre             | Obligatoire ? | Description                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Oui       | Code d’état HTTP numérique                |
| statusDescription | Oui       | Raison n ° lisible humaine du rejet |

L’URI qui en résulte est ensuite utilisée pour établir une connexion WebSocket ; là encore, oubliez pas que le certificat TLS ne peut-être pas correspond à l’adresse lors de l’aperçu, afin de validation peut avoir à désactiver.

Lorsqu’il a terminé correctement, cette négociation échouera intentionnellement avec un code d’erreur HTTP 410, dans la mesure où aucun WebSocket n’a été établie. Si une erreur se produit, voici les options :

| Code | Erreur          | Description                         |
|------|----------------|-------------------------------------|
| 403  | Interdit      | L’URL n’est pas valide.               |
| 500  | Erreur interne | Un problème est survenu dans le service |

### <a name="listener-token-renewal"></a>Renouvellement jeton récepteur

Jeton du récepteur est sur le point d’expirer, il peut Remplacez-le en envoyant un message de bloc de texte au service via le canal de contrôle établie. Le message contient un objet JSON nommé « renewToken », qui définit la propriété suivante pour le moment :

-   **jetons** – un valide, codée au format URL Service Bus partagés jeton d’accès de l’espace de noms ou connexion hybride qui donne le droit **d’écouter** .

| renewToken Message                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Si la validation du jeton échoue, accès refusé et service cloud se ferme la websocket canal contrôle avec une erreur, sinon il est sans réponse.

| État Web | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | Le jeton de sécurité a expiré et par conséquent violation de la stratégie d’autorisation. |

<a name="sender-protocol"></a>Protocole expéditeur
---------------

Le protocole expéditeur est similaire au mode d’établissement d’une écoute. L’objectif est plus grande transparence du socket Web de bout en bout. L’adresse pour vous connecter à est identique à celui que l’auditeur, mais diffère de l’action « » et le jeton nécessite une autorisation différents :

*wss : / / {espace de noms-address} /* *$servicebus* */* *hybridconnection /**{path} ? petite-hc-action =... & petite-hc-id =... \[& jeton de hc sbc =... \]*

L' *adresse de l’espace de noms* est le nom de domaine complet de l’espace de noms relais Azure qui héberge la connexion hybride, en général du formulaire {*NomUtilisateur}. servicebus.windows.net.*

La requête peut contenir arbitraires en-têtes HTTP supplémentaires, y compris ceux définis par l’application. Tous les en-têtes fournis au récepteur de flux et vous pouvez trouver sur l’objet « connectHeader » du message contrôle « accepter ».

Voici les options de paramètre de chaîne de requête

| Paramètre        | Obligatoire ? | Description                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| petite-hc-action | Oui       | Pour que le rôle de récepteur le paramètre doit être **action = se connecter**                                                                                                                                                    |
| {path}       | Oui       | Le chemin d’accès de l’espace de noms codée au format URL de la connexion hybride préconfigurés pour enregistrer ce récepteur sur.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| jeton de hc petite | Yes\*     | Le récepteur doit fournir une valide, codée au format URL Service Bus partagés jeton d’accès pour l’espace de noms ou connexion hybride qui donne le droit **d’Envoyer** .                                                            | | petite-hc-id | No        | Un ID facultatif qui permet de bout en bout pour le suivi de diagnostic et est mis à disposition que l’auditeur pendant la négociation accepter.                                                                                       |

Si la connexion Socket Web échoue en raison du chemin d’accès de connexion hybride ne pas enregistré, ou un jeton non valide ou manquant ou une autre erreur, les commentaires de l’erreur seront fournies en utilisant le modèle de commentaires de statut HTTP 1.1 normal. Description de l’état contiendra un suivi-id d’erreur qui peut être communiqué au Support Azure :

| Code | Erreur          | Description                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Introuvable      | Le **chemin d’accès** de connexion hybride n’est pas valide ou l’URL de base est incorrect |
| 401  | Non autorisé   | Le jeton de sécurité est manquant ou incorrect ou non valide                  |
| 403  | Interdit      | Le jeton de sécurité n’est pas valide pour ce chemin d’accès pour cette action          |
| 500  | Erreur interne | Un problème est survenu dans le service                                    |

Si la connexion de socket Web est arrêtée intentionnellement par le service après que qu’il a été initialement configuré, la raison pour cela sera communiqué à l’aide d’un code d’erreur protocole du approprié Web socket ainsi qu’un message d’erreur descriptif qui inclut également un id de suivi.

| État Web | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | Que l’auditeur arrête le socket.                                                 |
| 1001      | Le chemin d’accès de connexion hybride a été supprimé ou désactivé.                           |
| 1008      | Le jeton de sécurité a expiré et par conséquent violation de la stratégie d’autorisation. |
| 1011      | Un problème est survenu à l’intérieur du service.                                           |

## <a name="next-steps"></a>Étapes suivantes :

- [Relais Forum aux questions](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Prise en main .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Prise en main nœud](relay-hybrid-connections-node-get-started.md)