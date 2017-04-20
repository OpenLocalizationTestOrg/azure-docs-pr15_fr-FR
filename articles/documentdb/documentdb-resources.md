<properties 
    pageTitle="DocumentDB modèle ressources hiérarchique et concepts | Microsoft Azure" 
    description="Obtenir des informations sur le modèle hiérarchique de DocumentDB de bases de données, collections, fonction définie par l’utilisateur (UDF), documents, des autorisations pour gérer les ressources et bien plus encore."
    keywords="Modèle hiérarchique, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Concepts et le modèle de ressources hiérarchique DocumentDB

Les entités de base de données qui gère le DocumentDB sont appelées des **ressources**. Chaque ressource est identifiée par un URI logique. Vous pouvez interagir avec les ressources à l’aide de verbes HTTP standard, les en-têtes de demande/réponse et les codes d’état. 

En lisant cet article, vous pouvez répondre aux questions suivantes :

- Qu’est le modèle de ressources de DocumentDB ?
- Que sont les système défini ressources Contrairement à des ressources définies par l’utilisateur ?
- Comment définir les destinataires d’une ressource ?
- Comment travailler avec des collections ?
- Fonctionnement des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF)

## <a name="hierarchical-resource-model"></a>Modèle de ressources hiérarchique
Comme l’illustre le diagramme suivant, hiérarchique DocumentDB **modèle de ressources** se compose des jeux de ressources sous un compte de base de données, chacun dédié via un URI stable et logique. Un ensemble de ressources sera être appelé un **flux** dans cet article. 

>[AZURE.NOTE] DocumentDB offre un protocole TCP hautement efficace qui est également RESTful dans son modèle de communications disponible via le [client .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modèle de ressources hiérarchique DocumentDB][1]  
**Modèle de ressources hiérarchique**   

Pour commencer à travailler avec les ressources, vous devez [créer un compte de base de données DocumentDB](documentdb-create-account.md) à l’aide de votre abonnement Azure. Un compte de base de données peut être constitué d’un ensemble de **bases de données**, chacun d’eux contenant plusieurs **collections de sites**, chacun d'entre eux à son tour contenir **procédures stockées, déclencheurs, UDF, documents** et les **pièces jointes** (fonctionnalité Aperçu). Une base de données est également associé aux **utilisateurs**, chacune avec un ensemble d' **autorisations** pour accéder aux collections de sites, des procédures stockées, déclencheurs, UDF, documents ou pièces jointes. Alors que les bases de données, les utilisateurs, les autorisations et les collections de sites sont définies par le système de ressources avec des schémas connus, documents et les pièces jointes contiennent arbitraire, contenu JSON définies par l’utilisateur.  

|Ressource   |Description
|-----------|-----------
|Compte de base de données   |Un compte de base de données est associé à un ensemble de bases de données et une taille fixe de stockage d’objets blob pour les pièces jointes (fonctionnalité Aperçu). Vous pouvez créer un ou plusieurs comptes de base de données à l’aide de votre abonnement Azure. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
|Base de données   |Une base de données est un conteneur logique de stockage des documents partitionnée provenant de plusieurs collections. Il est également un conteneur d’utilisateurs.
|Utilisateur   |L’espace de noms logique pour l’étendue des autorisations. 
|Autorisation |Un jeton d’autorisation associé à un utilisateur pour l’accès à une ressource spécifique.
|Collection de sites |Une collection de sites est un conteneur de documents JSON et la logique d’application JavaScript associée. Une collection de sites est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le niveau de performance associé à la collection de sites. Collections de sites peuvent s’étendre sur un ou plusieurs partitions/serveurs et pouvant évoluer pour gérer des volumes quasi illimitées de stockage ou débit.
|Procédure stockée   |Logique de l’application écrite en JavaScript qui est inscrit auprès d’une collection et transactionnel exécuté dans le moteur de base de données.
|Déclencheur    |Logique de l’application écrite en JavaScript exécuté avant ou après un insérer, remplacer ou sur l’opération de suppression.
|UDF    |Logique de l’application écrite en JavaScript. UDF permettent de modéliser un opérateur de requêtes personnalisées et étendre ainsi le langage de requête DocumentDB de base.
|Document   |Contenu JSON (arbitraire) définies par l’utilisateur. Par défaut, aucun schéma ne doit être défini ni indices secondaires doivent-ils être fourni pour tous les documents ajoutés à une collection de sites.
|(Preview) Pièce jointe   |Une pièce jointe est un document spécial contenant des références et des métadonnées associées blob/multimédia externe. Le développeur peut choisir d’avoir le blob géré par DocumentDB ou stocker avec un fournisseur de services blob externe tel que OneDrive, Dropbox, etc.. 


## <a name="system-vs-user-defined-resources"></a>Système et ressources définies par l’utilisateur
Ressources comme des comptes de base de données, bases de données, collections de sites, les utilisateurs, autorisations, des procédures stockées, déclencheurs et UDF - tous ont un schéma fixe et sont appelées des ressources système. En revanche, ressources telles que des documents et des pièces jointes n’ont aucune restriction sur le schéma et quelques exemples de ressources définies par l’utilisateur. Dans DocumentDB, système et définis par l’utilisateur ressources sont représentés et managés en tant que compatible avec la norme JSON. Toutes les ressources, système ou définies par l’utilisateur ont les propriétés communes suivantes.

> [AZURE.NOTE] Remarque : tous les systèmes généré propriétés dans une ressource sont précédés d’un trait de soulignement (_) dans leur représentation JSON.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Propriété</strong></p></td>
            <td valign="top"><p><strong>Par l’utilisateur ou générées par le système ?</strong></p></td>
            <td valign="top"><p><strong>Objectif</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_RID</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Système généré, identificateur unique et hiérarchique de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>eTag de la ressource requise pour le contrôle de concurrence optimiste</p></td>
        </tr>
        <tr>
            <td valign="top"><p>DTS</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Dernière modification de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>URI dédié unique de la ressource</p></td>
        </tr>
        <tr>
            <td valign="top"><p>ID</p></td>
            <td valign="top"><p>Généré par le système</p></td>
            <td valign="top"><p>Nom unique de la ressource (avec la même valeur clé partition) définies par l’utilisateur. Si l’utilisateur ne spécifie un id, un id sera généré par le système</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Représentation fils de ressources
DocumentDB n’impose pas toutes les extensions de propriétaires pour les codages JSON standard ou spéciales ; il fonctionne avec les documents JSON conformes standards.  
 
### <a name="addressing-a-resource"></a>Définir les destinataires d’une ressource
Toutes les ressources sont URI dédié. La valeur de la propriété **_self** d’une ressource représente l’URI relatif de la ressource. Le format de l’identificateur URI se compose de la /\<flux\>/ segments de tracé {_rid} :  

|Valeur de la _self |Description
|-------------------|-----------
|/DBS   |Flux des bases de données sous un compte de base de données
|/DBS/ {dbName}  |Base de données avec un id correspondant à la valeur {dbName}
|/colls/ /DBS/ {dbName}   |Flux des collections sous une base de données
|/colls/ /DBS/ {dbName} {collName} |Collection de sites dont l’id correspondant à la valeur {collName}
|/colls/ /DBS/ {dbName} {collName} / documents    |Flux de documents dans une collection de sites
|/docs/ /colls/ {collName} /DBS/ {dbName} {ID de document}    |Document avec un id correspondant à la valeur {document}
|/ Users / /DBS/ {dbName}   |Flux des utilisateurs sous une base de données
|/DBS/ {dbName} / Users / {nom d’utilisateur}   |Utilisateur avec un id correspondant à la valeur {utilisateur}
|/DBS/ {dbName} / Users / {nom d’utilisateur} / autorisations   |Flux d’autorisations sous un utilisateur
|/permissions//Users / {nom d’utilisateur} /DBS/ {dbName} {permissionId}    |Autorisation dont l’id correspondant à la valeur {autorisation}
  
Chaque ressource a un nom défini par l’utilisateur unique exposé via la propriété id. Remarque : pour les documents, si l’utilisateur n’en spécifie un id, notre SDK pris en charge génère automatiquement un identificateur unique pour le document. L’id est une chaîne définie par l’utilisateur, jusqu'à 256 caractères qui est unique dans le contexte d’une ressource parent spécifique. 

Chaque ressource est également un identificateur de ressource hiérarchique généré par le système (également appelé un RID), qui est disponible via la propriété _rid. Le RID code toute la hiérarchie d’une ressource donnée et c’est une pratique représentation interne utilisée pour appliquer l’intégrité référentielle d’une manière distribuée. Le RID est unique au sein d’un compte de base de données et il est utilisé en interne par DocumentDB pour le routage efficace sans avoir besoin de recherches partition croisée. Les valeurs des propriétés _rid et la _self sont des représentations alternative et canoniques d’une ressource. 

Les API REST DocumentDB prend en charge adressage des ressources et le routage de requêtes par le code et les propriétés _rid.

## <a name="database-accounts"></a>Comptes de base de données
Vous pouvez configurer un ou plusieurs comptes DocumentDB base de données à l’aide de votre abonnement Azure.

Vous pouvez [créer et gérer les comptes de base de données DocumentDB](documentdb-create-account.md) via le portail Azure à [http://portal.azure.com/](https://portal.azure.com/). Création et gestion d’un compte de base de données requiert l’accès administratif et ne peuvent être effectuée sous votre abonnement Azure. 

### <a name="database-account-properties"></a>Propriétés du compte de base de données
Dans le cadre de la mise en service et la gestion d’un compte de base de données, vous pouvez configurer et lire les propriétés suivantes :  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nom de la propriété</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Règle de cohérence</p></td>
            <td valign="top"><p>Définir cette propriété pour configurer le niveau de cohérence par défaut pour toutes les collections sous votre compte de base de données. Vous pouvez remplacer le niveau de cohérence sur une base par demande à l’aide de l’en-tête de requête [x-ms--niveau de cohérence]. <p><p>Notez que cette propriété s’applique uniquement pour les <i>ressources définies par l’utilisateur</i>. Système toutes les ressources définies sont configurés pour prendre en charge lectures/requêtes grâce à la cohérence forte.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clés d’autorisation</p></td>
            <td valign="top"><p>Voici les clés principales et en lecture seule principales et secondaires qui fournissent un accès administratif à toutes les ressources sous le compte de base de données.</p></td>
        </tr>
    </tbody>
</table>

Notez qu’en plus de mise en service, la configuration et la gestion de votre compte de base de données à partir du portail Azure, vous pouvez également programmer créer et gérer les comptes de base de données DocumentDB à l’aide de la [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ainsi que les [clients SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

## <a name="databases"></a>Bases de données
Une base de données DocumentDB est un conteneur logique d’une ou plusieurs collections de sites et d’utilisateurs, comme le montre l’illustration suivante. Vous pouvez créer un nombre quelconque de bases de données sous un compte de base de données DocumentDB soumis à des limites de l’offre.  

![Modèle de base de données hiérarchique compte et des collections][2]  
**Une base de données est un conteneur logique des utilisateurs et des collections de sites**

Une base de données peut contenir le stockage de documents quasiment illimitée partitionnée par collections, qui constituent les domaines transaction pour les documents qu’ils contiennent. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Élastique échelle d’une base de données DocumentDB
Une base de données DocumentDB est élastique par défaut – allant quelques Go à Po de stockage des documents SSD sauvegardée et débit mis en service. 

Contrairement à une base de données SGBDR traditionnel, une base de données dans DocumentDB n’est pas dans l’étendue à un seul ordinateur. Avec DocumentDB, selon les besoins d’échelle de votre application se développe, vous pouvez créer plusieurs collections de sites, les bases de données ou les deux. En effet, diverses applications tierces premières au sein de Microsoft utilisiez-vous DocumentDB à une échelle consommateur en créant extrêmement des bases de données DocumentDB chaque qui contient des milliers de collections de sites avec To de stockage des documents. Vous pouvez agrandir ou réduire une base de données en ajoutant ou en supprimant des collections de sites pour répondre aux exigences d’échelle de votre application. 

Vous pouvez créer un nombre quelconque de collections de sites au sein d’une base de données soumis à l’offre. Chaque collection de sites a stockage SSD sauvegardée et débit mis en service pour vous en fonction du niveau de performance sélectionné.

Une base de données DocumentDB est également un conteneur d’utilisateurs. Un utilisateur, activer, est un espace de noms logique pour un ensemble d’autorisations qui fournit une autorisation précise et accéder aux collections de sites, des documents et des pièces jointes.  
 
Comme avec d’autres ressources dans le modèle de ressources DocumentDB, bases de données pouvant être créées, remplacé, supprimé, lire ou énumérés facilement à l’aide [d’Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou une du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garantit la cohérence forte pour la lecture ou interroger les métadonnées d’une ressource de base de données. Suppression d’une base de données automatiquement garantit que vous ne peut pas accéder à une des collections de sites ou des utilisateurs qu’il contient.   

## <a name="collections"></a>Collections de sites
Une collection de sites DocumentDB est un conteneur pour vos documents JSON. Une collection de sites est également une unité d’échelle pour les transactions et requêtes. 

### <a name="elastic-ssd-backed-document-storage"></a>Stockage des documents SSD sauvegardée élastique
Une collection de sites est intrinsèque élastique - elle se développe automatiquement et est réduit lorsque vous ajoutez ou supprimez des documents. Collections de sites sont ressources logiques et peuvent s’étendre sur une ou plusieurs partitions physiques ou serveurs. Le nombre de partitions au sein d’une collection de sites est déterminé par DocumentDB en fonction de la taille de stockage et le débit généré de votre collection de sites. Toutes les partitions dans DocumentDB a une taille fixe de stockage de secours SSD associé et répliquées de disponibilité. Gestion des partitions est entièrement gérée par DocumentDB Azure, et vous n’avez pas à écrire du code complexe ou gérer vos partitions. Collections DocumentDB sont **pratiquement illimité** en termes de stockage et de débit. 

### <a name="automatic-indexing-of-collections"></a>L’indexation automatique des collections
DocumentDB est un système true exempt de schéma de base de données. Il ne pas part du principe ou demander un schéma pour les documents JSON. Lorsque vous ajoutez des documents à une collection de sites, DocumentDB les index automatiquement et qu’elles sont disponibles pour vous permet de la requête. L’indexation automatique des documents sans schéma ou à partir d’index secondaire est une fonction essentielle de DocumentDB et est activée par des techniques de gestion des index optimisée pour l’écriture, sans verrouillage et structurées journal. DocumentDB prend en charge prolongée volume d’écritures extrêmement rapides lors de toujours traiter les requêtes cohérentes. Stockage de documents et index sont utilisées pour calculer le stockage utilisé par chaque collection de sites. Vous pouvez contrôler les stockage et performances compromis associés à l’indexation en configurant la stratégie d’indexation pour une collection de sites. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configuration de la stratégie d’indexation d’une collection de sites
La stratégie d’indexation de chaque collection de sites vous permet des performances et stockage compromis associés à l’indexation. Les options suivantes sont disponibles dans le cadre de la configuration de l’indexation :  

-   Choisissez si la collection automatiquement index tous les documents ou non. Par défaut, tous les documents sont automatiquement indexés. Vous pouvez choisir de désactiver l’indexation automatique et sélectivement ajouter uniquement des documents spécifiques à l’index. En revanche, vous pouvez sélectionner exclure des documents spécifiques. Vous pouvez y parvenir en définissant la propriété automatique pour être vrai ou faux sur l’indexingPolicy d’une collection d’et à l’aide de l’en-tête de requête [x-ms-indexingdirective] pendant l’insertion, remplacer ou suppression d’un document.  
-   Choisissez si vous souhaitez inclure ou exclure les chemins d’accès spécifiques ou des motifs dans vos documents de l’index. Vous pouvez y parvenir en paramètre includedPaths et excludedPaths sur l’indexingPolicy d’une collection de respectivement. Vous pouvez également configurer les stockage et performances compromis pour les requêtes de plage et hachage pour les modèles de chemin d’accès spécifique. 
-   Choisir entre synchrone (cohérent) et mises à jour asynchrone index (différé). Par défaut, l’index est mise à jour synchrone sur chaque insertion, de remplacer ou de suppression d’un document à la collection. Ainsi, les requêtes de respecter le niveau de cohérence même que celui de la lecture du document. Alors que DocumentDB est optimisé en écriture et prend en charge des volumes prolongées d’écritures document ainsi que la maintenance des index synchrone et traiter les requêtes cohérentes, vous pouvez configurer certains collections pour mettre à jour leur index différée. Indexation lente améliore les performances d’écriture supplémentaire et est idéal pour les scénarios d’intégration en bloc pour principalement des collections de lourds de lecture.

La stratégie d’indexation peut être modifiée par l’exécution de vente sur la collection de sites. Il est possible par le biais du [Kit de développement logiciel client](https://msdn.microsoft.com/library/azure/dn781482.aspx), le [Portail Azure](https://portal.azure.com) ou les [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Interroger une collection de sites
Les documents au sein d’une collection de sites peuvent avoir schémas arbitraires et vous pouvez rechercher des documents au sein d’une collection de sites sans fournir un schéma ou indices secondaires dès le départ. Vous pouvez interroger la collection de sites à l’aide de la [syntaxe SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), qui fournit enrichi hiérarchique, relationnelles et opérateurs spatiales et l’extensibilité via UDF basé sur JavaScript. Permet de grammaire JSON pour les documents JSON comme arborescences avec les étiquettes en tant que les nœuds d’arbre de modélisation. Ceci est exploité à la fois par des techniques d’indexation automatiques de DocumentDB ainsi que langage SQL de DocumentDB. Le langage de requête DocumentDB se compose de trois principaux aspects :   

1.  Un petit ensemble d’opérations de requête qui sont mappées naturellement à l’arborescence, y compris les projections et requêtes hiérarchiques. 
2.  Un sous-ensemble d’opérations relationnelles, y compris la composition, filtrer, projections, agrégats et jointures automatique. 
3.  JavaScript intégral sur la base UDF fonctionnant avec (1) et (2).  

Le modèle de requête DocumentDB tente d’obtenir un équilibre entre les fonctionnalités, l’efficacité et la simplicité. Le moteur de base de données DocumentDB en mode natif compile et exécute les requête des instructions SQL. Vous pouvez interroger une collection en utilisant les [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ni aucun du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Le Kit de développement .NET est fourni avec un fournisseur LINQ.

> [AZURE.TIP] Vous pouvez essayer DocumentDB et exécuter des requêtes SQL sur notre dataset dans le [Laboratoire de requête](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transactions de plusieurs documents
Transactions de base de données fournissent un modèle de programmation approuvé et prévisible en matière de gestion des modifications simultanées aux données. Dans SGBDR, la méthode classique pour écrire la logique métier consiste à écrire **des procédures stockées** et/ou **déclencheurs** et envoyez-le au serveur de base de données pour l’exécution de transactions. Dans SGBDR, le programmeur de l’application est requise pour traiter deux langages de programmation différents : 

- L’application (non transactions) (par exemple, JavaScript, Python, c#, Java, etc.) de langage de programmation
- T-SQL, le langage de programmation transactions qui est exécuté en mode natif par la base de données

En raison de son engagement approfondie à JavaScript et JSON directement dans le moteur de base de données, DocumentDB fournit un modèle de programmation intuitif pour exécuter logique JavaScript, en fonction de l’application directement sur les collections en ce qui concerne les procédures stockées et des déclencheurs. Ceci permet les actions suivantes :

- Contrôler l’implémentation efficace d’accès concurrentiels au, récupération automatique de l’indexation des graphiques d’objets JSON directement dans le moteur de base de données
- Naturellement exprimant flux de contrôle, variable étendue, d’affectation et de l’intégration des exceptions primitives avec les transactions de base de données directement en ce qui concerne le langage de programmation JavaScript

La logique JavaScript inscrite à un niveau de collection de sites peut alors émettre des opérations de base de données sur les documents de la collection donnée. DocumentDB enveloppe implicitement le code JavaScript en fonction de déclencheurs dans une ambiance transactions ACID avec isolement de capture instantanée et les procédures stockées dans tous les documents au sein d’une collection de sites. Au cours de son exécution, si le code JavaScript lève une exception, la transaction entière est annulée. Le modèle de programmation qui en résulte est très simple mais puissants. Les développeurs JavaScript obtenir un modèle de programmation « résistant » tout en utilisant leur constructions de langage familières et primitives de bibliothèque.   

La capacité à exécuter JavaScript directement dans le moteur de base de données dans le même espace d’adressage en tant que le pool de tampons permet performant et transactions exécution des opérations de base de données sur les documents d’une collection. En outre, moteur de base de données DocumentDB effectue une profondeur envers le JSON et JavaScript élimine tout impédance entre les systèmes de type d’application et la base de données.   

Après avoir créé une collection de sites, vous pouvez enregistrer des procédures stockées, déclencheurs et UDF avec une collection en utilisant les [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ni aucun du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Après l’enregistrement, vous pouvez faire référence et les exécuter. Examinez la procédure stockée suivante entièrement écrite en JavaScript, le code ci-dessous prend deux arguments (nom de l’annuaire et nom de l’auteur) et crée un nouveau document, des requêtes pour un document et puis met à jour – toutes les tâches dans une transaction ACID implicite. À tout moment pendant l’exécution, si une exception JavaScript est renvoyée, toute la transaction s’interrompt.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Le client peut « livrer » la logique JavaScript ci-dessus pour la base de données pour l’exécution de transactions via HTTP POST. Pour plus d’informations sur l’utilisation de méthodes HTTP, voir [RESTful interactions avec les ressources de DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Notez que parce que la base de données en mode natif comprend les JSON et JavaScript, il n’existe aucune incompatibilité de système de type, aucune « Mappage OR » ou magie de génération de code requis.   

Déclencheurs et procédures stockées interagissent avec une collection de sites et des documents dans une collection via un modèle d’objet bien défini qui expose le contexte actuel de la collection de sites.  

Collections dans DocumentDB peuvent être créées, supprimés, lisez ou énumérées facilement à l’aide de des options les [Azure DocumentDB reste API](https://msdn.microsoft.com/library/azure/dn781481.aspx) ni aucun du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB toujours cohérence fort pour la lecture ou interroger les métadonnées d’une collection de sites. Suppression d’une collection automatiquement garantit que vous ne pouvez pas accéder à toutes les documents, les pièces jointes, les procédures stockées, déclencheurs et UDF qu’il contient.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur (UDF)
Comme décrit dans la section précédente, vous pouvez écrire la logique de l’application pour exécuter directement au sein d’une transaction à l’intérieur du moteur de base de données. La logique d’application peut être écrites entièrement en JavaScript et peut être modélisée comme une procédure stockée, déclencheur ou un fichier UDF. Le code JavaScript au sein d’une procédure stockée ou un déclencheur peut insérer, remplacer, supprimer, des documents en lecture ou la requête dans une collection de sites. En revanche, le code JavaScript au sein d’un fichier UDF ne peut pas insérer, remplacer ou supprimer des documents. UDF énumérer les documents du jeu de résultats d’une requête et produisent un autre jeu de résultats. Pour plusieurs location, DocumentDB applique une strict réservation basée la gouvernance de ressources. Chaque procédure stockée, déclencheur ou UDF Obtient un quantum fixe de ressources de système d’exploitation son travail. En outre, les procédures stockées, déclencheurs ou UDF ne peut pas créer un lien par rapport aux bibliothèques JavaScript externe et sur liste noire si elles dépassent les budgets de ressources qui leur est attribuées. Vous pouvez enregistrer, annuler l’inscription des procédures stockées, déclencheurs ou UDF avec une collection de sites à l’aide de l’API REST.  Lors de l’enregistrement une procédure stockée, déclencheur ou un UDF est compilé avant et stockée en tant que code octet qui est exécutée ultérieurement. La section suivante illustrent comment vous pouvez utiliser le Kit de développement DocumentDB JavaScript pour enregistrer, exécuter et annuler l’enregistrement d’une procédure stockée, déclencheur et un fichier UDF. Le JavaScript SDK constitue une enveloppe simple via l' [API REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Inscription d’une procédure stockée
Enregistrement d’une procédure stockée crée une nouvelle ressource procédure stockée sur une collection via HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Exécution d’une procédure stockée
L’exécution d’une procédure stockée est terminée en lançant une publication HTTP par rapport à une ressource de procédure stockée existante en passant des paramètres à la procédure décrite dans le corps de la requête.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Annuler l’inscription d’une procédure stockée
Annuler l’inscription d’une procédure stockée simplement exécuté en lançant une méthode HTTP DELETE par rapport à une ressource de procédure stockée existante.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Enregistrement d’un déclencheur préliminaire
Enregistrement d’un déclencheur est effectué en créant une nouvelle ressource déclencheur sur une collection de sites via HTTP POST. Vous pouvez spécifier si le déclencheur est une pré ou un déclencheur billet et le type d’opération, il peut être associé (par exemple, créer, remplacer, supprimer ou tous).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>L’exécution d’un déclencheur préliminaire
L’exécution d’un déclencheur effectue en spécifiant le nom d’un déclencheur existant au moment de l’exécution de la demande POST/place/supprimer d’une ressource de document via l’en-tête de requête.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Annuler l’inscription d’un déclencheur préliminaire
Annuler l’inscription d’un déclencheur s’effectue simplement via délivrer une méthode HTTP DELETE par rapport à une ressource déclencheur existante.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Enregistrement d’un fichier UDF
Enregistrement d’un fichier UDF est effectué en créant une nouvelle ressource UDF sur une collection de sites via HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>L’exécution d’un fichier UDF dans le cadre de la requête
Un fichier UDF peut être indiqué dans le cadre de la requête SQL et est utilisé comme une façon d’étendre le [langage de requête SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)de base.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Annuler l’inscription d’un fichier UDF 
Annuler l’inscription d’un fichier UDF simplement exécuté en lançant une méthode HTTP DELETE par rapport à une ressource UDF existante.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Bien que les extraits de code ci-dessus montré l’inscription (POST), annulation de l’inscription (place), en lecture/liste (GET) et l’exécution (POST) via le [Kit de développement logiciel DocumentDB JavaScript](https://github.com/Azure/azure-documentdb-js), vous pouvez également utiliser les [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou autre [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documents
Vous pouvez insérer, remplacer, supprimer, lire, énumérer et interroger des documents JSON arbitraires dans une collection de sites. DocumentDB n’impose pas de n’importe quel schéma et ne nécessite pas d’index secondaires pour prendre en charge l’interrogation sur des documents dans une collection de sites.   

Est un service de base de données réellement ouverte, DocumentDB créez pas des types de données spécialisées (par exemple, date heure) ou des codages spécifiques pour les documents JSON. Notez que DocumentDB ne requiert pas les conventions JSON spéciales codifier les relations entre les différents documents ; la syntaxe SQL de DocumentDB fournit très puissante requête hiérarchique et relationnelle opérateurs aux documents de requête et de projet sans annotations spéciales ou qu’il soit nécessaire à la codification de relations entre des documents à l’aide de distinguent les propriétés.  
 
Comme avec toutes les autres ressources, documents peuvent être créés, remplacé, supprimés, lecture, énumérée et interrogé facilement à l’aide de l’API REST ou un du [client SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Suppression d’un document instantanément libère le quota correspondant à toutes les pièces jointes imbriquées. Le niveau de cohérence de lecture de documents suit la stratégie de cohérence sur le compte de base de données. Cette stratégie peut être remplacée sur une base par demande en fonction des besoins de la cohérence des données de votre application. Lorsque vous interrogez des documents, la cohérence de lecture suit le mode d’indexation défini dans la collection de sites. Pour « cohérentes », il suit la règle de cohérence du compte. 

## <a name="attachments-and-media"></a>Pièces jointes et éléments multimédias
>[AZURE.NOTE] Ressources de pièce jointe et éléments multimédias sont des fonctionnalités preview.
 
DocumentDB vous permet de stocker des objets BLOB/media binaire avec DocumentDB ou dans votre propre magasin le support étendu. Il vous permet également représenter les métadonnées d’un média en termes d’un document spécial appelé pièce jointe. Pièce jointe dans DocumentDB est un document (JSON) spécial qui fait référence au média/blob stocké ailleurs. Une pièce jointe est simplement un document spécial qui capture les métadonnées (par exemple, emplacement, auteur etc.) d’un média stockée dans un espace de stockage de support étendu. 

Considérez une application de lecture social qui utilise DocumentDB pour stocker les annotations manuscrites, et les métadonnées, y compris les commentaires, met en surbrillance, signets, évaluations, j’aime/sous-pages etc. associé à un livre électronique d’un utilisateur donné.   

-   Le contenu du livre lui-même est stocké dans le stockage media soit disponible dans le cadre du compte de base de données DocumentDB ou un magasin support étendu. 
-   Une application peut stocker les métadonnées de chaque utilisateur comme un document distinct : par exemple, les métadonnées de Jean pour Classeur1 sont stockées dans un document référencé par /colls/joe/docs/book1. 
-   Les pièces jointes en pointant sur les pages de contenu d’un livre donné d’un utilisateur sont stockées sous le document correspondant, par exemple /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 etc.. 

Notez que les exemples ci-dessus utilisent ID convivial pour illustrer la hiérarchie des ressources. Les ressources sont accessibles via l’API REST ID ressource unique. 

Pour les éléments multimédias sont gérées par DocumentDB, la propriété _media de la pièce jointe référence les éléments multimédias par son URI. DocumentDB assuré pour garbage collecter les éléments multimédias lorsque toutes les références en suspens sont supprimées. DocumentDB génère la pièce jointe lorsque vous téléchargez le nouveau média automatiquement et remplit la _media pour qu’il pointe vers le support nouvellement ajouté. Si vous choisissez de stocker les éléments multimédias dans un magasin étendu des objets blob géré par vous (par exemple, OneDrive, le stockage Azure, DropBox etc.), vous pouvez toujours utiliser les pièces jointes pour référencer les éléments multimédias. Dans ce cas, vous créez la pièce jointe vous-même et remplissez sa propriété _media.   

Comme avec toutes les autres ressources, pièces jointes pouvant être créées, remplacé, supprimés, lire ou énumérés facilement à l’aide de l’API REST ou un du client SDK. Comme avec les documents, le niveau de cohérence de lecture des pièces jointes suit la stratégie de cohérence sur le compte de base de données. Cette stratégie peut être remplacée sur une base par demande en fonction des besoins de la cohérence des données de votre application. Lorsque vous interrogez pour les pièces jointes, la cohérence de lecture suit le mode d’indexation défini dans la collection de sites. Pour « cohérentes », il suit la règle de cohérence du compte. 
 
## <a name="users"></a>Utilisateurs
Un utilisateur DocumentDB représente un espace de noms logique pour le regroupement des autorisations. Un utilisateur DocumentDB peut correspondent à un utilisateur dans un système de gestion des identités ou d’un rôle d’application prédéfinis. Pour DocumentDB, un utilisateur représente simplement une abstraction pour regrouper un ensemble d’autorisations sous une base de données.   

Pour implémenter location multiples dans votre application, vous pouvez créer des utilisateurs dans DocumentDB qui correspond à vos utilisateurs réels ou du client de votre application. Vous pouvez ensuite créer des autorisations pour un utilisateur donné qui correspondent au contrôle d’accès sur plusieurs collections de sites, documents, pièces jointes, etc..   

En fonction de vos applications besoins à l’échelle avec la croissance de vos utilisateurs, vous pouvez adopter différentes façons d’éclater vos données. Vous pouvez modéliser chacun de vos utilisateurs comme suit :   

-   Chaque utilisateur correspond à une base de données.
-   Chaque utilisateur correspond à une collection de sites. 
-   Documents correspondant à plusieurs utilisateurs atteindre une collection dédiée. 
-   Documents correspondant à plusieurs utilisateurs Accédez à un ensemble de collections de sites.   

Quelle que soit la stratégie ont spécifiques que vous choisissez, vous pouvez modéliser les utilisateurs réels en tant qu’utilisateurs de base de données DocumentDB et associer des autorisations à grains fins optimiser pour chaque utilisateur.  

![Collections d’utilisateurs][3]  
**Stratégies ont et les utilisateurs de modélisation**

Comme toutes les autres ressources, utilisateurs dans DocumentDB peut être créées, remplacé, supprimés, lire ou énumérés facilement à l’aide de l’API REST ou un du client SDK. DocumentDB toujours cohérence fort pour la lecture ou interroger les métadonnées d’une ressource utilisateur. Il est important de noter que la suppression d’un utilisateur automatiquement garantit que vous ne pouvez pas accéder à toutes les autorisations qu’il contient. Même si la DocumentDB récupère le quota des autorisations dans le cadre de l’utilisateur supprimé en arrière-plan, les autorisations supprimées est disponible instantanément à nouveau pour pouvoir utiliser.  

## <a name="permissions"></a>Autorisations
Dans une perspective de contrôle d’accès, ressources tels que les comptes de base de données, les bases de données, les utilisateurs et autorisations sont considérées comme des ressources *d’administration* dans la mesure où ces requièrent des autorisations administratives. Quant à eux, les ressources, y compris les collections de sites, documents, pièces jointes, les procédures stockées, déclencheurs et UDF sont inclus dans l’étendue sous une base de données donnée et considérées comme des *ressources de l’application*. Correspondant aux deux types de ressources et les rôles qui y accèdent (à savoir administrateur et utilisateur), le modèle d’autorisation définit deux types de *touches d’accès rapide*: *la clé principale* et la *clé de ressource*. La clé principale est une partie du compte de base de données et est fournie pour le développeur (ou administrateur) qui est mise en service du compte de base de données. Cette clé principale a une sémantique administrateur, dans la mesure où il peut être utilisé pour autoriser l’accès aux ressources d’administration et applications. En revanche, une clé de ressource est une clé d’accès précis qui autorise l’accès à une ressource d’application *spécifique* . Par conséquent, elle capture la relation entre l’utilisateur d’une base de données et les autorisations de l’utilisateur pour une ressource spécifique (par exemple, collection de sites, document, pièce jointe, une procédure stockée, déclencheur ou UDF).   

Le seul moyen d’obtenir une clé de ressource est en créant une ressource d’autorisation sous un utilisateur donné. Notez qu’afin de créer ou récupérer une autorisation, une clé principale doivent être affichée dans l’en-tête d’autorisation. Une ressource d’autorisation lie la ressource, son accès et l’utilisateur. Après avoir créé une ressource d’autorisation, l’utilisateur doit présenter la clé de ressource associées afin de pouvoir accéder à la ressource appropriée. Par conséquent, une clé de ressource peut être affichée comme une représentation compacte et logique de la ressource d’autorisation.  

Comme avec toutes les autres ressources, autorisations dans DocumentDB peut être créées, remplacé, supprimés, lire ou énumérés facilement à l’aide de l’API REST ou un du client SDK. DocumentDB toujours cohérence fort pour la lecture ou interroger les métadonnées d’une autorisation. 

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’utilisation des ressources à l’aide des commandes HTTP dans [RESTful interactions avec les ressources de DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

