<properties 
    pageTitle="1.0 AMQP Guide de protocole Bus des services Azure et événement Hubs | Microsoft Azure" 
    description="Guide de protocole expressions et une description de AMQP 1.0 dans Bus des services Azure et Hubs d’événement" 
    services="service-bus,event-hubs" 
    documentationCenter=".net" 
    authors="clemensv" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="07/01/2016"
    ms.author="clemensv;jotaub;hillaryc;sethm"/>

# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>1.0 AMQP Guide de protocole Bus des services Azure et Hubs d’événement

Advanced Message file d’attente protocole, 1.0 est un protocole cadrage et transfert standard pour asynchrone, en toute sécurité et fiable transfert de messages entre deux parties. Il est le principal protocole de messagerie de Azure Service Bus et Azure événement Hubs. Les deux services prennent également en charge HTTPS. Le protocole SBMP propriétaire qui est également prise en charge est progressivement et privilégie AMQP.

AMQP 1.0 est le résultat de la collaboration secteur qui rassembler des fournisseurs de logiciels intermédiaires, tels que Microsoft et chapeau rouge, entre de nombreux utilisateurs logiciels intermédiaires messagerie tels que JP Morgan Chase représentant le secteur des services financiers. Le forum de normalisation technique pour les spécifications extension et protocole AMQP est OASIS et il a obtenu formelles d’approbation comme une norme internationale en tant que ISO/IEC 19494.

## <a name="goals"></a>Objectifs

Cet article résume les concepts fondamentaux du 1.0 AMQP messagerie spécification ainsi qu’un petit ensemble de caractéristiques d’extension brouillon qui sont actuellement en cours de finalisation au sein du comité technique OASIS AMQP brièvement et explique comment Bus des services Azure mettent en œuvre et s’appuie sur ces caractéristiques.

L’objectif est de n’importe quel développeur à l’aide de n’importe quel pile du client AMQP 1.0 existante sur n’importe quelle plate-forme puissent interagir à Azure Service via AMQP 1.0.

Piles d’objectif général AMQP 1.0 courantes, tels que Apache PROTONS ou AMQP.NET personnelle, implémentent déjà tous les mouvements core AMQP 1.0. Ces mouvements notions de base sont parfois encapsulés avec un niveau plus élevé API ; Apache PROTONS offre même deux, l’API Messenger impérative et réacteur réactive.

Dans la discussion suivante suppose que la gestion des connexions, les sessions et les liens AMQP et la gestion des transferts cadre et contrôle de flux sont gérées par la pile correspondante (par exemple Apache PROTONS-C) et ne nécessitent pas beaucoup le cas échéant attention spécifique des développeurs d’applications. Nous supposons abstraitement l’existence de quelques primitives API comme la possibilité de se connecter et pour créer une forme de *l’expéditeur* et du *destinataire* objets abstraction, qui ont une forme de `send()` et `receive()` opérations, respectivement.

Lorsque vous abordez les fonctions avancées de Azure Bus des services, tels que message navigation ou gestion des sessions, ceux seront expliqués en termes AMQP, mais également comme une mise en œuvre pseudo-paramètres superposée en haut de cette abstraction API supposée.

## <a name="what-is-amqp"></a>Quelles sont les AMQP ?

AMQP est un protocole cadrage et transfert. Cadrage signifie qu’il fournit la structure de flux de données binaires flux dans les deux sens d’une connexion réseau. La structure fournit DELIMITATION pour distincts blocs d’échange de données – cadres – entre les parties connectés. Les fonctionnalités de transfert Assurez-vous que les deux parties la communication peuvent établir comprendre quand les cadres doivent être transférés, et lorsque les transferts sont considérées comme terminées.

Contrairement aux précédemment expiré brouillon versions obtenues par le groupe de travail AMQP qui sont en cours d’utilisation par plusieurs agents de message, du groupe de travail final et standardisé 1.0 AMQP le protocole n’impose pas la présence d’un agent de message ou n’importe quelle topologie particulière pour les entités à l’intérieur d’un agent de message.

Le protocole peut être utilisé pour les communications d’égal à égal symétrique, pour l’interaction avec intermédiaires de message qui prennent en charge des files d’attente et publication/abonnement entités, comme le fait de Bus des services Azure. Il peut également être utilisé pour l’interaction avec l’infrastructure de messagerie où les modèles d’interaction sont différentes de files d’attente réguliers, comme c’est le cas avec Azure événement Hubs. Un concentrateur événement se comporte comme une file d’attente lorsque des événements sont envoyées, mais se comporte comme un service de stockage en série plus lors de la lecture des événements à partir de celui-ci ; Il est un peu similaire à un lecteur de bande. Le client choisit un décalage dans le flux de données disponibles et puis est pris en charge tous les événements à partir de cet offset à disponible au plus tard.

Le protocole AMQP 1.0 est conçu pour être extensible, autorise spécifications améliorer ses fonctionnalités. Les spécifications trois extension que nous aborderons dans ce document illustrent ce principe. Pour communiquer sur infrastructure HTTPS/WebSocket existante où la configuration des ports TCP AMQP natives peut s’avérer difficile, une spécification de liaison définit comment layer AMQP sur WebSocket. Pour interagir avec l’infrastructure de messagerie de manière demande/réponse à des fins de gestion ou pour fournir des fonctionnalités avancées, la spécification AMQP gestion définit les primitives interaction simple requis. Pour l’intégration de modèle d’autorisation fédérés, la spécification de sécurité basée sur des revendications AMQP définit comment associer et renouveler jetons d’autorisations associés aux liens.

## <a name="basic-amqp-scenarios"></a>Scénarios de base AMQP

Cette section décrit l’utilisation de base de AMQP 1.0 à Azure Service, qui inclut la création de connexions, les sessions et les liens et transférer des messages vers et depuis entités Bus des services tels que files d’attente, rubriques et abonnements.

La source plus autoritée pour en savoir plus sur le fonctionnement de AMQP est la spécification AMQP 1.0, mais la spécification écrite pour guider précisément l’implémentation et à ne pas animer le protocole. Cette section se concentre sur présentation autant terminologie nécessaire pour décrire comment Bus des services utilise AMQP 1.0. Pour une présentation plus complète AMQP, ainsi que d’une description plus générale des AMQP 1.0, vous pouvez consulter [ce cours vidéo][].

### <a name="connections-and-sessions"></a>Sessions et connexions

![][1]

AMQP appelle les programmes la communication *conteneurs*; ceux contiennent des *nœuds*, qui sont les entités la communication à l’intérieur de ces conteneurs. Une file d’attente peut être tel nœud. AMQP permet de multiplexage, une seule connexion peut être utilisée pour plusieurs chemins d’accès de communication entre les nœuds ; par exemple, une application cliente peut simultanément recevoir à partir d’une file d’attente et envoyer à une autre file d’attente sur la même connexion réseau.

La connexion réseau est ancrée et donc sur le conteneur. Il est initié par le conteneur dans le rôle de client passage d’une connexion de socket TCP sortante à un conteneur dans le rôle de récepteur qui attend et accepte les connexions entrantes TCP. La liaison de connexion inclut la négociation de la version du protocole, déclarer ou négociation de l’utilisation de la sécurité au niveau de Transport (SSL/TLS) et une négociation d’authentification/autorisation au niveau de la portée de connexion qui est basée sur SASL.

Azure Bus des services nécessite l’utilisation de TLS à tout moment. Il prend en charge les connexions sur le port TCP 5671, selon laquelle la connexion TCP est tout d’abord a-t-il avec TLS avant d’entrer la négociation de protocole AMQP et prend également en charge les connexions sur le port TCP 5672 selon laquelle le serveur immédiatement offre une mise à niveau obligatoire de connexion sur TLS à l’aide du modèle prescrit AMQP. La liaison AMQP WebSocket crée un tunnel via le port TCP 443 qui équivaut puis AMQP 5671 connexions.

Après avoir configuré la connexion et TLS, Bus des services propose deux options de mécanisme SASL :

-   SASL brut est généralement utilisée pour passer des informations d’identification nom d’utilisateur et mot de passe à un serveur. Bus de service n’a pas de comptes, mais nommé de [règles de sécurité d’accès partagé](service-bus-shared-access-signature-authentication.md), qui confier des droits relatifs à l’et sont associé à une clé. Le nom d’une règle est utilisé comme le nom d’utilisateur et la clé (texte codé en base 64) est utilisée comme le mot de passe. Les droits associés à la règle choisie régissent les opérations autorisées sur la connexion.

-   SASL anonyme est utilisé pour ignorer l’autorisation SASL lorsque le client souhaite utiliser le modèle (CBS) sécurité basée sur des revendications qui est décrites ultérieurement. Avec cette option, une connexion client peut être établie anonyme pour une courte durée pendant laquelle le client ne peut interagir avec le point de terminaison CBS et la négociation CBS doit exécuter.

Une fois la connexion de transport établie, les conteneurs déclarer la taille maximale de trame qui sont prêts à gérer, et après le délai d’inactivité ils allez unilatérale déconnecter s’il n’existe aucune activité sur la connexion.

Ils déclarent également le nombre de canaux simultané est prises en charge. Un canal est un chemin d’accès de transfert unidirectionnelle, sortant, virtuel en haut de la connexion. Une session prend un canal à partir de chacune des conteneurs interconnectés pour former un chemin d’accès de la communication bidirectionnelle.

Sessions ont à un modèle de contrôle flux sur fenêtre ; Lorsqu’une session est créée, chaque partie déclare cadres combien il est prêt à accepter dans la fenêtre de réception. En tant que les cadres exchange parties, remplissage de cadres transférés fenêtre et les transferts arrêter lorsque la fenêtre est complète et jusqu'à ce que la fenêtre devient réinitialiser ou développé à l’aide du *flux performative* (*performative* est le terme AMQP des gestes au niveau du protocole échangés entre les deux parties).

Ce modèle basé sur la fenêtre est approximativement analogue à la notion TCP de contrôle de flux, basées sur fenêtre, mais au niveau de la session à l’intérieur du support. Concept du protocole de permettant à plusieurs sessions simultanées existe afin que le trafic haute priorité pourrait être manquer son au-delà de trafic normal accélérée, comme dans un couloir express autoroutes.

Bus de Service d’Azure utilise actuellement qu’une seule session pour chaque connexion. La taille du cadre de maximale de Bus des services est 262 144 octets (256 Ko) pour le Service Bus Standard et Hubs événement. Il est 1 048 576 lignes (1 Mo) pour le Service Bus Premium. Service Bus n’impose pas toutes les fenêtres de limitation de niveau session particuliers, mais réinitialise la fenêtre régulièrement dans le cadre du contrôle de flux au niveau du lien (voir [la section suivante](#links)).

Connexions de canaux et des sessions sont éphémères. Si la connexion sous-jacente est réduit, connexions, tunnel TLS et contexte d’autorisation SASL des sessions doivent être rétablies.

### <a name="links"></a>Liens

![][2]

AMQP transfère les messages sur les liaisons. Un lien est un chemin d’accès de communication créé sur une session qui permet de transfert de messages dans une direction ; la négociation de statut transfert est sur le lien et bidirectionnelle entre les parties connectés.

Liens peuvent être créés par un conteneur à tout moment et sur une session existante, ce qui permet de AMQP différent de nombreux protocoles, y compris HTTP et MQTT, où l’ouverture des transferts et chemin de transfert est un privilège exclus de la partie création de la connexion socket.

Le conteneur qui lance le lien vous invite à indiquer le conteneur opposé pour accepter un lien et il choisit un rôle de l’expéditeur ou du destinataire. Par conséquent, soit conteneur peut initier création unidirectionnelle ou chemins d’accès de la communication bidirectionnelle avec ces derniers modélisation sous forme de paires de liens.

Des liens sont nommées et associés aux nœuds. Comme indiqué dans le début, les nœuds sont les entités la communication à l’intérieur d’un conteneur.

Dans Azure Service marché, un nœud équivaut directement à une file d’attente, un sujet, un abonnement ou une file d’attente lettres mortes sous-adresse d’un abonnement ou file d’attente. Le nom du nœud utilisé dans AMQP est donc le nom de l’entité à l’intérieur de l’espace de noms de Service Bus relatif. Si une file d’attente est appelée **myqueue**, qui est également son nom nœud AMQP. Un abonnement rubrique respecte la convention API HTTP en être triées en une collection de ressources « abonnements » et par conséquent, un abonnement **sub** ou une rubrique **mytopic** contient le nom du nœud AMQP **mytopic/abonnements/sub**.

Le client qui se connecte est également requis pour utiliser un nom de nœud local pour la création de liens ; Bus de service n’est pas précises sur les noms de nœud et pas les considère. Piles de clients AMQP 1.0 généralement utilisent un modèle pour s’assurer que les noms des nœuds éphémères sont uniques dans l’étendue du client.

### <a name="transfers"></a>Transferts

![][3]

Une fois qu’un lien a été établi, les messages peuvent être transférés sur ce lien. Dans AMQP, un transfert est exécuté avec un mouvement de protocole explicite (le *transfert* performative) qui déplace un message à partir de l’expéditeur au destinataire sur un lien. Un transfert est terminé si elle est « réglée, » ce qui signifie que les deux parties ont établie une compréhension commune du résultat de que le transfert.

Dans le cas le plus simple, l’expéditeur pouvez choisir d’envoyer des messages « préalablement réglés », ce qui signifie que le client n’est pas d’informations sur le résultat et le destinataire ne fournira pas les commentaires sur le résultat de l’opération. Ce mode est pris en charge par Bus des services Azure au niveau du protocole AMQP, mais pas présenté dans un de l’API clientes.

Le cas normal est que les messages sont envoyés non réglés, et le destinataire puis indiqueront acceptation ou le rejet à l’aide de la *destruction* performative. Refus se produit lorsque le destinataire ne peut pas accepter le message pour une raison quelconque, le message de refus contient des informations sur la raison pour laquelle, qui est une structure erreur définie par AMQP. Si les messages sont rejetés en raison des erreurs internes à l’intérieur de Bus des services Azure, le service renvoie des informations supplémentaires à l’intérieur de cette structure qui peut être utilisée pour fournir des indications diagnostics du personnel de support si vous utilisez les demandes d’assistance. Vous allez découvrir obtenir plus d’informations sur les erreurs ultérieurement.

Une forme spéciale de refus est l’état *publié* , qui indique que le destinataire ne comporte aucune opposition technique au transfert, mais également aucun intérêt à régler le transfert. Que cas existe, par exemple, si un message est envoyé à un client Bus des services, et le client choisit le message « abandon », car il ne peut pas effectuer le travail qui résulte de traitement du message alors que la remise du message n’est pas en faute. Une variation de cet état est l’état *de modification* , qui permet de modifier le message comme il est lancé. Cet état n’est pas utilisé par Bus des services à l’heure actuelle.

La spécification AMQP 1.0 définit un autre destruction état *reçu* spécifiquement permettant de gérer récupération de lien. Récupération de lien permet de reconstituer l’état d’un lien et en attente de livraison en haut d’une nouvelle connexion et la session, lorsque la connexion antérieure et session ont été perdues.

Azure Bus de Service n’est pas compatible récupération de lien ; Si le client perd la connexion au Service Bus avec un message non réglé transférer en attente, le transfert de messages est perdu et le client doit vous reconnecter, rétablissez la liaison et réessayer le transfert.

Dès lors, Bus des services Azure et événement Hubs prennent en charge « au moins une fois » transferts où est assuré l’expéditeur du message ayant été stockées et accepté, mais elle ne reconnaît pas « une seule fois » transferts au niveau AMQP, où le système serait tente de récupérer le lien et continuez à négociation l’état de remise pour éviter la duplication de transfert du message.

Pour compense doublon possible envoie, Bus des services Azure prend en charge la détection des doublons en tant que fonctionnalité facultative files d’attente et rubriques. Enregistrements en double détection le message d’ID de tous les messages entrants pendant une période définie par l’utilisateur, puis déposez silencieusement tous les messages envoyés avec les mêmes ID de message au cours de cette même fenêtre.

### <a name="flow-control"></a>Contrôle de flux

![][4]

Outre le modèle de contrôle de flux au niveau de la session qui a été abordé précédemment, chaque lien possède son propre modèle de flux de contrôle. Contrôle de flux au niveau de la session protège des devoir gérer trop de cadres en une seule fois, contrôle de flux au niveau du lien met l’application permet de contrôler le nombre de messages qu’il souhaite gérer à partir d’un lien et lorsque le conteneur.

Sur un lien, transferts peuvent se produire uniquement lorsque l’expéditeur est suffisamment « crédit lier ». Lien crédit est un compteur défini par le récepteur à l’aide de *flux* performative, qui est étend à un lien. Lorsqu’et tandis que l’expéditeur est assignée lien crédit, il tentera d’utiliser ce crédit qui envoient des messages. Chaque décrémente de remise de message le lien restant carte bancaire par un. Lorsque le crédit lien est utilisé, livraison arrêter.

Lorsque Bus des services se trouve dans le rôle de récepteur il instantanément fournira l’expéditeur crédit suffisamment lien, afin que les messages peuvent être envoyés immédiatement. Comme lien crédit est utilisé, Bus des services parfois envoie un *flux* performative à l’expéditeur pour mettre à jour le solde de crédit lien.

Dans le rôle expéditeur, Service Bus dynamiquement envoient les messages à utiliser jusqu'à tout crédit lien en suspens.

Un appel « recevoir » au niveau de l’API se traduit par un *flux* performative envoyé à Bus de Service par le client et Bus des services utilisera ce crédit en prenant le premier message déverrouillé, disponible à partir de la file d’attente, verrouillant et transférer. S’il n’existe aucun message disponibles pour la remise, un crédit en souffrance par n’importe quel lien établie avec qu’entité particulière restera enregistrée dans l’ordre de réception et messages sont verrouillés et transférées dès qu’elles sont disponibles pour utiliser un crédit en cours.

Le verrou sur un message est publié lorsque le transfert est réglé dans les états terminaux *accepté*, *rejeté*ou *publié*. Le message est supprimé de Service Bus lorsque l’état terminal est *accepté*. Il reste dans Service marché et est envoyée au destinataire suivant lorsque le transfert atteint un des autres États. Bus des services passe automatiquement le message dans la file d’attente de lettres mortes de l’entité lorsqu’il atteint le nombre de remise maximale autorisé pour l’entité en raison de refus répétés ou les versions.

Même si les API Bus Service officiel n’exposent pas directement une telle option aujourd'hui, un client de protocole de niveau inférieur AMQP pouvez utiliser le modèle de lien crédit pour désactiver l’interaction de style « extraire » de délivrer une unité de crédit pour chaque demande de réception dans un modèle de style « push » en envoyant un grand nombre de crédits lien et recevoir des messages puis dès qu’elles sont disponibles sans aucune action supplémentaire. Push est pris en charge via les paramètres de propriété [MessagingFactory.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.prefetchcount.aspx) ou [MessageReceiver.PrefetchCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.prefetchcount.aspx) . Lorsqu’elles sont différente de zéro, le client AMQP utilise comme le crédit lien.

Dans ce contexte, il est important de comprendre que l’horloge de la date d’expiration du verrou sur le message à l’intérieur de l’entité démarre lorsque le message provient de l’entité, et non lorsque le message est mis sur le réseau. Chaque fois que le client indique est prêt à recevoir des messages à l’aide de lien crédit, il est prévu donc être activement extraient des messages sur le réseau et d’être prêt à les gérer. Dans le cas contraire le verrou message a peut-être expiré avant que le message est remis même. L’utilisation du contrôle de flux de lien crédit doit refléter directement la préparation immédiate de traiter disponibles messages envoyés au destinataire.

En résumé, les sections suivantes fournissent une vue d’ensemble du schéma du flux performative au cours des interactions que API. Chaque section décrit une opération logique différente. Certaines de ces interactions peuvent être « différée », ce qui signifie qu’ils ne peuvent être exécutées qu’auparavant. Création d’un expéditeur du message peut entraîner pas une interaction réseau jusqu'à ce que le premier message est envoyé ou demandé.

Les flèches indiquent le sens de circulation performative.

#### <a name="create-message-receiver"></a>Créer récepteur de Message

| Client                                                                                                                                                | Bus des services                                                                                                                                   |
|---------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------   |
| --> Joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**récepteur**,<br/>source = {nom de l’entité}<br/>cible = {client lien id}<br/>)         | Client est joint au entité comme récepteur                                                                                                         |
| Réponses Bus des services en joignant la fin de la liaison                                                                                                     | <--joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**expéditeur**,<br/>source = {nom de l’entité}<br/>cible = {client lien id}<br/>)       |

#### <a name="create-message-sender"></a>Créer l’expéditeur du Message

| Client                                                                                                            | Bus des services                                                                                                           |
|------------------------------------------------------------------------------------------------------------------ |--------------------------------------------------------------------------------------------------------------------   |
| --> Joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**expéditeur**,<br/>source = {id de lien client,}<br/>cible = {nom de l’entité}<br/>)   | Aucune action                                                                                                                     |
| Aucune action                                                                                                                 | <--joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**récepteur**,<br/>source = {id de lien client,}<br/>cible = {nom de l’entité}<br/>)     |

#### <a name="create-message-sender-error"></a>Créer l’expéditeur du Message (erreur)

| Client                                                                                                            | Bus des services                                                           |
|------------------------------------------------------------------------------------------------------------------ |---------------------------------------------------------------------  |
| --> Joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**expéditeur**,<br/>source = {id de lien client,}<br/>cible = {nom de l’entité}<br/>)   | Aucune action                                                                     |
| Aucune action                                                                                                                 | <--joindre (.)<br/>nom = {nom du lien,}<br/>gérer les = {poignée numérique,}<br/>rôle =**récepteur**,<br/>source = null,<br/>cible = null<br/>)<br/><br/><--détacher (.)<br/>gérer les = {poignée numérique,}<br/>fermé =**true**,<br/>erreur = {informations d’erreur}<br/>)  |

#### <a name="close-message-receiversender"></a>Fermer le Message récepteur/expéditeur

| Client                                            | Bus des services                                       |
|-------------------------------------------------  |-------------------------------------------------  |
| --> Détacher (.)<br/>gérer les = {poignée numérique,}<br/>fermé =**true**<br/>)    | Aucune action                                                 |
| Aucune action                                                 | <--détacher (.)<br/>gérer les = {poignée numérique,}<br/>fermé =**true**<br/>)    |

#### <a name="send-success"></a>Envoyer (succès)

| Client                                                                                                                        | Bus des services                                                                                           |
|------------------------------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------ |
| --> transfert (.)<br/>id de livraison = {poignée numérique,}<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,, plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   | Aucune action                                                                                                     |
| Aucune action                                                                                                                             | <--destruction (.)<br/>rôle = récepteur,<br/>tout d’abord = {id de remise}<br/>dernière = {id de remise}<br/>réglée =**true**,<br/>état =**accepté**<br/>)   |

#### <a name="send-error"></a>Envoyer (erreur)

| Client                                                                                                                        | Bus des services                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------ |-----------------------------------------------------------------------------------------------------------------------------  |
| --> transfert (.)<br/>id de livraison = {poignée numérique,}<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,, plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   | Aucune action                                                                                                                             |
| Aucune action                                                                                                                             | <--destruction (.)<br/>rôle = récepteur,<br/>tout d’abord = {id de remise}<br/>dernière = {id de remise}<br/>réglée =**true**,<br/>état =**rejeté**)<br/>erreur = {informations d’erreur}<br/>)<br/>)     |

#### <a name="receive"></a>Recevoir

| Client                                                                                                | Bus des services                                                                                                                   |
|------------------------------------------------------------------------------------------------------ |------------------------------------------------------------------------------------------------------------------------------ |
| --> flux (.)<br/>lien crédit = 1<br/>)                                                                                 | Aucune action                                                                                                                             |
| Aucune action                                                                                                     | < transférer (.)<br/>id de livraison = {poignée numérique,}<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)     |
| --> destruction (.)<br/>rôle =**récepteur**,<br/>tout d’abord = {id de remise}<br/>dernière = {id de remise}<br/>réglée =**true**,<br/>état =**accepté**<br/>)   | Aucune action                                                                                                                             |

#### <a name="multi-message-receive"></a>Réception des messages à plusieurs

| Client                                                                                                    | Bus des services                                                                                                                       |
|--------------------------------------------------------------------------------------------------------   |--------------------------------------------------------------------------------------------------------------------------------   |
| --> flux (.)<br/>lien crédit = 3<br/>)                                                                                 | Aucune action                                                                                                                                 |
| Aucune action                                                                                                         | < transférer (.)<br/>id de livraison = {poignée numérique,}<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)     |
| Aucune action                                                                                                         | < transférer (.)<br/>id de livraison = {poignée numérique + 1},<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   |
| Aucune action                                                                                                         | < transférer (.)<br/>id de livraison = {poignée numérique + 2},<br/>indicateur de remise = {poignée binaire,}<br/>réglée =**false**,<br/>plus =**false**,<br/>état =**null**,<br/>reprendre =**false**<br/>)   |
| --> destruction (.)<br/>rôle = récepteur,<br/>tout d’abord = {id de remise}<br/>dernière = {id de livraison + 2},<br/>réglée =**true**,<br/>état =**accepté**<br/>)     | Aucune action                                                                                                                                 |

### <a name="messages"></a>Messages

Les sections suivantes décrivent les propriétés des sections message AMQP standards sont utilisées par Bus des services et comment elles sont mappées aux API Bus Service officiel.

#### <a name="header"></a>en-tête

| Nom de champ        | Utilisation                             | Nom de l’API          |
|----------------   |-------------------------------    |---------------    |
| résistant           | -                                 | -                 |
| priorité          | -                                 | -                 |
| durée de vie               | Durée de vie pour ce message     | [Propriété TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)     |
| premier acquéreur    | -                                 | -                 |
| nombre de remise    | -                                 | [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx)   |

#### <a name="properties"></a>propriétés

| Nom de champ            | Utilisation                                                                                                                             | Nom de l’API                                      |
|---------------------- |---------------------------------------------------------------------------------------------------------------------------------  |--------------------------------------------   |
| id de message            | Identificateur défini par l’application, forme libre pour ce message. Utilisé pour la détection des doublons.                                         | [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)                                   |
| id d’utilisateur               | Identificateur utilisateur définies par l’application, ne pas interprété par Bus des services.                                                              | Pas accessible via l’API de Bus de Service.   |
| À                    | Identificateur de destination définie par l’application, ne pas interprété par Bus des services.                                                       | [À](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.to.aspx)                                             |
| Objet               | Identificateur de rôle message défini par l’application, ne pas interprété par Bus des services.                                                   | [Étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)                                       |
| répondre à              | Indicateur de chemin d’accès défini par l’application réponse, ne pas interprété par Bus des services.                                                         | [ReplyTo](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replyto.aspx)                                       |
| id de corrélation        | Identificateur de corrélation définies par l’application, ne pas interprété par Bus des services.                                                       | [ID de corrélation](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.correlationid.aspx)                               |
| type de contenu          | Indicateur de type de contenu défini par l’application pour le corps, ne pas interprété par Bus des services.                                          | [ContentType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx)                                   |
| codage de contenu      | Définies par l’application indicateur codage de contenu pour le corps, ne pas interprété par Bus des services.                                      | Pas accessible via l’API de Bus de Service.   |
| délai d’expiration absolue  | Déclare à quel absolue instantanée le message arrive à expiration. Ignoré en entrée (ttl en-tête est observé), faisant autorité en sortie.   | [ExpiresAtUtc](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.expiresatutc.aspx)                                 |
| heure de création         | Déclare à partir duquel le message a été créé. Pas utilisé par Bus des services                                                           | Pas accessible via l’API de Bus de Service.   |
| id du groupe              | Identificateur défini par l’application pour un ensemble de messages connexes. Utilisés pour les sessions de Bus des services.                                      | [ID de session](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)                                   |
| séquence de groupe        | Compteur identifiant le nombre de séquence relative du message à l’intérieur d’une session. Ignoré par Bus des services.                         | Pas accessible via l’API de Bus de Service.   |
| répondre aux id de groupe     | -                                                                                                                                 | [ReplyToSessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.replytosessionid.aspx)                             |

## <a name="advanced-service-bus-capabilities"></a>Fonctionnalités avancées de Bus des services

Cette section décrit les fonctions avancées de Bus des services Azure basés sur les extensions brouillon AMQP actuellement en cours de développement au sein du comité technique OASIS pour AMQP. Azure Bus des services mettent en œuvre, le dernier état de ces projets et adoptera changements introduits que ces brouillons atteignent statut standard.

> [AZURE.NOTE] Service de messagerie Bus opérations avancées sont prises en charge en quelque sorte un modèle demande/réponse. Les détails de ces opérations sont décrites dans le document [AMQP 1.0 dans Service marché : opérations demande/réponse de](https://msdn.microsoft.com/library/azure/mt727956.aspx).

### <a name="amqp-management"></a>Gestion des AMQP

La spécification AMQP Management est le premier des extensions brouillon que nous aborderons ici. Cette spécification définit un ensemble de gestes de protocole superposée le protocole AMQP qui permettent l’interactions gestion avec l’infrastructure de messagerie sur AMQP. La spécification définit génériques opérations, telles que *créer*, *lire*, *mettre à jour*et *Supprimer* pour la gestion des entités à l’intérieur d’une infrastructure de messagerie et d’un ensemble d’opérations de requête.

Toutes ces entrées tactiles nécessitent une interaction demande/réponse entre le client et l’infrastructure de messagerie, et par conséquent la spécification définit le mode de modèle de ce modèle interaction en haut AMQP : le client se connecte à l’infrastructure de messagerie, débute une session et puis crée une paire de liens. Sur un lien, le client sert de l’expéditeur et sur l’autre qu’il fonctionne comme récepteur, et donc en créant une paire de liens qui peuvent agir en tant qu’un canal bidirectionnelle.

| Opération logique            | Client                      | Bus des services                 |
|------------------------------|-----------------------------|-----------------------------|
| Créer le chemin d’accès de demande de réponse | --> Joindre (.)<br/>nom = {*nom du lien*,}<br/>gérer les = {*poignée numérique*},<br/>rôle =**expéditeur**,<br/>source =**null**,<br/>cible = « myentity / $gestion »<br/>)                            |Aucune action                             |
|Créer le chemin d’accès de demande de réponse                              |Aucune action                             | \<--joindre (.)<br/>nom = {*nom du lien*,}<br/>gérer les = {*poignée numérique*},<br/>rôle =**récepteur**,<br/>source = null,<br/>cible = « myentity »<br/>)                            |
|Créer le chemin d’accès de demande de réponse                              | --> Joindre (.)<br/>nom = {*nom du lien*,}<br/>gérer les = {*poignée numérique*},<br/>rôle =**récepteur**,<br/>source = « gestion myentity / $»,<br/>cible = « myclient$ id »<br/>)                            |                             |Aucune action
|Créer le chemin d’accès de demande de réponse                              |Aucune action                             | \<--joindre (.)<br/>nom = {*nom du lien*,}<br/>gérer les = {*poignée numérique*},<br/>rôle =**expéditeur**,<br/>source = « myentity »<br/>cible = « myclient$ id »<br/>)                            |

Absence de cette paire de liaisons, l’implémentation demande/réponse est simple : une demande est un message envoyé à une entité à l’intérieur de l’infrastructure de messagerie qui prend en charge ce modèle. Dans la mesure où message de demande, le champ *réponse* dans la section *Propriétés* est défini à l’identificateur *cible* du lien sur lequel transférer la réponse. L’entité de gestion traitera la demande et puis diffuser la réponse sur le lien dont l’identificateur de *cible* correspond à l’identificateur indiquée *pour répondre* .

Le modèle requiert visiblement que le conteneur de client et l’identificateur généré par le client pour la destination de réponse sont uniques pour tous les clients et, pour des raisons de sécurité, également difficiles à prévoir.

Les échanges de messages utilisés pour le protocole de gestion ou pour tous les autres protocoles qui utilisent le modèle même se produisent au niveau de l’application ; ils ne définissent pas de nouveaux mouvements au niveau du protocole AMQP. Qui est volontaire afin que les applications peuvent tirer parti immédiatement de ces extensions avec piles AMQP 1.0 compatibles.

Bus des services Azure ne pas actuellement mettre en œuvre toutes les principales fonctionnalités de la spécification de la gestion, mais le modèle de requête/réponse défini par la spécification de la gestion est notions de base pour la fonctionnalité de sécurité basée sur les revendications et pour presque toutes les fonctions avancées, que nous allons étudier dans les sections suivantes.

### <a name="claims-based-authorization"></a>Autorisation basée sur les revendications

Le brouillon spécification AMQP autorisation basée sur les revendications (CBS) s’appuie sur le modèle de demande/réponse de la spécification gestion et décrit un modèle GRG pour savoir comment utiliser les jetons de sécurité fédérée avec AMQP.

Le modèle de sécurité par défaut de AMQP mentionné dans l’introduction est basé sur SASL et s’intègre à la négociation de connexion AMQP. À l’aide de SASL a l’avantage qu’il fournit un modèle extensible pour laquelle un ensemble de mécanismes ont été définies à partir de n’importe quel protocole formelle utilise SASL pouvez bénéficiant. Parmi ces mécanismes sont « Brut » pour le transfert des noms d’utilisateur et mots de passe, « Externes » à lier à la sécurité au niveau TLS, « Anonyme » pour exprimer l’absence d’authentification/autorisation explicite et une grande variété de mécanismes supplémentaires qui permettent de passer d’authentification et/ou des informations d’identification d’autorisation ou des jetons.

L’intégration de AMQP SASL comporte deux inconvénients :

-   Toutes les informations d’identification et des jetons sont adaptées à la connexion. Une infrastructure de messagerie voudrez peut-être contrôler l’accès différenciés sur une base par entité. Par exemple, ce qui permet d’un jeton PORTEUR envoyer dans la file d’attente A mais pas dans la file d’attente B. Avec le contexte d’autorisation ancré sur la connexion, il n’est pas possible d’utiliser une connexion unique et encore utiliser jetons d’accès différentes pour file d’attente A et file d’attente B.

-   Les jetons d’accès sont généralement uniquement valides pendant une période limitée. Cela force l’utilisateur d’acquérir régulièrement jetons à et offre la possibilité de l’émetteur jeton de refuser de délivrer un jeton novateur si les autorisations d’accès de l’utilisateur ont été modifiés. Connexions AMQP peuvent dure très longues périodes de temps. Le modèle SASL fournit uniquement la possibilité de définir un jeton au moment de la connexion, ce qui signifie que l’infrastructure de messagerie soit devra se déconnecter le client lorsque le jeton a expiré ou il a besoin accepter le risque d’autoriser une communication continue avec un client qui dispose des droits d’accès a peut-être été révoqués dans l’intervalle.

La spécification AMQP CBS, implémentée par Bus des services Azure, une solution de contournement élégante pour permettre les deux de ces problèmes : il permet à un client pour associer des jetons d’accès à chaque nœud et mettre à jour ces jetons avant qu’ils n’expirent, sans interrompre le flux des messages.

CBS définit un nœud Gestion virtuel, nommé *$cbs*, doit être fourni par l’infrastructure de messagerie. Le nœud Gestion accepte des jetons au nom de tous les autres nœuds dans l’infrastructure de messagerie.

Le mouvement de protocole est un échange de demande/réponse telle que définie par la spécification de la gestion. Cela signifie que le client établit une paire des liens avec le nœud *$cbs* et puis passe une requête sur le lien sortant et puis attend de la réponse sur le lien entrant.

Le message de demande comporte les propriétés d’application suivantes :

| Clé        | Facultatif | Type de valeur | Contenu de valeur                             |
|------------|----------|------------|--------------------------------------------|
| opération  | N°       | chaîne     | **jeton de place**                                |
| type       | N°       | chaîne     | Le type du jeton placée.            |
| nom       | N°       | chaîne     | Le « public » à laquelle s’applique le jeton. |
| expiration | Oui      | horodatage  | Le délai d’expiration du jeton.              |

La propriété *name* identifie l’entité avec laquelle le jeton doit être associé. Dans Service marché, il est le chemin d’accès à la rubrique/abonnement ou file d’attente. La propriété *type* identifie le type de jeton :

| Type de jeton                      | Description du jeton      | Type de corps           | Notes                                                    |
|---------------------------------|------------------------|---------------------|----------------------------------------------------------|
| amqp:jwt                        | JSON Web jeton (JWT)   | Valeur AMQP (chaîne) | Pas encore disponible.  |
| amqp:SWT                        | Web simple jeton (SWT) | Valeur AMQP (chaîne) | Pris en charge uniquement pour les jetons SWT émis par AAD/ACS          |
| ServiceBus.Windows.NET:sastoken | Jeton de service Bus associations de sécurité  | Valeur AMQP (chaîne) | -                                                        |

Jetons CONFERANT droits. Service Bus sait trois droits fondamentaux : « Envoyer » autorise l’envoi, « Écouter » permet la réception et de « Gérer » manipulation entités. Les jetons SWT émis par AAD/ACS explicitement incluent ces droits en tant que revendications. Jetons Service Bus sa font référence aux règles configurés sur l’espace de noms ou une entité, et ces règles sont configurés avec des droits. Le jeton de signature avec la clé associée à cette règle vérifie et donc le jeton express les droits respectifs. Le jeton associé à une entité à l’aide du *jeton de place* permettra le client connecté pour interagir avec l’entité par les droits jetons. Un lien à l’endroit où le client joue le rôle de *l’expéditeur* nécessite la « envoyer » droite, prenez le rôle de *récepteur* nécessite le droit « Écouter ».

Le message de réponse comporte les valeurs de *Propriétés de l’application* suivante

| Clé                | Facultatif | Type de valeur | Contenu de valeur                    |
|--------------------|----------|------------|-----------------------------------|
| code d’état        | N°       | ent        | Code de réponse HTTP **[RFC2616]**. |
| description de l’état | Oui      | chaîne     | Description de l’état.        |

Le client peut appeler *jeton de placer* à plusieurs reprises et pour n’importe quelle entité dans l’infrastructure de messagerie. Les jetons sont inclus dans l’étendue du client en cours et ancrés sur la connexion actuelle, ce qui signifie que le serveur va supprimer des jetons conservés lorsque la connexion est coupée.

L’implémentation de Service Bus actuelle autorise uniquement CBS conjointement avec la méthode SASL « Anonyme ». Une connexion SSL/TLS doit toujours exister avant la négociation SASL.

Le mécanisme anonyme doit donc être pris en charge par le client AMQP 1.0 choisi. Accès anonyme signifie que la négociation de connexion initiale, y compris la création de la session initiale se produit sans Service Bus savoir qui consiste à créer la connexion.

Une fois la connexion et la session établie, en joignant les liens vers les *$cbs* nœud et envoyer la demande de *jeton de placer* sont les seuls autorisés opérations. Un jeton valide doit être défini correctement à une demande de *jeton de place* pour certains nœud entité dans les 20 secondes une fois la connexion établie, sinon la connexion est perdue unilatérale par Bus des services.

Le client est chargé par la suite pour effectuer le suivi d’expiration du jeton. Lorsqu’un jeton arrive à expiration, Bus des services va supprimer rapidement tous les liens sur la connexion à l’entité correspondante. Pour éviter ce problème, le client peut remplacer le jeton pour le nœud par une nouvelle à tout moment via le nœud de gestion des virtuel *$cbs* avec le même mouvement *jeton de place* et sans compromettre le trafic de charge utile flux sur différents liens.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur AMQP, consultez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques]
- [AMQP dans Service marché pour Windows Server]

[Ce cours vidéo]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp/amqp1.png
[2]: ./media/service-bus-amqp/amqp2.png
[3]: ./media/service-bus-amqp/amqp3.png
[4]: ./media/service-bus-amqp/amqp4.png

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans Service marché pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx