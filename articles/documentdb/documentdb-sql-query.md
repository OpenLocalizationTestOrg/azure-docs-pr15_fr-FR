<properties 
    pageTitle="La syntaxe SQL et SQL de la requête pour DocumentDB | Microsoft Azure" 
    description="Découvrez la syntaxe SQL, les concepts de base de données et les requêtes SQL pour DocumentDB, une base de données NoSQL. SQL utilisable comme un langage de requête JSON dans DocumentDB." 
    keywords="la syntaxe SQL, requête sql, requêtes sql, langage de requête json, concepts de base de données et des requêtes sql, fonctions d’agrégation"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Requête SQL et syntaxe SQL de DocumentDB
Microsoft Azure DocumentDB prend en charge l’interrogation des documents en utilisant SQL (Structured Query Language) comme un langage de requête JSON. DocumentDB est véritablement exempt de schéma. En raison de son engagement dans le modèle de données JSON directement dans le moteur de base de données, il propose l’indexation automatique de documents JSON sans schéma explicite ou création d’index secondaires. 

Lors de la conception du langage de requête pour DocumentDB, nous avons rencontré deux objectifs à l’esprit :

-   Au lieu d’un nouveau langage de requête JSON à inventer, nous voulons prend en charge SQL. SQL est l’une des langues de requête plus familière et populaires. DocumentDB SQL fournit un modèle de programmation formel pour des requêtes enrichies sur documents JSON.
-   En tant que JSON document base capable d’exécuter JavaScript directement dans le moteur de base de données, nous voulons utiliser le modèle de programmation du JavaScript comme point de départ pour notre langage de requête. Le code SQL DocumentDB racine se trouve dans le système de type de JavaScript, évaluation de l’expression ainsi que l’appel de fonction. Ce tour fournit un modèle de programmation naturel pour projections relationnelles, navigation hiérarchique dans l’ensemble de documents JSON, jointures automatique, recherches spatiales et appel de fonctions définies par l’utilisateur (UDF) entièrement écrit en JavaScript, entre autres fonctionnalités. 

Nous estimons que ces fonctionnalités sont clé pour réduire le frottement entre l’application et la base de données et sont essentielles pour la productivité du développeur.

Nous vous recommandons de prise en main en regardant la vidéo suivante, où Aravind Ramachandran affiche les fonctionnalités d’interrogation de DocumentDB, et en visitant notre [Laboratoire de requête](http://www.documentdb.com/sql/demo), où vous pouvez essayer DocumentDB et exécuter des requêtes SQL sur notre dataset.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Revenez ensuite à cet article, où nous allons commencer par un didacticiel de requête SQL qui vous guide dans certains documents JSON simples et les commandes SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Prise en main des commandes SQL dans DocumentDB
Pour voir DocumentDB SQL au travail, nous allons commencer par quelques documents JSON simples et passez en revue certaines des requêtes simples à celui-ci. Pensez à ces documents JSON deux sur deux familles. Notez qu’avec DocumentDB, nous n’avez pas besoin de créer des schémas indices secondaires explicitement. Il suffit d’insérer les documents JSON pour une collection de sites DocumentDB et par la suite de la requête. Ici, nous avons une JSON simple pour la famille Andersen, les parents, enfants (et leurs animaux), adresse et enregistrement informations sur le document. Le document comporte des chaînes, des nombres, des booléens, des tableaux et des propriétés imbriquées. 

**Document**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Voici un second document avec une légère différence – `givenName` et `familyName` sont utilisées à la place de `firstName` et `lastName`.

**Document**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Maintenant essayer plusieurs requêtes sur ces données pour comprendre certaines des aspects essentiels de DocumentDB SQL. Par exemple, la requête suivante renvoie les documents dans le champ Nº correspond à `AndersenFamily`. Dans la mesure où il s’agit d’un `SELECT *`, le résultat de la requête est la totalité du document JSON :

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Examinons à présent le cas dans lequel nous avons besoin de remettre en forme le résultat JSON dans une autre forme. Cette requête projets un nouvel objet JSON avec deux champs sélectionnés, nom et ville, lors de la ville de l’adresse portant le même nom en tant que l’état. Dans ce cas, correspond à « NY, NY ».

**Requête**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Résultats**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La requête suivante renvoie tous les noms de donné des enfants de la famille dont l’id correspond `WakefieldFamily` classés par la ville de résidence.

**Requête**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Résultats**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Nous souhaitons attirer l’attention sur quelques aspects applications intéressantes du langage de requête DocumentDB via les exemples que nous avons abordés jusqu'à maintenant :  
 
-   Dans la mesure où DocumentDB SQL travaille sur valeurs JSON, il traite des arborescence en forme entités au lieu de lignes et de colonnes. Par conséquent, la langue vous permet de faire référence à des nœuds de l’arborescence n’importe quelle profondeur arbitraire, comme `Node1.Node2.Node3…..Nodem`, similaire à relationnel SQL faisant référence à la référence de deux parties de `<table>.<column>`.   
-   Le langage de requête structuré fonctionne avec les données sans schéma. Par conséquent, le système de type doit être lié dynamiquement. La même expression peut générer différents types de documents différents. Le résultat d’une requête est une valeur JSON valide, mais n’est pas garanti comme étant d’un schéma fixe.  
-   DocumentDB prend uniquement en charge les documents JSON stricts. Cela signifie que le système de type et les expressions sont limitées à traiter uniquement les types de JSON. Reportez-vous à la [spécification JSON](http://www.json.org/) pour plus d’informations.  
-   Une collection de sites DocumentDB est un conteneur exempt de schéma de documents JSON. Les relations dans des entités de données au sein des documents dans une collection de sites et sont capturées implicitement l’imbrication et non par clé primaire et les relations de clé étrangères. Il s’agit d’un aspect important noter lumière les jointures internes au document décrites plus loin dans cet article.

## <a name="documentdb-indexing"></a>DocumentDB l’indexation

Avant de passer à la syntaxe de la DocumentDB SQL, il convient d’exploration de la conception d’indexation dans DocumentDB. 

L’objectif d’index de base de données est pour répondre à des requêtes dans leurs différents formulaires et formes avec une consommation minimale des ressources (par exemple, processeur et d’entrée/sortie) tout en garantissant bon débit et faible latence. Souvent, le choix de l’index droit pour interroger une base de données requiert une quantité planification et essais. Cette approche pose un problème aux bases de données sans schéma où les données n’est pas conforme à un schéma strict et évolue rapidement. 

Par conséquent, quand nous conçu la DocumentDB l’indexation sous-système, nous définir les objectifs suivants :

-   Indexer des documents sans avoir besoin de schéma : le sous-système d’indexation ne pas exiger des informations de schéma ou faire d’hypothèses à propos du schéma des documents. 

-   Prise en charge des requêtes efficaces, enrichis hiérarchiques et relationnels : l’index prend en charge le langage de requête DocumentDB efficacement, avec prise en charge pour les projections hiérarchiques et relationnelles.

-   Prise en charge pour les requêtes cohérentes constitué un volume prolongée d’écriture : pour écriture haut débit charges de travail avec requêtes cohérentes, l’index est mis à jour par incréments, efficacement et en ligne face à un nombre constant d’écrit. La mise à jour cohérente index est crucial pour traiter les requêtes au niveau de la cohérence dans lequel l’utilisateur configuré le service de document.

-   Prise en charge de plusieurs location : accordées le modèle de réservation en fonction de la gouvernance de ressources entre les clients, mises à jour de l’index sont exécutés dans le budget des ressources système (processeur, mémoire et entrées/sorties par seconde) allouées par réplique. 

-   L’efficacité du stockage : pour rentabilité, les frais de stockage sur le disque de l’index est limitée et prévisibles. Ceci est crucial car DocumentDB permet le développeur à faire des compromis coût basé entre index aérienne en ce qui concerne les performances des requêtes.  

Consultez les [exemples DocumentDB](https://github.com/Azure/azure-documentdb-net) sur MSDN pour exemples montrant comment configurer la stratégie d’indexation pour une collection de sites. Nous allons maintenant récupérer les détails de la syntaxe de la DocumentDB SQL.


## <a name="basics-of-a-documentdb-sql-query"></a>Concepts de base d’une requête DocumentDB SQL
Chaque requête est constitué par une clause SELECT et FROM facultative la clause WHERE par normes ANSI-SQL. En règle générale, pour chaque requête, la source dans la clause FROM est énumérée. Puis le filtre dans la clause WHERE est appliqué sur la source pour récupérer un sous-ensemble de documents JSON. Enfin, la clause SELECT est utilisée pour les valeurs JSON demandées dans la liste de sélection de projet.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>Clause FROM
La `FROM <from_specification>` clause est facultative, sauf si la source est filtrée ou projetée plus loin dans la requête. L’objectif de cette clause consiste à spécifier la source de données sur lequel la requête doit fonctionner. Généralement l’ensemble de la collection est la source, mais on peut spécifier un sous-ensemble de la collection de sites à la place. 

Une requête comme `SELECT * FROM Families` indique que l’intégralité de la collection familles est la source sur lequel vous souhaitez énumérer. Un identificateur spécial racine peut être utilisé pour représenter la collection au lieu d’utiliser le nom de la collection de sites. La liste suivante contient les règles sont appliquées par requête :

- La collection de sites peut être un alias, tel que `SELECT f.id FROM Families AS f` ou simplement `SELECT f.id FROM Families f`. Ici `f` équivaut à `Families`. `AS`est un mot-clé facultatif à alias l’identificateur.

-   Notez qu’une fois alias, la source d’origine ne peut pas être liée. Par exemple, `SELECT Families.id FROM Families f` est dont la syntaxe est incorrecte dans la mesure où l’identificateur « Familles » ne peut pas être résolu plus.

-   Toutes les propriétés qui doivent être référencées doivent être complets. En l’absence de conformité schéma stricte, cela est appliquée pour éviter les liaisons ambiguës. Par conséquent, `SELECT id FROM Families f` est dont la syntaxe est incorrecte depuis la propriété `id` n’est pas lié.
    
### <a name="sub-documents"></a>Sous-adresse de documents
La source peut également être réduite à un sous-ensemble plus petit. Par exemple, pour l’énumération uniquement une arborescence secondaire dans chaque document, la racine sous-adresse pourrait alors devenir la source, comme le montre l’exemple suivant.

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Tandis que l’exemple ci-dessus utilisé un tableau comme source, un objet pourrait également être utilisé comme source, ce qui correspond à celui présenté dans l’exemple suivant. Toute valeur JSON valide (ne pas non définie) que vous pouvez trouver dans la source est considéré à inclure dans le résultat de la requête. Si vous n’ont pas certaines familles un `address.state` valeur, ils seront exclus dans le résultat de la requête.

**Requête**

    SELECT * 
    FROM Families.address.state

**Résultats**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Clause WHERE
La clause WHERE (**`WHERE <filter_condition>`**) est facultative. Il spécifie les conditions que les documents JSON fournies par la source doivent satisfaire pour être inclus dans le cadre du résultat. N’importe quel document JSON doit correspondre les conditions spécifiées « True » pour prendre en compte pour le résultat. La clause WHERE est utilisée par la couche index afin de déterminer le plus petit sous-ensemble absolu des documents source qui peuvent faire partie du résultat. 

La requête suivante les demandes de documents contenant une propriété nom dont la valeur est `AndersenFamily`. Tout autre document qui ne possède pas de propriété nom, ou l’endroit où la valeur ne correspond pas `AndersenFamily` est exclu. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


L’exemple précédent a montré une requête simple d’égalité. DocumentDB SQL prend également en charge les diverses expressions scalaires. Les plus couramment utilisés sont des expressions binaires et unaire. Références de propriété de l’objet JSON source sont également des expressions valides. 

Les opérateurs binaires suivants sont actuellement pris en charge et peuvent être utilisés dans les requêtes comme indiqué dans les exemples suivants :  
<table>
<tr>
<td>Arithmétique</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Binaire</td>    
<td>|, &, ^, <<, >>, >>> (vers la droite zéro remplissage) </td>
</tr>
<tr>
<td>Logique</td>
<td>ET, OU NON</td>
</tr>
<tr>
<td>Comparaison</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Chaîne</td> 
<td>|| (concaténation)</td>
</tr>
</table>  

Examinons quelques requêtes à l’aide des opérateurs binaires.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Les opérateurs unaire +,-, ~ pas sont également pris en charge et peut être utilisé dans des requêtes comme le montre l’exemple suivant :

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Outre les opérateurs binaire et opérateur unaire, les références de propriété sont également autorisées. Par exemple, `SELECT * FROM Families f WHERE f.isRegistered` renvoie le document JSON contenant la propriété `isRegistered` où la valeur de propriété est égale à la JSON `true` valeur. Toutes les autres valeurs (faux, null, non définie, `<number>`, `<string>`, `<object>`, `<array>`, etc.) permet d’accéder au document source exclu du résultat. 

### <a name="equality-and-comparison-operators"></a>Opérateurs d’égalité et de comparaison
Le tableau suivant affiche le résultat de comparaisons d’égalité dans DocumentDB SQL entre les deux types JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Opérations</strong>
         </td>
         <td valign="top">
            <strong>Non défini</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Valeur booléenne</strong>
         </td>
         <td valign="top">
            <strong>Nombre</strong>
         </td>
         <td valign="top">
            <strong>Chaîne</strong>
         </td>
         <td valign="top">
            <strong>Objet</strong>
         </td>
         <td valign="top">
            <strong>Tableau</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Non défini<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Valeur booléenne<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nombre<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Chaîne<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objet<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
         <td valign="top">
Non défini </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Tableau<strong>
         </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
Non défini </td>
         <td valign="top">
            <strong>Bien</strong>
         </td>
      </tr>
   </tbody>
</table>

Pour les autres opérateurs de comparaison tels que >, > =, ! =, < et < =, les règles suivantes s’appliquent :   

-   Comparaison entre les types de résultat non défini.
-   Comparaison entre deux objets ou deux tableaux non défini des résultats.   

Si le résultat de l’expression scalaire dans le filtre est non défini, le document correspondant ne serait pas inclus dans le résultat, dans la mesure où non défini ne correspondent logiquement « true ».

### <a name="between-keyword"></a>ENTRE les mots clés
Vous pouvez également utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs, comme dans SQL ANSI. ENTRE peut être utilisé par rapport à des chaînes ou des nombres.

Par exemple, cette requête renvoie tous les documents famille dans lequel qualité du premier enfant est comprise entre 1 à 5 (tous deux inclus). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Contrairement dans SQL ANSI, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l’exemple suivant.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Pour le temps d’exécution requête plus rapides, pensez à créer une stratégie d’indexation qui utilise un type d’index plage par rapport aux propriétés numériques/les chemins filtré dans la clause BETWEEN. 

La différence entre l’utilisation de BETWEEN dans DocumentDB et SQL ANSI principale est que vous pouvez exprimer des requêtes de plage par rapport aux propriétés de types différents, par exemple, vous devrez « notes » à être un nombre (5) dans des documents et des chaînes dans d’autres personnes (« grade4 »). Dans ce cas, comme dans JavaScript, une comparaison entre deux types différents résultats dans « non défini » et le document sera ignorée.

### <a name="logical-and-or-and-not-operators"></a>Logique (et, ou et pas) opérateurs
Opérateurs logiques avec les valeurs booléennes. Les tables de vérité logiques de ces transporteurs s’affichent dans les tableaux suivants.

OR|Vrai|Faux|Non défini
---|---|---|---
Vrai|Vrai|Vrai|Vrai
Faux|Vrai|Faux|Non défini
Non défini|Vrai|Non défini|Non défini

ET|Vrai|Faux|Non défini
---|---|---|---
Vrai|Vrai|Faux|Non défini
Faux|Faux|Faux|Faux
Non défini|Non défini|Faux|Non défini

PAS|  |
---|---
Vrai|Faux
Faux|Vrai
Non défini|Non défini

### <a name="in-keyword"></a>Mot clé
Le mot-clé IN peut être utilisé pour vérifier si une valeur spécifiée correspond à une valeur dans une liste. Par exemple, cette requête renvoie tous les documents famille dont l’id est égale à « WakefieldFamily » ou « AndersenFamily ». 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Cet exemple renvoie tous les documents dans laquelle l’état est une des valeurs spécifiées.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternaire ( ?) et les opérateurs de fusion ( ?)
Les opérateurs ternaire et fusion peuvent être utilisés pour créer des expressions conditionnelles, similaires à des langages de programmation courantes tels que c# et JavaScript. 

L’opérateur ternaire ( ?) peut être très pratique lors de la création de nouvelles propriétés de JSON à la volée. Par exemple, vous pouvez maintenant écrire des requêtes pour classer les niveaux de classe dans un format lisible humain comme débutant/intermédiaire/Avancé comme illustré ci-dessous.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Vous pouvez également imbriquer les appels à l’opérateur comme dans la requête ci-dessous.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Comme avec d’autres opérateurs de requête, si les propriétés référencées dans l’expression conditionnelle n’apparaissent pas dans n’importe quel document, ou si les types comparés sont différents, puis ces documents seront exclus dans les résultats de requête.

L’opérateur de fusion ( ?) peut être utilisé efficacement vérifier la présence d’une propriété (également appelé est défini) dans un document. Ceci est utile lorsque vous interrogez contre semi-structurées ou les types de données. Par exemple, cette requête renvoie le cas échéant « nom » ou « nom de famille » si elle n’est pas présenter.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Accesseur de propriété entre guillemets
Vous pouvez également accéder aux propriétés à l’aide de l’opérateur entre guillemets propriété `[]`. Par exemple, `SELECT c.grade` et `SELECT c["grade"]` sont équivalents. La syntaxe suivante est utile lorsque vous avez besoin d’échappement une propriété qui contient des espaces, des caractères spéciaux, ou qui se produit à partager le même nom qu’un mot de passe SQL ou un mot réservé.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Clause SELECT
La clause SELECT (**`SELECT <select_list>`**) est obligatoire et spécifie les valeurs qui seront extraits de la requête, tout comme dans ANSI-SQL. Le sous-ensemble est visible sur les documents d’origine sont transmises à la phase de projection, où les valeurs JSON spécifiées sont récupérés et un nouvel objet JSON est construit, pour chaque entrée passée sur celui-ci. 

L’exemple suivant montre une requête sélection classique. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriétés imbriquées
Dans l’exemple suivant, nous allons projeter deux propriétés imbriquées `f.address.state` et `f.address.city`.

**Requête**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projection prend également en charge les expressions JSON comme le montre l’exemple suivant.

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Nous allons étudier le rôle de `$1` ici. La `SELECT` clause a besoin pour créer un objet JSON et dans la mesure où aucune clé n’est fourni, nous utilisons les noms de variables argument implicite commençant par `$1`. Par exemple, cette requête renvoie les deux variables argument implicite, intitulées `$1` et `$2`.

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Création d’alias
Maintenant nous allons étendre l’exemple ci-dessus avec alias explicites de valeurs. Comme c’est le mot clé utilisé pour alias. Notez qu’il est facultatif comme indiqué lors de la projection de la deuxième valeur en tant que `NameInfo`. 

En cas d’une requête comporte deux propriétés portant le même nom, alias doit être utilisé pour renommer une ou les deux des propriétés afin qu’elles sont désambiguïsés dans le résultat prévu.

**Requête**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressions scalaires
Outre les références de propriété, la clause SELECT prend en charge des expressions scalaires, telles que des constantes, des expressions arithmétiques, expressions logiques, etc.. Par exemple, voici une requête simple « Hello World ».

**Requête**

    SELECT "Hello World"

**Résultats**

    [{
      "$1": "Hello World"
    }]


Voici un exemple plus complexe qui utilise une expression scalaire.

**Requête**

    SELECT ((2 + 11 % 7)-2)/3   

**Résultats**

    [{
      "$1": 1.33333
    }]


Dans l’exemple suivant, le résultat de l’expression scalaire est une valeur booléenne.

**Requête**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Résultats**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Création d’objet et de tableau
Une autre fonction clé de DocumentDB SQL est la création de tableau/objet. Dans l’exemple précédent, notez que nous avons créé un nouvel objet JSON. De même, une pouvez également créer des tableaux comme indiqué dans les exemples suivants.

**Requête**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Résultats**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>VALEUR mot clé
Le mot clé **VALUE** fournit un moyen pour renvoyer la valeur JSON. Par exemple, la requête ci-dessous renvoie la valeur scalaire `"Hello World"` au lieu de `{$1: "Hello World"}`.

**Requête**

    SELECT VALUE "Hello World"

**Résultats**

    [
      "Hello World"
    ]


La requête suivante renvoie la valeur JSON sans le `"address"` étiquette dans les résultats.

**Requête**

    SELECT VALUE f.address
    FROM Families f 

**Résultats**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

L’exemple suivant étend ici pour montrer comment renvoyer les valeurs de JSON (niveau feuille de l’arborescence JSON). 

**Requête**

    SELECT VALUE f.address.state
    FROM Families f 

**Résultats**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Opérateur
L’opérateur spécial (*) est prise en charge pour le document sous forme de projet-est. Lorsqu’il est utilisé, il doit être le seul champ projeté. Tandis que vous une requête comme `SELECT * FROM Families f` est valide, `SELECT VALUE * FROM Families f ` et `SELECT *, f.id FROM Families f ` ne sont pas valides.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Opérateur TOP
Le mot-clé supérieur peut être utilisé pour limiter le nombre de valeurs d’une requête. Lorsque haut est utilisée avec la clause ORDER BY, le jeu de résultats est limité aux N premiers des valeurs ordonnées ; Sinon, elle retourne les N premiers des résultats dans un ordre aléatoire. Pour obtenir les meilleurs résultats, dans une instruction SELECT, utilisez toujours une clause ORDER BY avec la clause TOP. Il s’agit de la seule façon mis indiquer quelles lignes sont affectées en haut. 


**Requête**

    SELECT TOP 1 * 
    FROM Families f 

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

HAUT peut être utilisé avec une valeur constante (comme illustré ci-dessus) ou avec une valeur de la variable à l’aide de requêtes paramétrées. Pour plus d’informations, voir requêtes paramétrées ci-dessous.

## <a name="order-by-clause"></a>Clause ORDER BY
Comme dans SQL ANSI, vous pouvez inclure une clause Order By facultative lors de la requête. La clause peut contenir un argument facultatif croissant/décroissant pour spécifier l’ordre dans lequel les résultats doivent être récupérées. Pour une analyse plus détaillée de Order By, reportez-vous à [DocumentDB ordre par procédure](documentdb-orderby.md).

Par exemple, voici une requête qui Récupère familles dans l’ordre de nom de la ville résident.

**Requête**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Résultats**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

Et Voici une requête qui Récupère familles dans l’ordre de la date de création, qui est stockée comme un nombre qui représente l’origine du temps, c'est-à-dire, temps écoulé depuis le 1er janvier 1970 dans secondes.

**Requête**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Résultats**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Concepts de base de données avancées et requêtes SQL
### <a name="iteration"></a>Itération
Une nouvelle construction a été ajoutée via le mot-clé **IN** dans DocumentDB SQL pour prendre en charge l’itération sur des tableaux JSON. La source de prend en charge pour l’itération. Commençons par l’exemple suivant :

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Maintenant examinons une autre requête qui exécute itération sur enfants dans la collection de sites. Notez la différence dans le tableau de sortie. Cet exemple montre comment fractionner `children` et fusionne les résultats dans un tableau unique.  

**Requête**

    SELECT * 
    FROM c IN Families.children

**Résultats**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Cela peut plus être utilisée pour filtrer sur chaque écriture du tableau comme le montre l’exemple suivant.

**Requête**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Résultats**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Jointures
Dans une base de données relationnel, la nécessité de joindre à travers les tables est très importante. Il s’agit de la logique corollaire conception schémas centrée réduites. En revanche, DocumentDB traite le modèle de données dénormalisées de documents exempt de schéma. Il s’agit de l’équivalent logique d’un « jointure réflexive ».

La syntaxe de la qui prend en charge de la langue est < from_source1 > jointure < from_source2 > jointure... JOINTURE < from_sourceN >. En général, cela renvoie un jeu de **N**- tuples (tuple avec **N** valeurs). Chaque tuple comporte les valeurs générées par une itération de tous les alias de collection de sites sur leurs jeux respectifs. En d’autres termes, il s’agit d’un produit cartésien des jeux de participant à la jointure.

Les exemples suivants illustrent le fonctionne de la clause de jointure. Dans l’exemple suivant, le résultat est vide depuis le produit croisé de chaque document à partir de la source et un ensemble vide est vide.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Résultats**  

    [{
    }]


Dans l’exemple suivant, la jointure est comprise entre la racine du document et le `children` racine sous-adresse. Il est un produit cartésien entre deux objets JSON. Le fait que des enfants est un tableau n’est pas efficace de la jointure étant donné que nous avons affaire à une seule racine se trouvant dans le tableau enfants. Par conséquent, le résultat contienne uniquement deux résultats, étant donné que le produit croisé de chaque document avec le tableau donne exactement qu’un seul document.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


L’exemple suivant montre une jointure plus conventionnelle :

**Requête**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



La première chose à noter est que le `from_source` de la **jointure** clause est un itérateur. Par conséquent, le flux dans ce cas est la suivante :  

-   Développez chaque élément enfant **c** dans le tableau.
-   Appliquer un produit cartésien avec la racine du document **f** avec chaque élément enfant **c** qui a été fusionnés dans la première étape.
-   Pour finir, project la propriété nom racine objet **f** seule. 

Le premier document (`AndersenFamily`) contient un seul élément enfant, de sorte que le jeu de résultats contient un seul objet correspondant à ce document. Le second document (`WakefieldFamily`) contient deux enfants. Par conséquent, le produit croisé génère un objet distinct pour chaque enfant, ce qui entraîne deux objets, un pour chaque enfant correspondant à ce document. Notez que les champs racine dans les deux ces documents sera mêmes, comme vous attendez dans un produit cartésien.

L’utilitaire réel de la jointure consiste à tuples de formulaire dans le produit croisé dans une forme qui se trouve dans le cas contraire difficile de projet. En outre, comme nous verrons dans l’exemple ci-dessous, vous pouvez filtrer sur la combinaison d’un uplet que vous permet de l’utilisateur a choisi une condition satisfaite par les tuples global.

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Résultats**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Cet exemple est une extension naturelle de l’exemple précédent et effectue une jointure double. Par conséquent, le produit cartésien peut être affiché en tant que le pseudo-code suivant.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`a un enfant qui a un animal familier. Par conséquent, le produit croisé donnent une seule ligne (1*1*1) à partir de cette famille. WakefieldFamily a toutefois deux enfants, mais qu’un seul enfant « Jesse » a animaux. Jesse a toutefois 2 animaux. Par conséquent, le produit croisé donne 1*1*2 = 2 lignes à partir de cette famille.

Dans l’exemple suivant, il existe un filtre supplémentaire sur `pet`. Cela exclut tous les tuples où le nom de compagnie n’est pas « Ombre ». Notez que nous sont en mesure de générer de tuples à partir des tableaux, filtre sur un des éléments du tuple et n’importe quelle combinaison des éléments de projet. 

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Résultats**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Intégration JavaScript
DocumentDB fournit un modèle de programmation pour exécuter logique JavaScript, en fonction de l’application directement sur les collections en ce qui concerne les procédures stockées et des déclencheurs. Ceci permet les deux :

-   Possibilité d’effectuer les opérations de haute performance transactions et requêtes sur des documents dans une collection de sites en raison de l’intégration de runtime JavaScript directement dans le moteur de base de données. 
-   Modélisation de naturelle de flux de contrôle, variable étendue et l’attribution et l’intégration des exceptions primitives avec les transactions de base de données. Pour plus d’informations sur DocumentDB prises en charge pour l’intégration JavaScript, reportez-vous à la documentation de programmabilité JavaScript server côté.

###<a name="user-defined-functions-udfs"></a>Les fonctions définies par l’utilisateur
Ainsi que des types déjà définis dans cet article, DocumentDB SQL prend en charge pour le fonctions définies par l’utilisateur (UDF). En particulier, UDF scalaires sont prises en charge dans lequel les développeurs peuvent passer zéro ou plusieurs arguments et retourner le résultat argument unique précédent. Chacun de ces arguments sont activées pour les valeurs JSON juridique.  

La syntaxe de la DocumentDB SQL est étendu pour prendre en charge de la logique de l’application personnalisé à l’aide de ces fonctions définies par l’utilisateur. UDF peuvent être enregistrés avec DocumentDB et puis être référencées dans le cadre d’une requête SQL. En fait, les UDF sont ordinateurs destinés à être appelé par les requêtes. En corollaire à ce choix, UDF n’ont pas accès à l’objet de contexte qui ont les autres types de JavaScript (procédures stockées et déclencheurs). Dans la mesure où exécutent des requêtes en lecture seule, ils peuvent exécuter sur principal ou sur les doubles secondaires. Par conséquent, UDF sont conçus pour s’exécuter sur les doubles secondaires Contrairement à d’autres types JavaScript.

Voici un exemple de la façon dont un fichier UDF peut être enregistré à la base de données DocumentDB, en particulier sous une collection de documents.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
L’exemple précédent crée un fichier UDF dont le nom est `REGEX_MATCH`. Elle accepte les deux valeurs de chaîne JSON `input` et `pattern` et vérifie si la première correspond au modèle spécifié dans la seconde en utilisant String.Match () fonction du JavaScript.


Nous pouvons à présent utiliser ce UDF dans une requête dans une projection. UDF doivent être qualifiés par le préfixe sensible à la casse « udf. » Lorsqu’elle est appelée à partir de dans les requêtes. 

>[AZURE.NOTE] Avant le 3/17/2015, DocumentDB pris en charge UDF appels sans « udf. » préfixe tels que REGEX_MATCH() sélectionnez. Ce modèle appelant a été déconseillé.  

**Requête**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Résultats**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Le fichier UDF peut également servir à l’intérieur d’un filtre comme le montre l’exemple ci-dessous, également qualifiées avec le fichier « udf. » préfixe :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Résultats**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


En résumé, UDF sont des expressions scalaires valides et peuvent être utilisés dans les filtres et les prévisions. 

Pour développer la puissance d’UDF, nous allons étudier un autre exemple avec la logique conditionnelle :

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Voici un exemple qui teste l’UDF.

**Requête**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Résultats**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Comme les exemples précédents montrent, UDF intégrant la puissance de langage JavaScript DocumentDB SQL pour fournir une interface programmable riche pour effectuer une logique conditionnelle, procédurale complexe à l’aide des fonctionnalités de runtime JavaScript intégrées.

DocumentDB SQL fournit les arguments aux UDF pour chaque document dans la source à l’étape actuelle (clause WHERE ou sélectionnez) de traitement du fichier UDF. Le résultat est incorporé dans le pipeline de l’exécution générale en toute transparence. Si les propriétés auquel par le fichier UDF paramètres ne sont pas disponibles dans la valeur JSON, le paramètre est considéré comme non défini et par conséquent, l’appel UDF est entièrement ignoré. De même si le résultat de l’UDF n’est pas défini, il n’est pas inclus dans le résultat. 

En résumé, UDF sont des outils puissants pour effectuer une logique métier complexe dans le cadre de la requête.

### <a name="operator-evaluation"></a>Évaluation d’opérateur
DocumentDB, en la raison d’être une base de données JSON, dessine parallèles avec des opérateurs JavaScript et sa sémantique d’évaluation. Tandis que DocumentDB essaie de préserver la sémantique JavaScript en termes de prise en charge JSON, l’évaluation opération écart et dans certains cas.

Dans DocumentDB SQL, contrairement dans SQL classique, les types de valeurs sont souvent pas connues jusqu'à ce que les valeurs sont réellement récupérées à partir de la base de données. Pour exécuter des requêtes efficacement, la plupart des opérateurs ont des exigences de type stricte. 

DocumentDB SQL n’effectue pas les conversions implicites, contrairement à JavaScript. Par exemple, une requête comme `SELECT * FROM Person p WHERE p.Age = 21` correspond à des documents qui contiennent une propriété Age dont la valeur est 21. Tout autre document dont la propriété âge correspond variantes potentiellement illimité chaîne « 21 » ou autres telles que « 021 », « 21.0 », « 0021 », « 00021 », etc. ne sera pas trouvé. Il s’agit en revanche pour le code JavaScript où se trouvent convertis implicitement en nombres les valeurs de chaîne (basée sur opérateur, ex : ==). Cette option est cruciale pour index efficace correspondantes dans DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>Requêtes SQL paramétrées
DocumentDB prend en charge les requêtes avec paramètres exprimés avec la familiers @ notation. SQL paramétrée fournit gestion robuste et d’échappement entrée de l’utilisateur, empêchant accidentelle exposition des données par le biais d’injection SQL. 

Par exemple, vous pouvez écrire une requête qui utilise le nom et l’état de l’adresse en tant que paramètres et puis exécutez-le pour différentes valeurs de nom et adresse état basée sur les informations utilisateur.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Cette requête puis envoyée à DocumentDB comme une requête paramétrée JSON comme illustré ci-dessous.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

L’argument vers haut peut être défini à l’aide de requêtes paramétrées comme illustré ci-dessous.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Les valeurs de paramètre peuvent être n’importe quel JSON valide (chaînes, nombres, booléens, null, et même des matrices ou imbriquée JSON). Également DocumentDB étant sans schéma, les paramètres ne sont pas validées dans n’importe quel type.

##<a name="built-in-functions"></a>Fonctions intégrées
DocumentDB prend également en charge un nombre de fonctions intégrées pour des opérations courantes qui peuvent être utilisées à l’intérieur de requêtes tels que les fonctions définies par l’utilisateur (UDF).

<table>
<tr>
<td>Fonctions mathématiques</td> 
<td>ABS, plafond, EXP, plancher, LOG, LOG10, POWER, arrondi, se, racine, carrée, tronque, ACOS, ASIN, ATAN, ATN2, COS, COT, degrés, PI, RADIANS, SIN et TAN</td>
</tr>
<tr>
<td>Fonctions de vérification de type</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED et IS_PRIMITIVE</td>
</tr>
<tr>
<td>Fonctions de chaîne</td>   
<td>CONCAT, CONTAINS, ENDSWITH, INDEX_OF, gauche, longueur, inférieur, LTRIM, remplacer, répliquer, inverse, droite, RTRIM, STARTSWITH, sous-chaîne et supérieur</td>
</tr>
<tr>
<td>Fonctions de tableau</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH et ARRAY_SLICE</td>
</tr>
<tr>
<td>Fonctions spatiales</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID et ST_ISVALIDDETAILED</td>
</tr>
</table>  

Si vous utilisez actuellement une fonction définie par l’utilisateur (UDF) dont une fonction prédéfinie est désormais disponible, vous devez utiliser la fonction intégrée correspondante comme il va agir en plus rapides à exécuter et plus efficacement. 

### <a name="mathematical-functions"></a>Fonctions mathématiques
Les fonctions mathématiques chaque effectuent un calcul, généralement basé sur les valeurs d’entrée sont fournies comme arguments et renvoient une valeur numérique. Voici un tableau des fonctions mathématiques intégrés pris en charge.

<table>
<tr>
<td><strong>Utilisation</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Renvoie la valeur absolue (positive) de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">PLAFOND (num_expr)</a></td> 
<td>Renvoie la plus petite valeur d’entier supérieur ou égal à l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">PLANCHER (num_expr)</a></td> 
<td>Renvoie le plus grand entier inférieur ou égal à l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Renvoie l’exposant de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Renvoie le logarithme népérien de l’expression numérique spécifiée, soit le logarithme à l’aide de la base spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Renvoie la valeur logarithmique base 10 de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td> 
<td>Renvoie une valeur numérique, arrondie au nombre entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">Tronque (num_expr)</a></td> 
<td>Renvoie une valeur numérique, tronquée au nombre entier le plus proche.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">RACINE (num_expr)</a></td>   
<td>Renvoie la racine carrée de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">CARRÉ (num_expr)</a></td>   
<td>Retourne le carré de l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">PUISSANCE (num_expr, num_expr)</a></td>   
<td>Retourne la puissance de l’expression numérique spécifiée à la valeur spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGNE (num_expr)</a></td>   
<td>Renvoie la valeur se (-1, 0, 1) de l’expression numérique spécifiée.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>Renvoie l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelé arccosinus d’un nombre.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>   
<td>Renvoie l’angle en radians, dont le sinus est l’expression numérique spécifiée. Cette option est également appelée arcsinus.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>Renvoie l’angle en radians, dont la tangente est l’expression numérique spécifiée. Cette option est également appelée arctangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Renvoie l’angle en radians, entre l’axe des abscisses positifs et le rayon depuis l’origine du point (y, x), où x et y sont les valeurs des deux expressions flottante spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Renvoie le cosinus trigonométrique de l’angle spécifié en radians, dans l’expression spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Renvoie la cotangente trigonométrique de l’angle spécifié en radians, dans l’expression numérique spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">DEGRÉS (num_expr)</a></td> 
<td>Renvoie l’angle correspondant en degrés pour un angle spécifié en radians.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Renvoie la valeur constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANS (num_expr)</a></td> 
<td>Renvoie les radians lorsqu’une expression numérique, en degrés, est entrée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td> 
<td>Renvoie le sinus trigonométrique de l’angle spécifié en radians, dans l’expression spécifiée.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Renvoie la tangente de l’expression d’entrée, dans l’expression spécifiée.</td>
</tr>

</table> 

Par exemple, vous pouvez maintenant exécuter des requêtes à ce qui suit :

**Requête**

    SELECT VALUE ABS(-4)

**Résultats**

    [4]

La différence entre les fonctions de DocumentDB par rapport à SQL ANSI principale est qu’ils sont conçus pour fonctionner avec les données de schéma sans schéma et mixtes. Par exemple, si vous disposez d’un document dans lequel la propriété taille est manquant ou a une valeur non numérique comme « inconnue », puis le document est ignoré à droite, au lieu de renvoyer un message d’erreur.

### <a name="type-checking-functions"></a>Fonctions de vérification de type
Les fonctions de la vérification de type permettent de vérifier le type d’une expression dans les requêtes SQL. Fonctions de la vérification de type peuvent être utilisées pour déterminer le type de propriétés dans les documents à la volée lorsqu’elle est variable ou inconnu. Voici un tableau de type intégré pris en charge la vérification des fonctions.

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est un tableau.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est une valeur booléenne.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Renvoie une valeur booléenne qui indique si le type de la valeur est null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est un nombre.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est un objet JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est une chaîne.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si une valeur a été affectée à la propriété.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Renvoie une valeur booléenne indiquant si le type de la valeur est une chaîne, nombre, booléen ou la valeur null.</td>
</tr>

</table>

À l’aide de ces fonctions, vous pouvez maintenant exécuter des requêtes à ce qui suit :

**Requête**

    SELECT VALUE IS_NUMBER(-4)

**Résultats**

    [true]

### <a name="string-functions"></a>Fonctions de chaîne
Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et renvoyer une chaîne, la valeur numérique ou booléenne. Voici un tableau des fonctions de chaîne intégrées :

Utilisation|Description
---|---
[LONGUEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Renvoie le nombre de caractères de l’expression de chaîne spécifiée
[CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Renvoie une chaîne qui est le résultat de concaténation de deux ou plusieurs valeurs de chaîne.
[SOUS-chaîne (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Renvoie une partie d’une expression de chaîne.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Renvoie une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Renvoie une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde
[CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Renvoie une valeur booléenne indiquant si la première expression de chaîne contient la seconde.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Renvoie la position de départ de la première occurrence de la deuxième chaîne expression dans la première expression de chaîne spécifiée, ou -1 si la chaîne est introuvable.
[GAUCHE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Renvoie la partie gauche d’une chaîne avec le nombre spécifié de caractères.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Renvoie la partie droite d’une chaîne avec le nombre spécifié de caractères.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Retourne une expression de chaîne après avoir supprimé des espaces à gauche.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Retourne une expression de chaîne après avoir tronqué tous les espaces.
[INFÉRIEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Retourne une expression de chaîne après avoir converti les données de caractères en majuscules en minuscules.
[SUPÉRIEUR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Retourne une expression de chaîne après avoir converti les données en caractères minuscules en majuscules.
[Remplacer (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne.
[Dupliquer (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Répète une valeur de chaîne un nombre spécifié de fois.
[INVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Renvoie l’ordre inverse d’une valeur de chaîne.

À l’aide de ces fonctions, vous pouvez maintenant exécuter des requêtes à ce qui suit. Par exemple, vous pouvez retourner le nom de famille en majuscules comme suit :

**Requête**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Résultats**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou concaténer des chaînes, comme dans cet exemple :

**Requête**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Résultats**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Fonctions de chaîne peuvent également être utilisées dans la clause WHERE pour filtrer les résultats, comme dans l’exemple suivant :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Résultats**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Fonctions de tableau
Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée du tableau et la retour numérique, la valeur booléenne ou un tableau. Voici un tableau des fonctions de tableau intégré :

Utilisation|Description
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Renvoie le nombre d’éléments de l’expression de tableau spécifié.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Renvoie une matrice qui est le résultat de concaténation de deux ou plusieurs valeurs du tableau.
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Renvoie une valeur booléenne qui indique si le tableau contient la valeur spécifiée.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Renvoie une partie d’une expression de tableau.

Fonctions de tableau peuvent être utilisées pour manipuler des tableaux dans JSON. Par exemple, voici une requête qui renvoie tous les documents où un des parents est « Robin Wakefield ». 

**Requête**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Voici un autre exemple qui utilise ARRAY_LENGTH pour obtenir le nombre d’enfants par famille.

**Requête**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Résultats**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Fonctions spatiales

DocumentDB prend en charge les fonctions intégrées ouvrir géospatiales Consortium (OGC) suivantes pour interroger géospatiales. Pour plus d’informations sur géospatiales prise en charge dans DocumentDB, voir [utilisation des données géospatiales dans Azure DocumentDB](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux expressions point GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Retourne une expression booléenne qui indique si le point de GeoJSON spécifié dans le premier argument est intérieur du polygone GeoJSON dans le deuxième argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Renvoie une valeur booléenne indiquant si l’expression de point ou polygone GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie la valeur d’une valeur JSON qui contient une valeur booléenne si l’expression de point ou polygone GeoJSON spécifiée est valide et si non valide, plus la raison pour laquelle comme une valeur de chaîne.</td>
</tr>
</table>

Fonctions spatiales peuvent être utilisées pour effectuer querries proximité par rapport aux données spatiales. Par exemple, voici une requête qui renvoie tous les documents famille situés à 30 kilomètres de l’emplacement spécifié à l’aide de la fonction intégrée ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis « requêtes distance » seront servis efficacement dans l’index. Pour plus d’informations sur l’indexation spatiale, voir la section ci-dessous. Si vous n’avez pas un index spatial pour les chemins d’accès spécifiés, vous pouvez toujours effectuer des recherches spatiales en spécifiant `x-ms-documentdb-query-enable-scan` en-tête de requête avec la valeur définie sur « true ». Dans .NET, il est possible en passant l’argument facultatif **FeedOptions** requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) défini sur true. 

ST_WITHIN peut être utilisé pour vérifier si un point se trouve dans un polygone. Polygone est fréquemment utilisées pour représenter des limites, comme les codes postaux, les frontières des États ou des formations Natural Keyboard. Nouveau si vous incluez une indexation spatiale dans votre stratégie d’indexation, puis requêtes « within » seront servis efficacement dans l’index. 

Arguments polygone dans ST_WITHIN peuvent contenir uniquement une sonnerie simple, c'est-à-dire polygone ne doit pas contenir holes qu’ils. Vérifier les [limites de DocumentDB](documentdb-limits.md) pour le nombre maximal de points autorisée dans un polygone pour une requête ST_WITHIN.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Similaire à qui ne correspondent pas comment fonctionne types dans une requête DocumentDB, si la valeur de l’emplacement spécifié dans l’argument est incorrecte ou non valide, puis il prend la valeur **non défini** et le document évalué sont ignorés dans les résultats de requête. Si votre requête ne renvoie aucun résultat, exécutez ST_ISVALIDDETAILED à déboguer pourquoi le type de spatail n’est pas valide.     

ST_ISVALID et ST_ISVALIDDETAILED peuvent être utilisés pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d’un point avec une valeur hors limites latitude (-132.8). ST_ISVALID renvoie une valeur booléenne et ST_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne contenant la raison pour laquelle il est non valide.

**Requête**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Ces fonctions peuvent également être utilisées pour valider polygone. Par exemple, ici nous utilisons ST_ISVALIDDETAILED pour valider un polygone qui n’est pas fermé. 

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Résultats**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Qui renvoie les fonctions spatiales et la syntaxe SQL pour DocumentDB. Maintenant examinons à présent comment LINQ interroger works et interaction avec la syntaxe de la nous l’avons vu jusqu'à présent.

## <a name="linq-to-documentdb-sql"></a>LINQ à SQL DocumentDB
LINQ est un modèle de programmation .NET qui exprime calcul sous forme de requêtes sur les flux d’objets. DocumentDB fournit un client bibliothèque côté interface avec LINQ en facilitant une conversion entre des objets .NET et JSON et un mappage à partir d’un sous-ensemble de LINQ requêtes DocumentDB requêtes. 

L’image ci-dessous montre l’architecture de prise en charge des requêtes LINQ à l’aide de DocumentDB.  Le client DocumentDB, aux développeurs de créer un objet **IQueryable** que le fournisseur de requête DocumentDB qui puis se traduit par la requête LINQ dans une requête DocumentDB directement des requêtes. La requête est ensuite passée au serveur DocumentDB pour récupérer un jeu de résultats au format JSON. Les résultats renvoyés sont désérialisés dans un flux d’objets .NET côté client.

![Architecture de prise en charge des requêtes LINQ à l’aide de DocumentDB - syntaxe SQL, langage de requête JSON, concepts de base de données et requêtes SQL][1]
 


### <a name="net-and-json-mapping"></a>.NET et JSON mappage
Le mappage entre les objets .NET et les documents JSON est naturel - chaque champ de membre de données est mappé à un objet JSON, où le nom du champ est mappé au composant « clé » de l’objet et le composant « valeur » est mappé à la partie de la valeur de l’objet de manière récursive. Prenons l’exemple suivant. L’objet famille créé est mappé au document JSON comme illustré ci-dessous. Et inversement, le document JSON est mappé à un objet .NET.

**Classe c#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ traduction SQL
Le fournisseur de requêtes DocumentDB effectue un mappage effort optimal à partir d’une requête LINQ dans une requête DocumentDB SQL. Dans la description suivante, nous part du principe que le lecteur possède une connaissance de base de LINQ.

Tout d’abord, pour le système de type, nous prend en charge tous les types primitifs JSON – types numériques, booléen, chaîne et null. Uniquement ces types JSON sont prises en charge. Les expressions scalaires suivantes sont prises en charge.

-   Les valeurs de constante – ces inclut les valeurs de constante des types de données primitifs au moment la requête est évaluée.

-   Tableau de la propriété index expressions – ces expressions font référence à la propriété d’un objet ou un élément de tableau.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Expressions arithmétiques - ces incluent des expressions arithmétiques courantes sur les valeurs numériques et booléens. Pour obtenir la liste complète, reportez-vous à la spécification SQL.

        2 * family.children[0].grade;
        x + y;

-   Expression de comparaison de chaîne - ces incluent la comparaison d’une valeur de chaîne à une valeur de chaîne constante.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Objet/tableau la création d’une expression - ces expressions retour un objet de type de valeur composée ou anonyme ou une matrice de ces objets. Les valeurs suivantes peuvent être imbriquées.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Liste des opérateurs LINQ pris en charge
Voici une liste des opérateurs LINQ pris en charge dans le fournisseur LINQ inclus avec le Kit de développement .NET DocumentDB.

-   **Sélectionnez**: Projections traduisent à la requête SQL SELECT, y compris de construction d’un objet
-   **Où**: traduire en SQL où les filtres et prend en charge de la traduction entre & &, || et ! pour les opérateurs SQL
-   **SelectMany**: permet de déroulement de tableaux à la clause SQL JOIN. Peut être utilisé pour chaîne/imbriquer des expressions pour filtrer les éléments du tableau
-   **OrderBy et OrderByDescending**: devient par ordre croissant ou décroissant :
-   **CompareTo**: devient comparaisons de plage. Fréquemment utilisées pour les chaînes car ils ne sont pas comparables dans .NET
-   **Prendre**: devient haut pour limiter les résultats d’une requête SQL
-   **Fonctions mathématiques**: prend en charge la traduction à partir de. Abs, Acos, Asin de filet, Atan, plafond, Cos, Exp, plancher, journal, Log10, Pow, arrondi, se, Sin, Sqrt, Tan, tronquer pour les fonctions intégrées SQL équivalentes.
-   **Fonctions de chaîne**: prend en charge la traduction à partir de. Concat, Contains, EndsWith de filet, IndexOf, Nb, ToLower, TrimStart, remplacer, inverse, TrimEnd, StartsWith, sous-chaîne, ToUpper pour les fonctions intégrées SQL équivalentes.
-   **Fonctions de tableau**: prend en charge la traduction à partir de. De filet Concat contient et NB pour les fonctions intégrées SQL équivalentes.
-   **Fonctions d’Extension géospatiales**: prend en charge de traduction de méthodes stub Distance, dans, IsValid et IsValidDetailed vers les fonctions intégrées SQL équivalentes.
-   **Fonction de l’Extension de fonction définie par l’utilisateur**: fonction définie par le traduction prend en charge de la méthode stub UserDefinedFunctionProvider.Invoke vers l’utilisateur correspondant.
-   **Divers**: prend en charge de traduction de la fusion et d’opérateurs conditionnelle. Traduire Contains chaîne contient, ARRAY_CONTAINS ou IN SQL en fonction du contexte.

### <a name="sql-query-operators"></a>Opérateurs de requête SQL
Voici quelques exemples qui illustrent comment certaines des opérateurs de requête LINQ standards sont convertis vers le bas jusqu'à DocumentDB requêtes.

#### <a name="select-operator"></a>Sélectionnez opérateur
La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Expression lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expression lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expression lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Opérateur SelectMany
La syntaxe est `input.SelectMany(x => f(x))`, où `f` est une expression scalaire qui renvoie un type de collection de sites.

**Expression lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Où opérateur
La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui renvoie une valeur booléenne.

**Expression lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expression lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Requêtes SQL composite
Les opérateurs ci-dessus peuvent être composés pour former des requêtes plus puissantes. Dans la mesure où DocumentDB prend en charge des collections imbriquées, la composition peut être concaténée ou imbriquée.

#### <a name="concatenation"></a>Concaténation 

La syntaxe est `input(.|.SelectMany())(.Select()|.Where())*`. Une requête concaténée pouvez commencer avec un facultatif `SelectMany` requête suivie de multiple `Select` ou `Where` opérateurs.


**Expression lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expression lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expression lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expression lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Imbrication

La syntaxe est `input.SelectMany(x=>x.Q())` où Q est un `Select`, `SelectMany`, ou `Where` opérateur.

Dans une requête imbriquée, la requête interne est appliquée à chaque élément de la collection externe. Une fonctionnalité importante est que la requête interne peut faire référence aux champs des éléments de la collection externe comme les jointures réflexives.

**Expression lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expression lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expression lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>L’exécution des requêtes SQL
DocumentDB expose ressources via une API REST qui peut être appelé par n’importe quelle langue capable de procéder à des demandes HTTP/HTTPS. En outre, DocumentDB fournit des bibliothèques de programmation pour plusieurs langues courantes, tels que .NET et Node.js, JavaScript Python. L’API REST et les différentes bibliothèques tout prend en charge l’interrogation au moyen de SQL. Le Kit de développement .NET prend en charge LINQ interroger outre SQL.

Les exemples ci-dessous illustrent comment créer une requête et envoyez-la par rapport à un compte de base de données DocumentDB.

### <a name="rest-api"></a>API REST
DocumentDB offre un modèle de programmation RESTful ouvert sur HTTP. Comptes de base de données peuvent être mis en service à l’aide d’un abonnement Azure. Le modèle de ressources DocumentDB comprend un ensembles de ressources sous un compte de base de données, chacun d'entre eux est dédié à l’aide d’un URI stable et logique. Un ensemble de ressources est appelé un flux dans ce document. Un compte de base de données est constitué d’un ensemble de bases de données, chacun contenant plusieurs collections de sites, chacun du activer contiennent des documents, UDF et autres types de ressources.

Le modèle de base interaction avec les ressources consiste aux verbes HTTP GET, place, publier et supprimer avec leur interprétation standard. Le verbe billet est utilisé pour la création d’une nouvelle ressource, l’exécution d’une procédure stockée ou émission d’une requête DocumentDB. Requêtes sont toujours lus uniquement les opérations sans effets secondaires.

Les exemples ci-dessous illustrent une publication d’une requête DocumentDB effectuée par rapport à une collection de sites contenant les deux documents exemple que nous avons examiné jusqu'à présent. La requête a un filtre simple sur la propriété name JSON. Notez l’utilisation de la `x-ms-documentdb-isquery` et Type de contenu : `application/query+json` en-têtes pour indiquer que l’opération est une requête.


**Demande**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Le second montre une requête plus complexe qui retourne plusieurs résultats à partir de la jointure.

**Demande**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Si les résultats d’une requête ne tient pas dans une seule page de résultats, puis l’API REST renvoie un jeton de continuation via la `x-ms-continuation-token` en-tête de réponse. Les clients peuvent pagination résultats en incluant l’en-tête dans les résultats suivants. Le nombre de résultats par page peut également être contrôlé via la `x-ms-max-item-count` en-tête nombre.

Pour gérer la stratégie de la cohérence des données pour les requêtes, utilisez le `x-ms-consistency-level` en-tête comme toutes les demandes de l’API REST. Pour garantir la cohérence de session, il est nécessaire pour un écho également plus tard `x-ms-session-token` en-tête des cookies dans la demande de requête. Notez que politique d’indexation de la collection interrogée peut influencer également la cohérence des résultats de la requête. Avec la valeur par défaut des paramètres de stratégie d’indexation, pour des collections de l’index est toujours en cours avec le contenu du document et les résultats de requête correspondent à la cohérence choisie pour les données. Si la stratégie d’indexation est stricte pour lent, puis requêtes peuvent retourner des résultats obsolètes. Pour plus d’informations, reportez-vous à [Niveaux de cohérence DocumentDB] [consistency-levels].

Si la stratégie d’indexation configurée sur la collection de sites ne reconnaît pas la requête spécifiée, le serveur DocumentDB renvoie 400 « demande incorrecte ». Il est renvoyé pour les requêtes de plage sur chemins d’accès configurés pour les recherches hachage (égalité) et des chemins d’accès explicitement exclues de l’indexation. La `x-ms-documentdb-query-enable-scan` en-tête peut être défini pour recevoir la requête effectuer une analyse lorsqu’un index n’est pas disponible.

### <a name="c-net-sdk"></a>KIT DE DÉVELOPPEMENT LOGICIEL C# (.NET)
Le Kit de développement .NET prend en charge LINQ et SQL interroger. L’exemple suivant montre comment effectuer la requête de filtre simple présentée précédemment dans ce document.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Cet exemple compare deux propriétés sont égales dans chaque document et utilise projections anonymes. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


L’exemple suivant montre jointures, exprimés via LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Le client .NET parcourt automatiquement toutes les pages de résultats de la requête dans les blocs foreach comme indiqué ci-dessus. Les options de requête introduites dans la section API REST sont également disponibles dans le Kit de développement .NET à l’aide du `FeedOptions` et `FeedResponse` cours dans la méthode CreateDocumentQuery. Le nombre de pages peut être contrôlé à l’aide de la `MaxItemCount` paramètre. 

Vous pouvez également explicitement contrôler la pagination en créant `IDocumentQueryable` à l’aide de la `IQueryable` objet, puis en lisant la` ResponseContinuationToken` valeurs et en les passant sauvegarder dans le cadre `RequestContinuationToken` dans `FeedOptions`. `EnableScanInQuery`peut être défini à activer les analyses lors de la requête ne peut pas être pris en charge par la stratégie d’indexation configurée. Pour les collections partitionnées, vous pouvez utiliser `PartitionKey` pour exécuter la requête sur une seule partition (même si DocumentDB pouvez cela extraire automatiquement le texte de requête), et `EnableCrossPartitionQuery` pour exécuter des requêtes devant être exécutées avec plusieurs partitions. 

Pour plus d’échantillons contenant des requêtes, voir [exemples .NET DocumentDB](https://github.com/Azure/azure-documentdb-net) . 

### <a name="javascript-server-side-api"></a>API JavaScript côté serveur 
DocumentDB fournit un modèle de programmation pour exécuter logique JavaScript, en fonction de l’application directement sur les collections à l’aide des déclencheurs et des procédures stockées. La logique JavaScript inscrite à un niveau de collection de sites peut alors émettre des opérations de base de données sur les opérations sur les documents de la collection de sites donnée. Ces opérations sont de sport avec ambiance transactions ACID.

L’exemple suivant montre comment utiliser le queryDocuments dans l’API JavaScript du serveur pour créer des requêtes à partir de l’intérieur de procédures stockées et déclencheurs.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Fonctions d’agrégation

Prise en charge native pour les fonctions d’agrégation est riches du programme works, mais si vous avez besoin de compter ou additionner les fonctionnalités dans l’intervalle, vous pouvez obtenir le même résultat à l’aide de différentes méthodes.  

Sur le chemin d’accès en lecture :

- Vous pouvez effectuer des fonctions d’agrégation en récupérant les données et en effectuant un compteur localement. Il est conseillé d’utiliser une projection de requête bon comme `SELECT VALUE 1` plutôt que de complète pour le document comme `SELECT * FROM c`. Cela permet d’augmenter le nombre de documents traités dans chaque page de résultats, évitant aller-retour supplémentaires au service si nécessaire.
- Vous pouvez également utiliser une procédure stockée pour réduire la latence du réseau de répétition de boucles. Pour un exemple de procédure stockée qui calcule le nombre d’une requête de filtre donné, voir [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). La procédure stockée permet aux utilisateurs de combiner la logique métier enrichi ainsi qu’effectuant des agrégations de façon efficace.

Sur le chemin d’accès de l’écriture :

- Un autre modèle commun consiste à agréger avant les résultats dans le chemin d’accès « écriture ». Il s’agit particulièrement attrayant lorsque le volume de requêtes « lire » est supérieur à celui des demandes « rédiger ». Une fois préalable agrégé, les résultats sont disponibles avec un point unique demande de lecture.  La meilleure façon de pré-mise agréger dans DocumentDB consiste à configurer un déclencheur qui est appelé avec chaque « écriture » et mettre à jour d’un document de métadonnées qui comporte les derniers résultats de la requête qui est en cours réalisée. Par exemple, regardez l’échantillon [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) qui met à jour les minSize, maxSize et totalSize du document pour la collection de métadonnées. L’exemple peut être prolongée pour mettre à jour d’un compteur, somme, etc..

##<a name="references"></a>Références
1.  [Présentation des DocumentDB Azure][introduction]
2.  [Spécification DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Exemples de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
4.  [Niveaux de cohérence DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Spécification JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Interroger des techniques d’évaluation pour des bases de données [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Requête traitement dans des systèmes de base de données relationnelle en parallèle, appuyez sur la société d’ordinateur IEEE, 1994
11. Lu, Ooi, Tan, traitement dans des systèmes de base de données relationnelle en parallèle, appuyez sur la société de IEEE ordinateur, 1994 des requêtes.
12. Christophe Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins : Latin cochon : une langue non étrangère pour traitement de données, SIGMOD 2008.
13.     G. Graefe. L’enveloppe Cascades pour l’optimisation des requêtes. Données IEEE ENG Bull., 18, paragraphe 3 : 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
