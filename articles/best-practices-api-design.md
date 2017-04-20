<properties
   pageTitle="Conseils de conception de l’API | Microsoft Azure"
   description="L’aide lors de la création d’un bien conçue API."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>

# <a name="api-design-guidance"></a>Conseils de conception de l’API

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Vue d’ensemble

De nombreuses solutions basées sur le web modernes Vérifiez l’utilisation des services web, hébergés par des serveurs web, pour fournir des fonctionnalités à distance client applications. Les opérations qu’un service web expose constituent un API web. Un site web bien conçu API doit visent à prendre en charge :

- **Indépendance des plates-formes**. Les applications clientes doivent être en mesure d’utiliser l’API du service web fournit sans exiger la façon dont les données ou les opérations API expose sont implémentées physiquement. Cette fonctionnalité nécessite que l’API respecte des normes communes qui permettent à un service web et une application client d’accord sur les formats de données à utiliser et la structure des données qui sont échangées entre les applications clientes et le service web.

- **Évolution du Service**. Le service web doit être en mesure d’évoluer et ajouter (ou supprimer) fonctionnalité indépendamment des applications clientes. Les applications clientes existantes doivent être en mesure de continuer à fonctionner non modifié comme les fonctionnalités fournies par la modification de service web. Toutes les fonctionnalités doivent également être détectable, afin que les applications clientes peuvent tirer pleinement parti il.

L’objectif de ce guide est pour décrire les problèmes que vous devez prendre en compte lors de la conception d’un site web API.

## <a name="introduction-to-representational-state-transfer-rest"></a>Présentation de transfert état représentation (REST)

Dans son dissertation dans 2000, Roy fielding a proposé une autre approche architecturale pour structurer les opérations exposées par les services web ; RESTE. RESTE est un style d’architecture pour créer des systèmes distribués basés sur hypermédia. Le principal avantage du modèle reste est qu’il est basé sur des normes ouvertes et n’est pas lié l’implémentation du modèle ou les applications clientes qui accèdent à l’implémentation spécifique. Par exemple, un service web REST peut être implémenté à l’aide de l’API Microsoft ASP.NET Web et les applications clientes peuvent être développées à l’aide de n’importe quel langue et un ensemble d’outils qui peut générer des requêtes HTTP et analyser les réponses HTTP.

> [AZURE.NOTE] RESTE est réellement indépendante de n’importe quel protocole sous-jacent et n’est pas nécessairement lié à HTTP. Toutefois, plus courants mises en œuvre des systèmes basés sur reste utilisent HTTP comme le protocole d’application pour envoyer et recevoir des requêtes. Ce document se concentre sur le mappage de principes reste conçus pour fonctionner à l’aide de HTTP.

Le modèle reste utilise un jeu de navigation pour représenter des objets et des services sur un réseau (appelé _ressources_). De nombreux systèmes qui implémentent reste généralement utilisent le protocole HTTP pour transmettre des demandes d’accès à ces ressources. Dans ces systèmes, une application cliente envoie une demande sous la forme d’un URI qui identifie une ressource et d’une méthode HTTP (les plus courants étant GET, POST, place ou suppression) qui indique l’opération à effectuer sur cette ressource.  Le corps de la requête HTTP contient les données nécessaires pour effectuer cette opération. Le point important à comprendre est que reste définit un modèle de requête sans état. Requêtes HTTP doivent être indépendants et peuvent se produire dans n’importe quel ordre, afin d’essayer conserver les informations d’état transitoires entre demandes n’est pas possible.  Est le seul endroit où les informations sont stockées dans les ressources elles-mêmes et chaque demande doit être une opération atomique. En fait, un modèle reste met en œuvre une machine d’état déterminée où une demande de transitions une ressource d’un état non transitoires bien défini à l’autre.

> [AZURE.NOTE] La nature sans état des requêtes individuelles dans le modèle reste permet à un système construit en suivant ces principes pour être hautement scalable. Il n’est pas nécessaire de conserver les affinité entre une application cliente effectue une série de demandes et les serveurs web spécifique gère les demandes.

Un autre point crucial mise en œuvre d’un modèle reste efficace consiste à comprendre les relations entre les diverses ressources permet d’accéder à laquelle le modèle. Ces ressources sont généralement organisées en tant que collections et les relations. Par exemple, supposons qu’une analyse rapide d’un système de commerce électronique indique qu’il existe deux collections de sites dans laquelle les applications sont susceptibles d’être intéressés : commandes et clients. Chaque commande et le client doivent avoir son propre clé unique fins d’identification. URI à accéder à la collection de commandes pourrait être quelque chose de simple _/orders_, et de la même façon URI pour l’extraction de tous les clients pourrait être _/customers_. Émission d’une demande HTTP GET à la _/orders_ URI doit renvoyer une liste de toutes les commandes dans la collection codée sous forme d’une réponse HTTP :

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

La réponse ci-dessous code les commandes en tant qu’une structure de la liste JSON :

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
[{"orderId":1,"orderValue":99.90,"productId":1,"quantity":1},{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2},{"orderId":3,"orderValue":16.60,"productId":2,"quantity":4},{"orderId":4,"orderValue":25.90,"productId":3,"quantity":1},{"orderId":5,"orderValue":99.90,"productId":1,"quantity":1}]
```
Pour récupérer un ordre nécessite de spécifier l’identificateur de la commande à partir de la ressource de _commandes_ , comme _/orders/2_:

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2}
```

> [AZURE.NOTE] Pour simplifier, ces exemples montrent les informations dans les réponses renvoyées sous la forme données texte JSON. Toutefois, il est inutile pourquoi ressources ne doivent pas contenir un autre type de données pris en charge par HTTP, telles que les informations binaires ou chiffrées ; le type de contenu dans la réponse HTTP doit indiquer le type. En outre, un modèle reste peut-être en mesure de retourner les mêmes données dans différents formats, tels que XML ou JSON. Dans ce cas, le service web doit être en mesure d’effectuer négociation du contenu avec le client qui effectue la demande. La demande peut inclure un en-tête _Accepter_ qui spécifie le format par défaut que le client souhaite recevoir et le service web doit essayer de respecter ce format si possible.

Notez que la réponse à une demande reste montre comment utiliser le protocole HTTP standard codes d’état. Par exemple, une requête qui renvoie des données valides doit inclure le code de réponse HTTP 200 (OK), tandis qu’une requête qui ne parvient pas à trouver ou de supprimer une ressource spécifiée doit renvoyer une réponse incluant le code d’état HTTP 404 (introuvable).

## <a name="design-and-structure-of-a-restful-web-api"></a>Conception et la structure d’un site web RESTful API

Les clés de conception d’une API web réussie sont simplicité et cohérence. Une API Web qui présente ces deux facteurs simplifie de créer des applications clientes que vous avez besoin d’utiliser l’API.

Un site web RESTful API se concentre sur affichant un jeu de ressources connectées et fournir les opérations de base qui permettent à une application manipuler ces ressources et naviguer facilement entre elles. Pour cette raison, les URI qui constituent un site web RESTful classique API doivent être orientées vers les données qu’il expose et utiliser les fonctionnalités fournies par HTTP pour agir sur ces données. Cette approche nécessite une vision différente de celle généralement utilisés lorsque vous créez un ensemble de classes dans une API orientée objet qui a tendance à être plus origine par le comportement des objets et des classes. En outre, un site web RESTful API doit être sans état et pas dépendent des opérations appelées dans une séquence particulière. Les sections suivantes synthétisent les points que vous devez prendre en compte lors de la conception d’un site web RESTful API.

### <a name="organizing-the-web-api-around-resources"></a>Organisation de l’API web autour de ressources

> [AZURE.TIP] Les URI exposé par un service web REST doivent être basées sur les noms (les données à laquelle l’API web offre un accès) et pas les verbes (qu’une application peut faire avec les données).

Vous concentrer sur les entités métier qui expose l’API web. Par exemple, dans un site web API conçue pour prendre en charge le système de commerce électronique décrit précédemment, les entités primaires sont clients et commandes. Processus, comme le fait de passer une commande peuvent être obtenus en fournissant une opération HTTP POST qui utilise les informations d’ordre et le place dans la liste des commandes pour le client. En interne, cette opération POST peut effectuer des tâches telles que la vérification des niveaux de stock et la facturation. La réponse HTTP peut indiquer si la commande a été effectuée correctement ou non. Notez également qu’une ressource ne doit pas être basé sur un élément de données physique unique. Par exemple, une ressource d’ordre peut être implémentée en interne à l’aide des informations agrégées de nombreuses lignes réparties sur plusieurs tables dans une base de données relationnel mais présentées au client comme une seule entité.

> [AZURE.TIP] Évitez de conception d’une interface reste qui reflète ou dépend de la structure des données qu’il expose interne. RESTE est plus de mise en œuvre simples opérations CRUD (créer, récupérer, Update, Delete) sur des tables distinctes dans une base de données relationnelle. L’objectif de repos consiste à mapper les entités métier et les opérations qu’une application peut effectuer sur ces entités à l’implémentation physique de ces entités, mais un client ne doit pas être exposé à ces détails physiques.

Entités métier individuelles rarement existent dans isolement (même si certains objets singleton peuvent exister), mais plutôt tendent à être regroupés en collections de sites. Restante termes, chaque entité et chaque collection de sites sont des ressources. Dans une API web RESTful, chaque collection possède son propre URI au sein du service web et effectuer une demande HTTP GET sur un URI pour une collection récupère une liste d’éléments dans cette collection. Chaque élément est également équipé de son propre URI et une application peut soumettre une autre demande HTTP GET à l’aide de ce URI pour récupérer les détails de l’élément. Vous devez organiser les URI des collections de sites et des éléments dans une structure hiérarchique. Dans le système de commerce électronique, les URI _/customers_ indique la collection de sites du client et _/customers/5_ récupère les détails du client unique avec l’ID 5 de cette collection. Cette approche permet à garder à l’API web intuitive.

> [AZURE.TIP] Arrêter une convention d’appellation cohérente dans les URI ; en règle générale, il faut utilisez des noms plurielles pour URI que collections de référence.

Vous devez également prendre en considération les relations entre les différents types de ressources et comment vous pouvez exposer ces associations. Par exemple, clients placent des zéro ou plusieurs commandes. Un moyen naturel pour représenter cette relation serait via un URI comme _/customers/5/orders_ pour rechercher toutes les commandes de client 5. Vous pouvez également envisager représentant l’association d’une commande à un client donné via un URI comme _/orders/99/customer_ pour rechercher le client de commande 99, mais extension trop éloigné ce modèle peut devenir difficile à mettre en œuvre. Une meilleure solution consiste à fournir des liens de navigation vers des ressources, tels que le client, dans le corps du message de réponse HTTP retourné lorsque l’ordre est interrogé associées. Ce mécanisme est décrit plus en détail dans la section à l’aide de l’approche HATEOAS pour activer la Navigation à ressources connexes plus loin dans ce guide.

Systèmes plus complexes peut être de nombreux autres types d’entité, et il peut être tentées par les solutions pour fournir URI qui permettent à une application cliente naviguer parmi plusieurs niveaux de relations, tels que _/customers/1/orders/99/products_ pour obtenir la liste de produits dans l’ordre 99 passées par client 1. Toutefois, ce niveau de complexité peut être difficile de tenir à jour et fixe si les relations entre les ressources modifier ultérieurement. Au lieu de cela, vous devez chercher à conserver URI relativement simple. N’oubliez pas qu’une fois qu’une application a une référence à une ressource, il doit être possible d’utiliser cette référence pour rechercher des éléments associés à cette ressource. La requête précédente peut être remplacée par le URI _/customers/1/orders_ pour rechercher toutes les commandes pour le client 1 et puis interroger le URI _/orders/99/products_ pour trouver les produits dans l’ordre suivant (en supposant que 99 de commande par le client 1).

> [AZURE.TIP] Évitez d’exiger plus complexe que _collection/élément/collection_URI de ressource.

Un autre point à prendre en considération est que toutes les requêtes web imposent une charge sur le serveur web, et plus le nombre de requêtes plus la charge. Vous devez tenter de définir vos ressources afin d’éviter web « amené » API qui exposent un grand nombre de ressources petites. Une telle API peut-être nécessiter une application cliente à envoyer des demandes de plusieurs pour rechercher toutes les données dont il a besoin. Il peut être utile dénormaliser données et combiner des informations en plus grands ressources qui peuvent être récupérés en envoyant une requête unique. Toutefois, vous devez équilibrer cette approche par rapport à la charge de l’extraction des données qui ne peuvent pas être fréquemment requises par le client. Récupération des objets de grande taille, vous pouvez augmenter la latence d’une demande et entraîner des coûts de bande passante supplémentaire pour se démarquer peu si les données supplémentaires ne sont pas souvent utilisées.

Évitez d’introduire des dépendances entre l’API web à la structure, le type ou l’emplacement des sources de données sous-jacentes. Par exemple, si vos données sont trouve dans une base de données relationnel, l’API web n’a pas besoin exposer chaque table dans une collection de ressources. Du site web API doit être considéré comme une abstraction de la base de données et si nécessaire introduire une couche de mappage entre la base de données et de l’API web. De cette façon, si la conception ou l’implémentation de la base de données change (par exemple, vous déplacez à partir d’une base de données relationnelle qui contient une collection de tables centrée réduites à un système de stockage NoSQL dénormalisé comme une base de données de document) des applications clientes sont soustraites ces modifications.
> [AZURE.TIP] La source de données indispensables une API web ne doit pas être un magasin de données ; Cela peut s’expliquer un autre service ou application métier ou même d’anciennes applications en cours d’exécution en local au sein d’une organisation.

Enfin, il peut ne pas être possible de mapper chaque opération implémentée par un site web API à une ressource spécifique. Vous pouvez gérer ces scénarios _pas d’une ressource_ via les demandes HTTP GET qui appeler une partie des fonctionnalités et renvoyant les résultats sous forme d’un message de réponse HTTP. Un site web API qui mettent en œuvre, opérations de style de calculatrice simple tel qu’addition et soustraction peut fournir URI qui exposent ces opérations en tant que ressources pseudo et utiliser la chaîne de requête pour spécifier les paramètres requis. Par exemple une requête GET à l’URI _/add?operand1 = 99 & operand2 = 1_ peut renvoyer un message de réponse avec le corps contenant la valeur 100 et obtenez demande à l’URI _/subtract?operand1 = 50 & operand2 = 20_ peut renvoyer un message de réponse avec le corps contenant la valeur 30. Toutefois, utiliser uniquement ces formulaires d’URI avec parcimonie.

### <a name="defining-operations-in-terms-of-http-methods"></a>Définition des opérations en termes de méthodes HTTP

Le protocole HTTP définit un certain nombre de méthodes affecter la signification sémantique à une demande. Méthodes HTTP fréquemment utilisées par web plus RESTful API sont :

- **Obtenir**, pour récupérer une copie de la ressource à l’URI spécifié. Le corps du message de réponse contient les détails de la ressource demandée.

- **Billet**, pour créer une nouvelle ressource à l’URI spécifié. Le corps du message de demande fournit les informations de la nouvelle ressource. Notez que billet peut également être utilisé pour déclencher opérations qui ne créez pas réellement de ressources.

- **Placer**, remplacer ou mettre à jour de la ressource à l’URI spécifié. Le corps du message de demande spécifie la ressource doit être modifié et les valeurs à appliquer.

- **Supprimer**, pour supprimer la ressource à l’URI spécifié.

> [AZURE.NOTE] Le protocole HTTP définit également d’autres méthodes moins fréquemment utilisées, telles que correctif qui permet de demander des mises à jour sélectifs à une ressource, qui est utilisé pour demander une description d’une ressource, les OPTIONS qui permet d’obtenir des informations sur les options de communication pris en charge par le serveur, un client des informations d’en-tête et qui permet à un client pour demander des informations qu’il peut utiliser fins de test et les diagnostics de suivi.

L’effet d’une requête spécifique doit dépendre que la ressource à laquelle il est appliqué est une collection de sites ou un élément individuel. Le tableau suivant résume les conventions courantes adoptées par des mises en œuvre plus RESTful à l’aide de l’exemple de commerce. Notez que certaines de ces demandes peuvent être implémentées ; tout dépend du scénario spécifique.

| **Ressource** | **Publier** | **Télécharger** | **PLACER** | **SUPPRIMER** |
|--------------|----------|---------|---------|------------|
| /Customers. | Créer un nouveau client | Récupérer tous les clients | Mise à jour en bloc des clients (_si implémenté_) | Supprimer tous les clients |
| / clients/1 | Erreur | Récupérer les détails pour le client 1 | Mettre à jour des détails de client 1 s’il existe, sinon retour une erreur | Supprimer le client 1 |
| /Customers/1/Orders | Créer un nouvel emplacement pour le client 1 | Extrait toutes les commandes pour le client 1 | Mettre à jour des commandes pour le client 1 (_si implémenté_) | Supprimer toutes les commandes pour le client 1 (_si implémenté_) |

L’objectif de requêtes GET et supprimer sont relativement simples, mais il est étendue pour toute confusion concernant l’objectif et les effets de demandes de publication et place.

Une requête POST doit créer une nouvelle ressource avec des données fournies dans le corps de la demande. Dans le modèle reste, vous appliquez fréquemment demandes POST aux ressources qui sont des collections de sites ; la nouvelle ressource est ajoutée à la collection de sites.

> [AZURE.NOTE] Vous pouvez également définir des requêtes POST qui déclencher certaines fonctionnalités (et qui ne retournent nécessairement de données), et ces types de requête peuvent être appliquées aux collections. Par exemple, vous pouvez utiliser une requête POST pour passer d’une feuille de temps à un service de traitement de paie et revenir les taxes calculées en tant que réponse.

Une demande de placer est destinée à modifier une ressource existante. Si la ressource spécifiée n’existe pas, la demande place peut renvoyer un message d’erreur (dans certains cas, il risquent de créer la ressource). Demandes de placement sont plus fréquemment appliquées aux ressources qui sont des éléments individuels (par exemple, un client donné ou la commande), même si elles peuvent être appliquées aux collections, même si cela est moins fréquemment implémenté. Notez que les demandes de placer sont idempotents tandis que les requêtes POST ne sont pas ; Si une application soumet le même placer demande plusieurs fois les résultats doivent toujours être la même (la même ressource est modifiée avec les mêmes valeurs), mais si une application répète la même requête POST le résultat sera la création de plusieurs ressources.

> [AZURE.NOTE] En principe, une demande HTTP placer remplace une ressource existante par la ressource spécifiée dans le corps de la demande. Si l’intention est de modifier une sélection de propriétés dans une ressource tout en conservant les autres propriétés telles quelles, cela doit être implémentée à l’aide d’une demande HTTP correctif. Toutefois, nombreuses mises en œuvre RESTful alléger cette règle et utilisent place dans les deux cas.

### <a name="processing-http-requests"></a>Traitement des demandes HTTP
Les données incluses par une application cliente de nombreuses requêtes HTTP et les messages de réponse correspondant à partir du serveur web, pourraient être présentées dans une variété de formats (ou types de médias). Par exemple, les données qui spécifiant les détails pour un client ou une commande peuvent être fournies sous forme de XML, JSON ou un autre format codé et compressé. Un site web RESTful API doit prendre en charge différents types de supports comme demandé par l’application cliente qui envoie une demande.

Lorsqu’une application cliente envoie une requête qui renvoie des données dans le corps d’un message, elle peut spécifier les types de médias que pouvoir prendre en charge dans l’en-tête accepter de la demande. Le code suivant montre une demande HTTP GET qui Récupère les détails du client 1 et demande le résultat à retourner en tant que JSON (le client doit toujours examiner le type de média des données dans la réponse à vérifier le format des données retournées) :

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

Si le serveur web prend en charge ce type de média, il peut répondre avec une réponse qui inclut l’en-tête de Type de contenu qui spécifie le format des données dans le corps du message :

> [AZURE.NOTE] Pour une interopérabilité maximale, le support types référencées dans les en-têtes accepter et Type de contenu doivent être reconnu types MIME plutôt que de taper un support personnalisé.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

Si le serveur web ne prend pas en charge le type de média requis, il peut envoyer les données dans un format différent. DANS tous les cas, il doit spécifier le type de média (par exemple, _application/json_) dans l’en-tête de Type de contenu. Il est la responsabilité de l’application cliente pour analyser le message de réponse et interpréter les résultats dans le corps du message de manière appropriée.

Notez que dans cet exemple, le serveur web correctement extrait les données demandées et indique la réussite en passant en un code d’état de 200 dans l’en-tête de réponse. Si aucune donnée n’est trouvée, elle doit renvoyer à la place d’un code d’état de 404 (non trouvé) et le corps du message de réponse peut contenir des informations supplémentaires. Le format de ces informations est spécifié par l’en-tête de Type de contenu, comme le montre l’exemple suivant :

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

Commande 222 n’existe pas, afin que le message de réponse ressemble à ceci :

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"message":"No such order"}
```

Lorsqu’une application envoie une demande HTTP mettre à jour une ressource, il spécifie l’URI de la ressource et fournit les données à modifier dans le corps du message de demande. Il doit également spécifier le format de ces données à l’aide de l’en-tête de Type de contenu. Un format courant utilisé pour plus d’informations textuel est _application/x-www-form-urlencoded_, qui comprend un ensemble de paires nom/valeur séparées par le caractère &. L’exemple suivant montre une demande HTTP placer qui modifie les informations de la commande 1 :

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

Si la modification est réussie, elle doit répondre parfaitement avec un code d’état HTTP 204, indiquant que le processus a été correctement géré, mais que le corps de réponse ne contient aucune information supplémentaire. L’en-tête d’emplacement dans la réponse contient l’URI de la ressource nouvellement mis à jour :

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [AZURE.TIP] Si les données dans un message de demande HTTP placer incluent des informations de date et heure, assurez-vous que votre service web accepte les dates et heures au format suivant la norme ISO 8601.

Si la ressource doit être mis à jour n’existe pas, le serveur web peut répondre avec une réponse introuvable comme décrit plus haut. Par ailleurs, si le serveur crée en fait l’objet lui-même, il peut retourner l’état codes HTTP 200 (OK) ou HTTP 201 (créé) et le corps de réponse peuvent contenir les données pour la nouvelle ressource. Si l’en-tête de Type de contenu de la demande spécifie un format de données que le serveur web ne peuvent pas gérer, elle doit répondre avec un code d’état HTTP 415 (Type de média non pris en charge).

> [AZURE.TIP] Envisagez d’implémenter des opérations de place HTTP en bloc qui peuvent lot mises à jour à plusieurs ressources dans une collection de sites. La demande place doit spécifier l’URI de la collection de sites, et le corps de la requête doit définir les détails des ressources à modifier. Cette approche peut vous aider à réduire la longueur et améliorer les performances.

Le format d’une demande HTTP POST que crée de nouvelles ressources sont semblables à ceux de placer les requêtes. le corps du message contient les détails de la nouvelle ressource à ajouter. Toutefois, l’URI spécifie généralement la collection de sites à laquelle la ressource doit être ajoutée. L’exemple suivant crée un nouvel emplacement et l’ajoute à la collection de commandes :

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
productID=5&quantity=15&orderValue=400
```

Si la demande réussit, le serveur web doit répondre avec un code de message avec un code d’état HTTP 201 (créé). L’en-tête d’emplacement doit contenir l’URI de la ressource nouvellement créée, et le corps de la réponse doit contenir une copie de la nouvelle ressource ; l’en-tête de Type de contenu spécifie le format de ces données :

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":99,"productID":5,"quantity":15,"orderValue":400}
```

> [AZURE.TIP] Si les données fournies par une requête place ou POST ne sont pas valides, le serveur web doit répondre par un message avec un code d’état HTTP 400 (demande incorrecte). Le corps de ce message peut contenir des informations supplémentaires sur le problème avec la demande et les formats prévus ou elle peut contenir un lien vers une URL qui fournit davantage de détails.

Pour supprimer une ressource, une demande HTTP DELETE fournit simplement l’URI de la ressource à supprimer. L’exemple suivant tente de supprimer ordre 99 :

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

Si l’opération de suppression réussit, le serveur web doit répondre avec un code d’état HTTP 204, indiquant que le processus a été correctement géré, mais que le corps de réponse ne contient aucune information supplémentaire (cela est la même réponse renvoyée par une opération de placer réussie, mais sans en-tête emplacement en tant que la ressource n’existe plus.) Il est également possible d’une requête Suppression renvoyer le code d’état HTTP 200 (OK) ou 202 (accepté) si la suppression est exécutée de façon asynchrone.

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

Si la ressource est introuvable, le serveur web doit renvoyer un message 404 (introuvable).

> [AZURE.TIP] Si toutes les ressources dans une collection de sites doivent être supprimées, activer une demande HTTP DELETE être indiquée pour l’URI de la collection plutôt que d’obliger l’application à supprimer de chaque ressource à son tour à partir de la collection de sites.

### <a name="filtering-and-paginating-data"></a>Le filtrage et la pagination des données

Vous devez efforce de conserver les URI simple et intuitive. Une collection de ressources via un seul URI assiste à ce sujet, mais il peut affecter les applications l’extraction de grandes quantités de données lorsque uniquement un sous-ensemble d’informations est requis. Générer un grand volume de trafic affecte non seulement les performances et extensibilité élevées du serveur web, mais également affecter la réactivité de l’aide des données des applications clientes.

Par exemple, si les commandes contiennent le prix d’achat de la commande, une application cliente qui doit récupérer toutes les commandes qui ont un coût sur une valeur spécifique devrez récupérer toutes les commandes de la _/orders_ URI et puis filtrer ces commandes localement. Clairement ce processus s’avère très inefficace ; Il fait perdre power de traitement et la bande passante réseau sur le serveur qui héberge l’API web.

Une solution peut être à fournir un modèle URI comme _/orders/ordervalue_greater_than_n_ où _n_ est le prix de la commande, mais pour tous, mais un nombre limité de prix une telle approche n’est pas pratique. En outre, si vous devez requête commandes basées sur d’autres critères, vous pouvez vous retrouver tenu de fournir une longue liste d’URI des noms éventuellement non intuitive.

Une meilleure stratégie de filtrage des données est de proposer des critères de filtre dans la chaîne de requête est passé sur le web API, telles que _/orders?ordervaluethreshold = n_. Dans cet exemple, l’opération correspondante dans le site web API est responsable de l’analyse et la gestion de la `ordervaluethreshold` paramètre dans la chaîne de requête et retourner les résultats filtrés dans la réponse HTTP.

Certaines demandes HTTP GET simples sur les ressources de la collection de sites peuvent très bien renvoyer un grand nombre d’éléments. Pour combattre la possibilité de ce qui se passe vous devez concevoir le web API pour limiter la quantité de données renvoyées par une requête unique. Vous pouvez y parvenir en prenant en charge des chaînes de requête qui permettent à l’utilisateur spécifier le nombre maximal d’éléments à récupérer (qui pourrait lui-même être soumis à une limite supérieures à protéger contre les attaques de refus de Service) et un décalage de départ dans la collection. Par exemple, la chaîne de requête dans l’URI _/orders?limit = 25 & offset = 50_ doit récupérer 25 commandes commençant par la commande 50e figure dans la collection de commandes. Comme avec filtrage des données, l’opération qui mettent en œuvre, la requête GET dans le site web API est responsable de l’analyse et la gestion de la `limit` et `offset` paramètres de la chaîne de requête. Pour vous aider les applications clientes, obtenez demandes qui renvoient des données paginées doivent également inclure des métadonnées qui indiquent le nombre total de ressources disponibles dans la collection. Vous pouvez également envisager d’autres stratégies de pagination intelligent ; Pour plus d’informations, voir [API Design Notes : pagination Smart](http://bizcoder.com/api-design-notes-smart-paging)

Vous pouvez suivre une stratégie similaire pour trier les données qu’elle est extraite ; Vous pouvez fournir un paramètre de tri qui prend un nom de champ en tant que valeur, tel que _/orders?sort = ProductID_. Toutefois, notez que cette approche peut avoir un inconvénient pour la mise en cache (requête paramètres de chaîne font partie de l’identificateur de ressource utilisé par les nombreuses mises en œuvre cache comme clé aux données mises en cache).

Vous pouvez étendre cette approche pour limiter (projet) les champs retournée si un élément de ressource unique contient une grande quantité de données. Par exemple, vous pouvez utiliser un paramètre de chaîne de requête qui accepte une liste délimitée par des virgules des champs, tel que _/orders?fields = Réf produit, quantité_.

> [AZURE.TIP] Donnez à tous les paramètres facultatifs dans les chaînes de requête par défaut significatif. Par exemple, définir la `limit` paramètre à 10 et la `offset` paramètre 0 si vous implémentez la pagination, définissez le paramètre de tri sur la clé de la ressource si vous implémentez le classement et définir le `fields` paramètre à tous les champs de la ressource si vous prenez en charge les prévisions.

### <a name="handling-large-binary-resources"></a>Gestion des ressources binaires volumineux

Une seule ressource peut contenir des champs binaires de grande taille, tels que des fichiers ou des images. Pour éviter la transmission des problèmes provoquée par connexions non fiables et intermittentes et pour améliorer la réactivité, envisagez de fournir des opérations qui permettent à ces ressources à récupérer sous forme de segments par l’application cliente. Pour ce faire, l’API web doit prend en charge l’en-tête accepter plages pour les requêtes GET pour les ressources volumineux et préférence implémenter demandes de tête HTTP pour ces ressources. L’en-tête accepter plages indique que l’opération GET prend en charge les résultats partielles, et qu’une application cliente peut envoyer des demandes GET qui renvoient un sous-ensemble d’une ressource spécifiée comme une plage d’octets. Une demande de tête est similaire à une requête GET à ceci près qu’elle retourne uniquement un en-tête qui décrit la ressource et un corps de message vide. Une application cliente peut émettre une demande de tête pour déterminer s’il faut extraire une ressource à l’aide de requêtes GET partielles. L’exemple suivant montre une demande de tête qui obtient les informations relatives à une image de produit :

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
...
```

Le message de réponse contient un en-tête qui inclut la taille de la ressource (4580 octets) et l’en-tête de plages d’accepter que l’opération GET correspondante prend en charge les résultats partielles :

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

L’application cliente peut utiliser ces informations pour créer une série d’opérations GET pour extraire l’image par petits morceaux. La première requête extrait les premiers octets 2500 à l’aide de l’en-tête de plage :

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
...
```

Le message de réponse indique qu’il s’agit d’une réponse partielle en renvoyant le code d’état HTTP 206. L’en-tête de longueur du contenu spécifie le nombre réel d’octets renvoyés dans le corps du message (et non la taille de la ressource) et l’en-tête de la plage de contenu indique quelle partie de la ressource il s’agit (octets 0-2 499 d’absence du 4580) :

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

Une requête suivante à partir de l’application cliente peut extraire le reste de la ressource à l’aide d’un en-tête de plage approprié :

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=2500-
...
```

Le message de résultat correspondant doit ressembler à ceci :

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## <a name="using-the-hateoas-approach-to-enable-navigation-to-related-resources"></a>À l’aide de l’approche HATEOAS pour activer la navigation vers d’autres ressources

Un des principale motivations reste est qu’il doit être possible de naviguer dans l’ensemble des ressources sans connaître avant du modèle URI. Chaque demande HTTP GET doit retourner les informations nécessaires pour trouver les ressources liées directement à l’objet demandé par des liens hypertexte inclus dans la réponse, et il doit également être apportée aux informations qui décrit les opérations disponibles sur chacune de ces ressources. Ce principe est appelé HATEOAS ou Hypertext en tant que le moteur d’état de l’Application. Le système est effectivement une machine d’état déterminée, et la réponse à chaque demande contient les informations nécessaires pour passer d’un état à un autre ; Aucune information n’est nécessaire.

> [AZURE.NOTE] Il n’existe actuellement aucune normes ou spécifications qui définissent comment le principe HATEOAS du modèle. Les exemples présentés dans cette section illustrent une solution possible.

Par exemple, pour gérer la relation entre les clients et commandes, les données renvoyées dans la réponse à un ordre spécifique doivent contenir URI sous la forme d’un lien hypertexte identifiant le client ayant passé la commande et les opérations pouvant être effectuées à ce client.

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

Le corps du message de réponse contient un `links` tableau (mis en surbrillance dans l’exemple de code) qui spécifie la nature de la relation (_client_), l’URI du client (_http://adventure-works.com/customers/3_), comment faire pour récupérer les détails de ce client (_obtenir_) et les types MIME que le serveur web prend en charge pour récupérer ces informations (_texte/xml_ et _application/json_). Il s’agit de toutes les informations une application cliente doit être en mesure de récupérer les détails du client. En outre, le tableau des liens inclut également des liens vers d’autres opérations qui peuvent être effectuées, telles que placer (pour modifier le client, ainsi que le format que le serveur web attend le client pour fournir) et supprimer.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[(some links omitted){"rel":"customer","href":" http://adventure-works.com/customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":"
customer","href":" http://adventure-works.com /customers/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"customer","href":" http://adventure-works.com /customers/3","action":"DELETE","types":[]}]}
```

Pour plus de précision, le tableau des liens doit également contenir elle-même plus d’informations sur la ressource qui a été extrait. Ces liens ont été omis dans l’exemple précédent, mais sont mis en surbrillance dans le code suivant. Notez que dans ces liens, la relation _automatique_ a été utilisée pour indiquer qu’il s’agit d’une référence à la ressource renvoyée par l’opération :

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[{"rel":"self","href":" http://adventure-works.com/orders/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" self","href":" http://adventure-works.com /orders/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"self","href":" http://adventure-works.com /orders/3", "action":"DELETE","types":[]},{"rel":"customer",
"href":" http://adventure-works.com /customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" customer" (customer links omitted)}]}
```

Pour que cette approche soit efficace, les applications clientes doivent être préparées à récupérer et d’analyser des informations supplémentaires.

## <a name="versioning-a-restful-web-api"></a>Contrôle de version une API web RESTful

Il est très peu probable que dans l’ensemble, mais la plus simple de situations qu’un site web API restera statique. Mesure que les besoins de l’entreprise changent nouvelles collections de ressources peuvent être ajoutés, les relations entre les ressources peuvent changer et la structure des données dans les ressources peut-être être modifiée. Pendant la mise à jour d’un site web API pour gérer les demandes nouvelles ou différentes est un processus relativement simple, vous devez prendre en compte les effets que ces changements auront sur les applications clientes par d’autres programmes l’API web. Le problème est que bien que le développeur conception et implémentation d’un site web API a un contrôle total sur cette API, le développeur n’est pas le même degré de contrôle sur les applications clientes qui peuvent être créés par les organisations tiers d’exploitation à distance. Les impératifs principal consiste à activer les applications clientes existantes continue de fonctionner inchangée tout en autorisant nouveau client applications pour tirer parti des nouvelles fonctionnalités et des ressources.

Le contrôle de version permet à un site web API pour indiquer les fonctionnalités et les ressources qu’il expose, et une application cliente peut envoyer des demandes qui sont dirigées vers une version spécifique d’une fonctionnalité ou une ressource. Les sections suivantes décrivent les différentes approches, chacun d’eux ayant ses propres avantages et inconvénients.

### <a name="no-versioning"></a>Aucun suivi de version

Cette approche est la plus simple et peut être acceptable pour certaines API interne. Principaux changements peuvent être représentés sous forme de nouvelles ressources ou de nouveaux liens.  Ajout de contenu à des ressources existantes peut ne pas présente une modification avec rupture en tant que les applications clientes qui ne sont pas attend de voir que ce contenu l’ignore simplement.

Par exemple, une demande pour l' URI _http://adventure-works.com/customers/3_ doit renvoyer les détails d’un seul client contenant `id`, `name`, et `address` champs attendus par l’application cliente :

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [AZURE.NOTE] Aux fins de simplicité et de clarté, les exemples de réponses présentées dans cette section n’incluent pas les liens HATEOAS.

Si la `DateCreated` champ est ajouté dans le schéma de la ressource client, puis la réponse se présente comme suit :

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

Les applications clientes existantes peuvent continue de fonctionner correctement si elles sont en mesure d’ignorer les champs non reconnus, tandis que les nouvelles applications clientes peuvent être conçues pour gérer ce nouveau champ. Cependant, si plus radical des modifications du schéma des ressources se produisent (par exemple, supprimant ou en renommant des champs) ou modifier les relations entre les ressources suivantes peuvent constituer nouveautés qui empêche les applications clientes existantes de fonctionner correctement. Dans ce cas, vous devez envisager une des approches suivantes.

### <a name="uri-versioning"></a>Contrôle de version URI

Chaque fois que vous modifiez l’API web ou modifiez le schéma de ressources, vous ajoutez un numéro de version à l’URI pour chaque ressource. Les URI existantes doivent continuer à fonctionner comme auparavant, rétablir des ressources qui sont conformes à leur schéma d’origine.

Extension de l’exemple précédent, si la `address` champ est converti en champs sous-adresse contenant chaque composante de l’adresse (tel que `streetAddress`, `city`, `state`, et `zipCode`), cette version de la ressource peut être exposée via un URI contenant un numéro de version, par exemple http://adventure-works.com/v2/customers/3 :

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Ce mécanisme de contrôle de version est très simple mais dépend du serveur routage la demande au point de terminaison approprié. Cependant, il peut devenir complexe que le site web QU'API arrivera à échéance plusieurs itérations et le serveur doit prendre en charge un nombre de versions différentes. En outre, du point de vue d’une préférence, dans tous les cas les applications clientes extraire les mêmes données (client 3), afin que l’URI ne doit pas être vraiment différent en fonction de la version. Ce modèle complique également l’implémentation de HATEOAS que tous les liens doit inclure le numéro de version dans leurs URI.

### <a name="query-string-versioning"></a>Gestion des versions de chaîne de requête

Plutôt que de fournir plusieurs MU, vous pouvez spécifier la version de la ressource à l’aide d’un paramètre au sein de la chaîne de requête ajoutée à la demande HTTP, par exemple _http://adventure-works.com/customers/3?version=2_. Le paramètre de version par défaut une valeur significative comme 1 s’il est omis par les applications clientes plus anciennes.

Cette approche a l’avantage sémantique que la même ressource est extraite toujours le même URI, mais il dépend du code qui gère la demande pour analyser la chaîne de requête et renvoyer la réponse HTTP appropriée. Cette approche comporte également les problèmes d’implémentation HATEOAS comme mécanisme de contrôle de version URI mêmes.

> [AZURE.NOTE] Certains anciens navigateurs web et les proxys web stockera pas les réponses pour les demandes incluent une chaîne de requête dans l’URL. Ceci peut avoir un impact négatif sur les performances des applications web qui utilisent une API web et qui exécuter à partir de ce type de navigateur web.

### <a name="header-versioning"></a>Contrôle de version en-tête

Plutôt que d’ajouter le numéro de version comme paramètre de chaîne de requête, vous pouvez implémenter un en-tête personnalisé qui indique la version de la ressource. Cette approche nécessite que l’application cliente ajoute l’en-tête approprié à toutes les requêtes, même si le code gère la demande de client puisse utiliser une valeur par défaut (version 1) si l’en-tête de version est omis. Les exemples suivants utilisent un en-tête personnalisé nommé _En-tête personnalisé_. La valeur de cet en-tête indique la version d’API web.

Version 1 :

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Version 2 :

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Notez que comme avec les deux approches précédente, mise en œuvre HATEOAS requiert, y compris l’en-tête personnalisé approprié dans tous les liens.

### <a name="media-type-versioning"></a>Type d’élément multimédia le contrôle de version

Lorsqu’une application cliente envoie une demande HTTP GET à un serveur web, elle doit prévoir le format du contenu qu’elle peut gérer à l’aide d’un en-tête accepter, comme décrit plus haut dans ce guide. L’objectif de l’en-tête de _Accepter_ est fréquemment pour permettre à l’application cliente à spécifier si le corps de la réponse doit être XML, JSON ou un autre format courantes le client peut analyser. Toutefois, il est possible de définir des types de médias personnalisé comprenant des informations sur l’activation de l’application cliente indiquer quelle version d’une ressource attendu. L’exemple suivant montre une requête qui spécifie un en-tête _Accepter_ avec valeur _application/vnd.adventure-works.v1+json_. L’élément _vnd.adventure works.v1_ indique au serveur web qu’elle doit renvoyer version 1 de la ressource, tandis que l’élément _json_ indique que le format du corps de la réponse doit être JSON :

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

Le code gère la demande est responsable de traitement de l’en-tête _Accepter_ et en respectant autant que possible (l’application cliente peut spécifier plusieurs formats dans l’en-tête de _Accepter_ , dans laquelle la casse au serveur web peut choisir format le plus approprié pour le corps de réponse). Le serveur web confirme le format des données dans le corps de réponse à l’aide de l’en-tête de Type de contenu :

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Si l’en-tête accepter n’indique pas les types de médias connus, le serveur web peut générer un message de réponse HTTP 406 (non Acceptable) ou renvoyer un message avec un type de média par défaut.

Cette approche est sans doute les sons des mécanismes de contrôle de version et prête naturellement à HATEOAS, ce qui peut inclure le type MIME des données associées dans des liens vers les ressources.

> [AZURE.NOTE] Lorsque vous sélectionnez une stratégie de contrôle de version, vous devez également considérer les implications sur les performances, notamment la mise en cache sur le serveur web. Le contrôle de version URI et les modèles de contrôle de version de chaîne de requête sont employez cache dans la mesure où la même combinaison de chaîne de requête/URI fait référence aux mêmes données chaque fois.

> Le contrôle de version en-tête et mécanismes de contrôle de version de Type de média en général nécessitent une logique supplémentaire pour examiner les valeurs dans l’en-tête personnalisé ou l’en-tête accepter. Dans un environnement à grande échelle, de nombreux clients à l’aide de versions différentes d’une API web peuvent entraîner une quantité importante de données dupliquées dans un cache côté serveur. Ce problème peut devenir aiguë si une application cliente communique avec un serveur web via un proxy qui mettent en œuvre, la mise en cache, et qui transfère uniquement une demande au serveur web si elle ne tient pas actuellement une copie des données demandées dans son cache.

## <a name="more-information"></a>Plus d’informations

- Le [Livre de recettes RESTful](http://restcookbook.com/) contient une introduction à la création d’API RESTful.
- La [Liste de vérification de l’API](https://mathieu.fenniak.net/the-api-checklist/) Web contient la liste des éléments à prendre en considération lors de la conception et implémentation d’un API Web utile.
