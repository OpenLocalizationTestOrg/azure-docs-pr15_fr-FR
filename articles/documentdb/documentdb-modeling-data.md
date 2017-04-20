<properties 
    pageTitle="Modélisation des données dans Azure DocumentDB | Microsoft Azure" 
    description="Obtenir des informations sur la modélisation de données pour DocumentDB, une base de données de documents NoSQL." 
    keywords="modélisation de données"
    services="documentdb" 
    authors="kiratp" 
    manager="jhubbard" 
    editor="mimig1" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/05/2016" 
    ms.author="kipandya"/>

#<a name="modeling-data-in-documentdb"></a>Modélisation de données dans DocumentDB#
Tandis que les bases de données exempt de schéma, comme DocumentDB Azure, rendent très facile à adopter les modifications apportées à votre modèle de données vous devez toujours réfléchir sur vos données. 

Façon dont données vont être stockés ? Comment votre application va récupérer et interroger des données ? Votre application lu gras ou écriture est importante ? 

Après la lecture de cet article, vous ne pourrez pas répondre aux questions suivantes :

- Comment dois-je envisager un document dans une base de données de document ?
- Quelle est la modélisation des données et Pourquoi m’intéresser ? 
- Quelle est la modélisation des données dans une base de données de document et à une base de données relationnel ?
- Comment faire express des relations entre les données dans une base de données non relationnelles ?
- Lorsque incorporer des données et quand lier aux données ?

##<a name="embedding-data"></a>Incorporer des données##
Lorsque vous démarrez modélisation des données dans un magasin de documents, telles que DocumentDB, essayez de considérer vos entités comme **documents autonomes** représentée dans JSON.

Avant de nous Découvrez trop plus loin, laissez-nous reprendre la procédure d’ajout et observez comment nous pouvons modèle quelque chose dans une base de données relationnel, un objet plupart du temps sont familiers. L’exemple suivant montre comment une personne pouvant être stockée dans une base de données relationnelle. 

![Modèle de base de données relationnelle](./media/documentdb-modeling-data/relational-data-model.png)

Lorsque vous travaillez avec des bases de données relationnelles, nous avons appris ans à normaliser, normaliser, normaliser.

Normaliser vos données en général implique de prendre une entité, telle qu’une personne et transmettre vers le bas aux discrètes ensembles de données. Dans l’exemple ci-dessus, une personne peut avoir plusieurs enregistrements détail de contact, ainsi que plusieurs enregistrements d’adresse. Nous même aller plus loin et décomposer détails de contact en extrayant plus courantes champs comme un type. Même adresse, chaque enregistrement ici a un type comme *famille* ou *entreprise* 

Le guider premise, lors de la normalisation des données est afin d' **éviter de stocker les données redondantes** sur chaque enregistrement et préférez font référence aux données. Dans cet exemple, pour lire une personne, avec tous leurs détails de contact et adresses, vous devez utiliser des jointures pour agréger efficacement vos données en cours d’exécution.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Mise à jour d’une seule personne leurs coordonnées et leurs adresses requiert les opérations d’écriture sur plusieurs tables individuelles. 

Maintenant examinons à présent comment nous serait modèle les mêmes données comme une entité autonome dans une base de données du document.
        
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

À l’aide de l’approche ci-dessus, nous avons à présent l’emplacement d’enregistrement **dénormalisées** la personne nous **incorporé** toutes les informations relatives à cette personne, par exemple, leurs coordonnées et les adresses dans à un seul document JSON.
En outre, étant donné que nous allons limite pas à un schéma fixe nous avons la possibilité d’effectuer les actions comme ayant des coordonnées de différentes formes entièrement. 

Récupération d’un enregistrement de personne complet à partir de la base de données est maintenant une seule opération par rapport à une collection unique et pour un seul document de lecture. Mise à jour d’un enregistrement de la personne, leurs coordonnées et leurs adresses, est également une opération d’écriture unique par rapport à un seul document.

Par dénormalisation des données, votre application peut avoir besoin de problème moins des requêtes et des mises à jour pour effectuer les opérations courantes. 

###<a name="when-to-embed"></a>Quand l’incorporer

En règle générale, utilisez données incorporé modèles quand :

- Il existe **contient** les relations entre les entités.
- Il existe **un-à-quelques-unes** des relations entre les entités.
- Il sont données incorporé qui **changent rarement**.
- Il est incorporé données n’agrandir **sans limite**.
- Il existe des données incorporé sont **intégré** à des données dans un document.

> [AZURE.NOTE] Modèles de données dénormalisées fournissent généralement meilleures performances de **lecture** .

###<a name="when-not-to-embed"></a>Quand ne pas incorporer

Tandis que la règle de base dans une base de données de document consiste à dénormaliser tout incorporer toutes les données dans un seul document, cela peut affecter certaines situations qui doivent être évitées.

Prendre cet extrait JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Il peut s’agir de ce que donnera une entité billet avec commentaires incorporés une si nous avons modélisation un blog classique ou CMS, système. Le problème de cet exemple est que le tableau commentaires est **illimité**, c'est-à-dire qu’il n’existe aucune limite au nombre de commentaires tout poste unique peut avoir (pratique). Cela devient un problème comme augmente considérablement la taille du document.

> [AZURE.TIP] Documents dans DocumentDB ont une taille maximale. Pour plus d’informations sur ce problème, reportez-vous à [DocumentDB limites](documentdb-limits.md).

Fur et à la possibilité de transmettre les données sur le fils ainsi que de lecture et de mise à jour le document, à l’échelle, la taille du document seront affectées.

Dans ce cas, il serait mieux à prendre en considération le modèle suivant.
        
    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Ce modèle présente les trois dernières commentaires incorporés sous le billet lui-même, qui est un tableau avec un fixe lié cette fois. Les autres commentaires sont regroupées dans à des lots de 100 commentaires et stockées dans des documents différents. La taille du lot a été choisie comme 100, car notre application fictif permet à l’utilisateur à charger 100 commentaires à la fois.  

Un autre cas où l’incorporation de données ne sont pas recommandé est lorsque les données incorporées sont souvent utilisées dans tous les documents et modifiera fréquemment. 

Prendre cet extrait JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Il peut s’agir portefeuille d’actions d’une personne. Nous avons choisi d’incorporer les informations de stock à chaque document portefeuille. Dans un environnement où les données associées changent fréquemment, comme une action cotation application, incorporer des données qui changent fréquemment va signifie que vous mettez en permanence à jour chaque document portefeuille chaque fois qu’une action est échangée.

Boursier *zaza* animaux plusieurs centaines de fois dans la journée, ainsi que des milliers d’utilisateurs peuvent avoir *zaza* sur leur portefeuille. Avec un modèle de données comme ci-dessus, il faut mettre à jour des milliers de documents de portefeuille autant de fois quotidiennement menant à un système qui ne puisse pas évoluer très bien. 

##<a id="Refer"></a>Référence à des données##

Par conséquent, incorporer des données fonctionne de façon optimisée pour la plupart des cas, mais il est clair qu’il existe des scénarios lorsque dénormalisation de vos données provoquera plus de problèmes qu’il est important. Ainsi que nous faire maintenant ? 

Bases de données relationnelles ne sont pas le seul endroit où vous pouvez créer des relations entre entités. Dans une base de données de document vous pouvez contenir des informations dans un seul document réellement relatives aux données dans d’autres documents. À présent, je ne suis pas recommandation pour une minute même que nous créer des systèmes qui seraient mieux à une base de données relationnel dans DocumentDB ou toute autre base de données de document, mais des relations simples sont corrects et peuvent être très utiles. 

Dans le JSON ci-dessous, nous avons choisi d’utiliser l’exemple d’un portefeuille de version antérieure, mais cette fois nous faire référence à l’élément boursier sur le portefeuille au lieu de l’incorporer. Ainsi, lorsque l’élément boursier change fréquemment toute la journée le seul document devant être mis à jour est le seul document boursier. 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }
    
    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }
    

Un inconvénient immédiate de cette approche est bien que si votre application est requise pour afficher des informations sur chaque stock qui est conservée lors de l’affichage de portefeuille d’une personne ; Dans ce cas, vous devrez apporter des déplacements multiples à la base de données pour charger les informations relatives à chaque document boursier. Ici, nous avons apporté une décision pour améliorer l’efficacité des opérations d’écriture, qui se produire fréquemment toute la journée, mais qui à son tour compromis sur les opérations de lecture potentiellement ont moins d’impact sur les performances de ce système en particulier.

> [AZURE.NOTE] Normalisé données modèles **peut nécessiter plus aller-retour** sur le serveur.

### <a name="what-about-foreign-keys"></a>Qu’en est-il des clés étrangères ?
Car il n’existe actuellement aucune concept d’une contrainte, clé étrangère ou dans le cas contraire, des relations entre documents que vous avez dans les documents sont en fait « faibles » et la ne seront pas vérifiées par la base de données. Si vous souhaitez vous assurer que les données de qu'un document fait référence à existe réellement, vous devez effectuer cette opération dans votre application ou au moyen de déclencheurs côté serveur ou les procédures stockées sur DocumentDB.

###<a name="when-to-reference"></a>Quand pour faire référence à
En règle générale, utilisez données normalisées modèles quand :

- Représentant **un-à-plusieurs** relations.
- Représentant les relations **plusieurs-à-plusieurs** .
- Liées des données **changent fréquemment**.
- Données référencées peuvent être **illimitée**.

> [AZURE.NOTE] En règle générale normalisation offre de meilleures performances **écrire** .

###<a name="where-do-i-put-the-relationship"></a>Où placer les la relation ?
La croissance de la relation permettent de déterminer dans quel document pour stocker la référence.

Si nous examinons le JSON ci-dessous illustrant les ouvrages et les éditeurs.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "DocumentDB 101" }
    {"id": "2", "name": "DocumentDB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure DocumentDB" }
    ...
    {"id": "1000", "name": "Deep Dive in to DocumentDB" }

Si le nombre des livres par publisher est petit avec croissance limitée, puis le stockage de la référence de livre à l’intérieur du document publisher peuvent vous être utile. Toutefois, si le nombre de livres par publisher est illimité, ce modèle de données entraînerait aux tableaux modifiable, croissants, comme dans l’exemple de document publisher ci-dessus. 

Changement d’éléments peu entraînerait dans un modèle qui représente toujours les mêmes données, mais évite désormais ces grandes collections modifiable.

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }
    
    Book documents: 
    {"id": "1","name": "DocumentDB 101", "pub-id": "mspress"}
    {"id": "2","name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

Dans l’exemple ci-dessus, nous avons supprimé la collection illimitée sur le document de publisher. À la place nous avons simplement une référence à chaque document manuel de l’éditeur.

###<a name="how-do-i-model-manymany-relationships"></a>Comment modéliser les relations plusieurs ?
Dans une base de données relationnel *plusieurs* relations sont souvent modélisation avec des tableaux de jointure, qui simplement relient entre eux les enregistrements à partir d’autres tables. 

![Joindre des tables](./media/documentdb-modeling-data/join-table.png)

Vous serez peut-être tenté de répliquer la même chose à l’aide de documents et produire un modèle de données qui ressemble à ce qui suit.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }
    
    Book documents:
    {"id": "b1", "name": "DocumentDB 101" }
    {"id": "b2", "name": "DocumentDB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure DocumentDB" }
    {"id": "b5", "name": "Deep Dive in to DocumentDB" }
    
    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Cette méthode fonctionne. Toutefois, chargement soit un auteur avec leurs livres ou le chargement d’un carnet d’adresses avec son auteur, toujours verser au moins deux requêtes supplémentaires sur la base de données. Une requête pour le document joint et puis une autre requête pour récupérer le document réel qui sont joint. 

Si toutes les effectue cette table de jonction est collant deux éléments de données, puis pourquoi pas incluez-la complètement ?
Posez-vous les questions suivantes.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}
    
    Book documents: 
    {"id": "b1", "name": "DocumentDB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "DocumentDB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure DocumentDB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to DocumentDB", "authors": ["a2"]}

À présent, si j’ai eu un auteur, immédiatement savoir quels livres qu’ils ont écrits, et inversement si j’avais un document de livre chargé serait savoir les ID du ou des auteurs. Cela permet de gagner cette requête intermédiaire par rapport à la table de jointure réduire le nombre de serveur boucles votre application doit effectuer. 

##<a id="WrapUp"></a>Modèles de données hybride##
Nous avons trouvé maintenant l’incorporation (ou dénormalisation) et données de références (ou normalisation), chacun ont leurs avantages que représenterait et chacun avoir compromis comme nous l’avons vu. 

Il n’est toujours soit soit ou ne pas être pris peur de mélanger choses un peu. 

Basé sur des modèles d’utilisation spécifique et il peut y avoir des cas où mélange incorporé les charges de travail de votre application et données référencées a du sens et pourrait mener à logique de l’application plus simple avec serveur moins boucles tout en conservant un bon niveau de performance.

Vous pouvez le JSON suivant. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",     
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }
    
    Book documents:
    {
        "id": "b1",
        "name": "DocumentDB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "DocumentDB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Ici nous avons (principalement) suivi du modèle incorporé, dans lequel les données à partir d’autres entités sont incorporées dans le document de niveau supérieur, mais d’autres données sont référencées. 

Si vous examinez le document de livre, nous pouvons voir quelques intéressant champs quand nous examinons le tableau des auteurs. Il existe un champ *d’id* qui est le champ que nous utilisons en référence à un document d’auteur, standard relatives aux exercices pratiques dans un modèle centrée réduite, mais puis nous avons également *nom* et *thumbnailUrl*. Nous pouvons avez simplement bloqué avec *l’id* et gauche de l’application pour obtenir des informations supplémentaires, qu'il est nécessaire à partir du document respectifs auteur à l’aide de la « liaison », mais étant donné que notre application affiche le nom d’auteur et une image miniature avec tous les ouvrages affiché nous pouvons enregistrer un aller-retour sur le serveur par la loi dans une liste par dénormalisation **des** données à partir de l’auteur.

Bien sûr, si le nom de l’auteur modifié ou qu’ils veulent mettre à jour sa photo que nous aurait à l’envoi d’une mise à jour tous les ouvrages ils jamais publié mais pour notre application, basée sur l’hypothèse que les auteurs ne changent pas leurs noms très souvent, il s’agit d’une décision de conception acceptable.  

Dans l’exemple sont valeurs **calculées des agrégats** pour enregistrer le traitement coûteux sur une opération de lecture. Dans l’exemple, certaines données incorporées dans le document auteur donnée est calculée au moment de l’exécution. Chaque fois qu’un nouveau classeur est publié, un document de livre est créé **et** que le champ countOfBooks est défini sur une valeur calculée en fonction du nombre de documents d’un livre qui existent pour un auteur particulier. Cette optimisation serait correctement dans systèmes dense lecture dans lequel nous pouvons vous permettre d’effectuer des calculs sur écrit afin d’optimiser la lecture.

La possibilité d’un modèle avec des champs calculés préalable est possible, car DocumentDB prend en charge les **transactions de plusieurs documents**. Nombreuses banques NoSQL ne peut pas transactions dans n’importe quel documents et par conséquent représenter des décisions de conception, tels que « toujours incorporer tout », en raison de cette limitation. Avec DocumentDB, vous pouvez utiliser des déclencheurs côté serveur ou les procédures stockées, qui insèrent ouvrages et mettre à jour les auteurs au sein d’une transaction ACID. À présent que vous **n’avez** à incorporer tout à un seul document uniquement pour vous assurer que vos données restent cohérentes.

##<a name="NextSteps"></a>Étapes suivantes

Les points principaux à partir de cet article consiste à comprendre que la modélisation de données dans un univers exempt de schéma soient tout aussi importantes que jamais. 

Comme il n’existe aucune règle permet de représenter une partie des données sur un écran, il est inutile unique pour modéliser vos données. Vous devez comprendre votre application et comment il produira, consommer et traiter les données. Ensuite, en appliquant certaines des instructions présentées ici, vous pouvez définir sur la création d’un modèle qui répond aux besoins de l’exécution de votre application. Lorsque vos applications ont besoin de modifier, vous pouvez optimiser la flexibilité d’une exempt de schéma de base de données à adopter changent et évoluent facilement votre modèle de données. 

Pour en savoir plus sur DocumentDB Azure, reportez-vous à la page de la [documentation](https://azure.microsoft.com/documentation/services/documentdb/) du service. 

Pour en savoir plus sur réglage index dans Azure DocumentDB, reportez-vous à l’article politique [d’indexation](documentdb-indexing-policies.md).

Pour mieux comprendre comment éclater vos données sur plusieurs partitions, font référence aux [Données partition dans DocumentDB](documentdb-partition-data.md). 

Et enfin, pour des instructions sur la modélisation des données et ont pour les applications clients multiples, consultez [mise à l’échelle d’une Application client à plusieurs avec Azure DocumentDB](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx).
 
