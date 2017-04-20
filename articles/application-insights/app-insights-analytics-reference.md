<properties 
    pageTitle="Référence dans Analytique dans perspectives Application | Microsoft Azure" 
    description="Référence des instructions dans Analytique, l’outil recherche puissant d’analyse de l’Application. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Référence pour Analytique

[Analytique](app-insights-analytics.md) est la fonctionnalité de recherche puissant [d’Analyse de l’Application](app-insights-overview.md). Ces pages décrivent le langage de requête Analytique.

> [AZURE.NOTE] [Testez Analytique dans nos données simulées](https://analytics.applicationinsights.io/demo) si votre application n’est pas envoyer des données Application analyse encore.

## <a name="index"></a>Index


**Laisser** [laisser](#let-clause)


**Opérateurs et des requêtes** [nombre](#count-operator)  |  [évaluer](#evaluate-operator) | [étendre](#extend-operator) | [jointure](#join-operator) | [limite](#limit-operator) | [mvexpand](#mvexpand-operator) | [analyser](#parse-operator) | [projet](#project-operator) | [projet absent](#project-away-operator) | [plage](#range-operator) | [réduire](#reduce-operator) | [rendu directive](#render-directive) | [restreindre la clause](#restrict-clause) | [tri](#sort-operator) | [synthétiser](#summarize-operator) | [prendre](#take-operator) | [haut](#top-operator) | [imbriqués haut](#top-nested-operator) | [union](#union-operator) | [où](#where-operator) | [dans l’emplacement](#where-in-operator)

**Agrégations** [tout](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [Nb.Si](#countif) | [BDNB](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [centile](#percentile) | [centiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [ECARTYPE](#stdev) | [somme](#sum) | [variation](#variance)

**Scalaires** [Littéraux booléens](#boolean-literals)  |  [Opérateurs booléens](#boolean-operators) | [lumières](#casts) | [comparaisons scalaires](#scalar-comparisons) | [gettype](#gettype) | [hachage](#hash) | [données](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [NOT NULL](#notnull) | [toscalar](#toscalar)

**Numéros** [Opérateurs arithmétiques](#arithmetic-operators)  |  [Littéraux numériques](#numeric-literals) | [abs](#abs) | [Corbeille](#bin) | [exp](#exp) | [plancher](#floor) | [gamma](#gamma) | [journal](#log) | [rand](#rand) | [racine](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Date et heure** [Date et heure expressions](#date-and-time-expressions)  |  [Date et d’heure](#date-and-time-literals) | [il y a](#ago) | [PartDate](#datepart) | [dayofmonth](#dayofmonth) | [jour de la semaine](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [maintenant](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Chaîne** [GUID](#guids)  |  [Littéraux de chaîne brouillé](#obfuscated-string-literals) | [Littéraux de chaîne](#string-literals) | [des comparaisons de chaînes](#string-comparisons) | [countof](#countof) | [Extraire](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [Remplacer](#replace) | [Fractionner](#split) | [strcat](#strcat) | [strlen](#strlen) | [sous-chaîne](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Tableaux, objets et dynamique** [Littéraux Array et object](#array-and-object-literals)  |  [Fonctions objet dynamique](#dynamic-object-functions) | [objets dynamiques dans des clauses laisser](#dynamic-objects-in-let-clauses) | [chemin d’accès JSON expressions](#json-path-expressions) | [noms](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [plage](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Laisser

### <a name="let-clause"></a>laisser clause

**Tabulaire laisser - nommant une table**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Valeur scalaire laisser - une valeur**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda laisser - dénomination une fonction**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Une clause permettent lie un [nom](#names) à une table de résultats, valeur scalaire ou une fonction. La clause est un préfixe à une requête et l’étendue de la liaison est cette requête. (Laissez ne permet pas aux éléments de nom que vous utilisez plus loin dans votre session.)

**Syntaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type :* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query :* Une requête sans le préfixe par une clause autoriser.

**Exemples**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Jointure réflexive :

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Opérateurs et des requêtes

Une requête sur votre télémétrie est constituée d’une référence à un flux source, suivi d’un pipeline de filtres. Par exemple :


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Chaque filtre par le caractère le préfixe `|` est une instance d’un *opérateur*, avec certains paramètres. L’entrée de l’opérateur est la table qui est le résultat du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](#scalars) par rapport aux colonnes de l’entrée. Dans certains cas, les paramètres sont les noms des colonnes d’entrée, et dans certains cas, le paramètre est une seconde table. Le résultat d’une requête est toujours une table, même si elle n’a une colonne et une seule ligne.

Les requêtes peuvent contenir des sauts de ligne seul, mais sont terminent par une ligne vide. Ils peuvent contenir des commentaires entre `//` et de fin de ligne.

Une requête peut être précédée un ou plusieurs [laisser clauses](#let-clause), qui définissent scalaires, des tableaux ou des fonctions qui peuvent être utilisées dans la requête.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`est utilisé dans les exemples de requête ci-dessous pour indiquer le tableau pipeline ou source précédent.
> 

### <a name="count-operator"></a>opérateur Count

La `count` opérateur renvoie le nombre d’enregistrements (lignes) dans le jeu d’enregistrements d’entrée.

**Syntaxe**

    T | count

**Arguments**

* *T*: les données sous forme de tableau dont les enregistrements sont à compter.

**Renvoie**

Cette fonction renvoie une table avec un enregistrement unique et une colonne de type `long`. La valeur de la cellule uniquement est le nombre d’enregistrements dans *T*. 

**Exemple**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>évaluer opérateur

`evaluate`est un mécanisme d’extension qui permet aux algorithmes spécialisées à ajouter aux requêtes.

`evaluate`doit être le dernier opérateur dans le pipeline de requête (à l’exception d’un possible `render`). Il ne doit pas apparaître dans le corps d’une fonction.

[évaluer les autocluster](#evaluate-autocluster) | [évaluer panier](#evaluate-basket) | [évaluer diffpatterns](#evaluate-diffpatterns) | [évaluer extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>évaluer les autocluster

     T | evaluate autocluster()

AutoCluster recherche modèles courants des attributs discrètes (dimensions) dans les données et réduire les résultats de la requête d’origine (si elle est 100 ou 100 lignes k) à un petit nombre de modèles. AutoCluster a été développé pour aider à analyser les échecs (par exemple, exceptions, se bloque) mais peuvent potentiellement travailler sur n’importe quel jeu de données filtré. 

**Syntaxe**

    T | evaluate autocluster( arguments )

**Renvoie**

AutoCluster renvoie un ensemble de modèles qui capturer des parties de données avec valeurs courantes partagées parmi plusieurs attributs discrètes (généralement petit). Chaque modèle est représenté par une ligne dans les résultats. 

Les deux premières colonnes sont le nombre et le pourcentage de lignes sont capturés par le modèle de la requête d’origine. Les autres colonnes sont à partir de la requête d’origine et leur valeur est une valeur spécifique de la colonne ou ' *' ce qui signifie que des valeurs de variables. 

Notez que les modèles ne sont pas disjoints : ils peuvent avoir des doublons et généralement ne décrivent pas toutes les lignes d’origine. Certaines lignes ne peuvent pas être sous n’importe quel modèle.

**Conseils**

* Utiliser `where` et `project` dans le canal d’entrée pour réduire les données à simplement ce qui vous intéresse.
* Lorsque vous trouvez une ligne intéressante, vous souhaiterez peut-être Explorer davantage en ajoutant ses valeurs spécifiques à votre `where` filtre.

**Arguments (facultatifs)**

* `output=all | values | minimal` 

    Le format des résultats. Les colonnes Nb et pourcentage apparaissent toujours dans les résultats. 

 * `all`-toutes les colonnes à partir de l’entrée sont sortie
 * `values`-filtre les colonnes avec uniquement « * » dans les résultats
 * `minimal`-également filtre les colonnes qui sont identiques pour toutes les lignes de la requête d’origine. 


* `min_percent=`*double* (par défaut : 1)

    Couverture de pourcentage minimal des lignes générées.

    Exemple :`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*ent* (par défaut : 25) 

    Le nombre de graines détermine le nombre de points de recherche locale initial de l’algorithme. Dans certains cas, en fonction de la structure des données, augmenter le nombre de graines accroît le nombre (ou qualité) des résultats dans l’espace de recherche accrue en plus lente compromis de requête. L’argument num_seeds a réduire les résultats dans les deux sens afin de diminution inférieure à 5 obtiendrez améliorations des performances négligeable et augmentant au-dessus de 50 générera rarement des motifs supplémentaires.

    Exemple :`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < double < 1*+ (par défaut : 0,5)

    Vous donne contrôler l’équilibre entre générique (couverture élevée) et instructifs (nombreuses valeurs partagées). Size_weight croissant réduit généralement le nombre de modèles et chaque modèle est généralement expliquer un pourcentage supérieur. Diminution size_weight généralement génère des modèles plus spécifiques avec plus les valeurs partagées et la plus petite couverture de pourcentage. Les coulisses formule est une moyenne géométrique pondérée entre le score générique normalisé et instructif score avec size_weight et 1-size_weight en tant que les pondérations. 

    Exemple :`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*column_name*

    Estime chaque ligne de l’entrée en fonction de l’épaisseur spécifiée (par défaut chaque ligne comporte une épaisseur de '1'), il est fréquent d’une colonne de poids à prendre en compte échantillonnage ou la création de compartiments/agrégation des données qui sont déjà incorporées dans chaque ligne.

    Exemple :`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>évaluer panier

     T | evaluate basket()

Panier recherche tous les modèles fréquents des attributs discrètes (dimensions) dans les données et renvoie tous les motifs fréquents que passé le seuil de fréquence dans la requête d’origine. Panier est certain de trouver tous les modèles fréquents dans les données, mais n’est pas garantie d’avoir polynomiale lors de l’exécution. L’exécution de la requête est linéaire dans le nombre de lignes, mais dans certains cas, peut-être être exponentielle du nombre de colonnes (dimensions). Panier est basé sur l’algorithme priori initialement développé pour l’exploration de données analysis panier. 

**Renvoie**

Tous les modèles qui apparaissent dans le plus de choses qu’une fraction spécifiée (par défaut 0,05) des événements.

**Arguments (facultatifs)**


* `threshold=`*0.015 < double < 1* (par défaut : 0,05) 

    Définit le rapport minimal entre les lignes à prendre en considération fréquents (modèles rapport hauteur / largeur plus petits ne seront pas renvoyées).

    Exemple :`T | evaluate basket("threshold=0.02")`


* `weight_column=`*column_name*

    Estime chaque ligne de l’entrée en fonction de l’épaisseur spécifiée (par défaut chaque ligne comporte une épaisseur de '1'), il est fréquent d’une colonne de poids à prendre en compte échantillonnage ou la création de compartiments/agrégation des données qui sont déjà incorporées dans chaque ligne.

    Exemple : T | évaluer les basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (par défaut : 5)

    Définit le nombre maximal de dimensions non liées par panier, limitée par défaut pour diminuer l’exécution de la requête.


* `output=minimize` | `all` 

    Le format des résultats. Les colonnes Nb et pourcentage apparaissent toujours dans les résultats.

 * `minimize`-filtre les colonnes avec uniquement « * » dans les résultats.
 * `all`-toutes les colonnes à partir de l’entrée sont sortie.




#### <a name="evaluate-diffpatterns"></a>évaluer les diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compare deux jeux de données de la même structure et recherche de modèles d’attributs discrètes (dimensions) caractérisant des différences entre les deux jeux de données. Diffpatterns a été développé pour aider à analyser les échecs (par exemple par des défaillances comparaisons non échecs dans une période donnée) mais trouverez potentiellement des différences entre les deux jeux de données de la même structure. 

**Syntaxe**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Renvoie**

Diffpatterns renvoie un ensemble de modèles différentes parties de données dans les deux jeux de capturer (par exemple, un modèle de capturer un pourcentage important des lignes dans le premier jeu de données et faible pourcentage des lignes dans le second jeu) (généralement petit). Chaque modèle est représenté par une ligne dans les résultats.

Les quatre premières colonnes sont le nombre et le pourcentage de lignes la requête d’origine sont capturés par le modèle dans chaque ensemble, la cinquième colonne est la différence (en points de pourcentage absolue) entre les deux jeux. Les autres colonnes sont à partir de la requête d’origine et leur valeur est une valeur spécifique de la colonne ou * ce qui signifie que des valeurs de variables. 

Notez que les modèles ne sont pas distincts : ils peuvent avoir des doublons et généralement ne décrivent pas toutes les lignes d’origine. Certaines lignes ne peuvent pas être sous n’importe quel modèle.

**Conseils**

* Utilisez l’emplacement et project dans le canal d’entrée pour réduire les données à simplement ce qui vous intéresse.

* Lorsque vous trouvez une ligne intéressante, vous souhaiterez peut-être Explorer davantage en ajoutant des valeurs spécifiques pour filtrer vos where.

**Arguments**

* `split=`*nom de colonne* (obligatoire)

    La colonne doit comporter précisément deux valeurs. Si nécessaire, créez une colonne de ce type :

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*chaîne*

    Indique l’algorithme de rechercher uniquement les modèles qui ont un pourcentage supérieur dans le jeu de données cible, la cible doit être un des deux valeurs de la colonne de fractionnement.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < double < 1* (par défaut : 0,05) 

    Définit la différence de motif minimales (ratio) entre les deux jeux.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    Le format des résultats. Les colonnes Nb et pourcentage apparaissent toujours dans les résultats. 

 * `minimize`-filtre les colonnes avec uniquement « * » dans les résultats
 * `all`-toutes les colonnes à partir de l’entrée sont sortie

* `weight_column=`*column_name*

    Estime chaque ligne de l’entrée en fonction de l’épaisseur spécifiée (par défaut, chaque ligne comporte une épaisseur de '1'). Une utilisation courante d’une colonne de poids consiste à prendre en compte échantillonnage ou la création de compartiments/agrégation des données qui sont déjà incorporées dans chaque ligne.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>évaluer les extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns est utilisé pour enrichir un tableau comportant plusieurs colonnes simples qui sont dynamiquement extraites déconnecter (semi-transparent) ou les colonnes structurées selon leur type. Actuellement prise en charge des colonnes json uniquement, les deux dynamique et sérialisation de jsons de chaîne.


* `max_columns=`*ent* (par défaut : 10) 

    Le nombre de nouvelles colonnes ajoutées est dynamique et il peut être très volumineux (en réalité il est le nombre de clés distinctes dans tous les enregistrements json) afin que nous devons le limiter. Les nouvelles colonnes sont triées dans l’ordre décroissant selon leur fréquence et jusqu'à max_columns sont ajoutés à la table.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*double* (par défaut : 10.0) 

    Autre procédure pour limiter les nouvelles colonnes en ignorant les colonnes dont la fréquence est inférieure à min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool* (par défaut : vrai) 

    Une valeur True indique le nom de la colonne complexe est ajouté comme un préfixe aux noms de colonnes extraite.


* `prefix_delimiter=`*chaîne* (par défaut : « _ ») 

    Si add_prefix = true ce paramètre définit le séparateur qui sera utilisé pour concaténer les noms des nouvelles colonnes.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool* (par défaut : false) 

    Si vrai les colonnes (json) d’origine sont conservées dans la table de sortie.


* `output=query | table` 

    Le format des résultats. 

 * `table`-Le résultat est la même table comme reçu moins les colonnes d’entrée spécifiées plus les nouvelles colonnes qui ont été extraits à partir des colonnes d’entrée.
 * `query`-Le résultat est une chaîne qui représente la requête que vous faites pour obtenir le résultat sous forme de tableau. 




### <a name="extend-operator"></a>étendre opérateur

     T | extend duration = stopTime - startTime

Ajoutez un ou plusieurs colonnes calculées dans une table. 


**Syntaxe**

    T | extend ColumnName = Expression [, ...]

**Arguments**

* *T:* Le tableau d’entrée.
* *ColumnName :* Au nom d’une colonne à ajouter. [Noms](#names) respectent la casse et peuvent contenir alphabétique, numérique ou '_' caractères. Utiliser `['...']` ou `["..."]` devis noms avec d’autres caractères ou mots clés pour le code.
* *Expression :* Un calcul sur les colonnes existantes.

**Renvoie**

Une copie de la table d’entrée, avec les autres colonnes spécifiées.

**Conseils**

* Utiliser [`project`](#project-operator) au lieu de cela, si vous souhaitez également déplacer ou renommer certaines colonnes.
* N’utilisez pas `extend` simplement pour obtenir un nom plus court à utiliser dans une expression de type long. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Les colonnes de la table natives ont été indexés ; le nouveau nom définit une colonne supplémentaire n’est pas indexée, afin que la requête est susceptible de s’exécuter plus lentement.

**Exemple**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>opérateur de jointure

    Table1 | join (Table2) on CommonColumn

Fusionne les lignes des deux tables à des valeurs correspondantes de la colonne spécifiée.


**Syntaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Arguments**

* *Table1* - en le « côté gauche » de la jointure.
* *Table2* - « droite » de la jointure. Il peut être une expression de requête imbriquée qui renvoie une table.
* *CommonColumn* - une colonne qui a le même nom dans les deux tables.
* *Type* - spécifie la façon dont les lignes des deux tables doivent correspondre.

**Renvoie**

Une table avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les touches correspondantes. Les colonnes du côté droit seront automatiquement renommés s’il existe des conflits de nom.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée d’une table qui comporte la même valeur pour toutes les `on` champs en tant qu’une ligne dans une autre table. 

* `Kind`n’est pas spécifié

    Qu’une seule ligne à partir du bord gauche est mise en correspondance pour chaque valeur de la `on` clé. La sortie contient une ligne pour chaque occurrence de cette ligne avec des lignes de la droite.

* `Kind=inner`
 
     Il existe une ligne dans le résultat pour toutes les combinaisons de lignes correspondantes de la gauche et droite.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Outre les correspondances internes, il existe une ligne pour chaque ligne sur la gauche (et/ou la droite), même si elle n’a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs NULL.

* `kind=leftanti`

     Renvoie tous les enregistrements du côté gauche qui n’ont pas de correspondance par la droite. La table obtenue comporte uniquement les colonnes à partir de la gauche. 
 
Il existe plusieurs lignes avec les mêmes valeurs pour ces champs, vous obtiendrez lignes pour toutes les combinaisons.

**Conseils**

Pour de meilleures performances :

* Utiliser `where` et `project` pour réduire le nombre de lignes et de colonnes dans les tableaux d’entrée, avant le `join`. 
* Si une table est toujours inférieure à l’autre, l’utiliser comme le côté gauche (canal) de la jointure.
* Les colonnes pour la correspondance de jointure doivent porter le même nom. Utilisez l’opérateur de projet si nécessaire pour renommer une colonne dans une des tables.

**Exemple**

Obtenir des étendues activités à partir d’un journal dans lequel certaines entrées marque le début et la fin d’une activité. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>opérateur de limite

     T | limit 5

Renvoie jusqu’au nombre spécifié de lignes de la table d’entrée. Il n’existe aucune garantie que les enregistrements renvoyés. (Pour renvoyer des enregistrements spécifiques, utilisez [`top`](#top-operator).)

**Alias**`take`

**Syntaxe**

    T | limit NumberOfRows


**Conseils**

`Take`est un moyen simple et efficace pour afficher un échantillon de vos résultats lorsque vous travaillez de façon interactive. N’oubliez pas qu’il ne garantit pas pour générer les lignes particuliers, ou pour les produire dans un ordre en particulier.

Il existe une limite implicite du nombre de lignes renvoyées au client, même si vous n’utilisez pas `take`. Pour lever cette limite, utilisez la `notruncation` option de demande de client.



### <a name="mvexpand-operator"></a>opérateur mvexpand

    T | mvexpand listColumn 

Développe une liste à partir d’une cellule (JSON) tapé dynamique afin de chaque entrée comporte une ligne distincte. Toutes les autres cellules dans une ligne développée sont dupliqués. 

(Voir aussi [`summarize makelist`](#summarize-operator) qui effectue la fonction inverse.)

**Exemple**

Supposons que la table d’entrée est :

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|« hello »|{« clé » : « valeur »}|
|2|« monde »|[0,1, « k », « v »]|

    mvexpand D

Résultat est la suivante :

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|« hello »|{« clé » : « valeur »}|
|2|« monde »|0|
|2|« monde »|1|
|2|« monde »|« k »|
|2|« monde »|« v »|


**Syntaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Arguments**

* *ColumnName :* Dans le résultat, les tableaux dans la colonne nommée sont développés à plusieurs lignes. 
* *ArrayExpression :* Une expression produisant un tableau. Si cet écran est utilisé, une nouvelle colonne est ajoutée et celui qui existe déjà est conservée.
* *Nom :* Un nom pour la nouvelle colonne.
* *Typename :* Convertit l’expression développée à un type particulier
* *RowLimit :* Le nombre maximal de lignes généré à partir de chaque ligne d’origine. La valeur par défaut est 128.

**Renvoie**

Plusieurs lignes pour chacune des valeurs dans un tableau dans la colonne nommée ou dans l’expression de tableau.

La colonne développée a toujours le type dynamique. Utiliser un cast tel que `todatetime()` ou `toint()` si vous voulez calculer ou des valeurs agrégées.

Deux modes d’extensions de sac de propriétés sont prises en charge :

* `bagexpansion=bag`: Conteneurs de propriétés sont développés en conteneurs de propriétés d’entrée unique. Il s’agit de l’extension de la valeur par défaut.
* `bagexpansion=array`: Conteneurs de propriétés sont développés dans deux éléments `[` *clé*`,`*valeur* `]` tableau structures, autoriser l’accès uniforme aux clés et les valeurs (comme, par exemple, exécute une agrégation total distinct sur noms de propriété). 

**Exemples**


    exceptions | take 1 
  	| mvexpand details[0]

Fractionne un enregistrement d’exception en lignes pour chaque élément dans le champ Détails.



### <a name="parse-operator"></a>opérateur d’analyse

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrait les valeurs d’une chaîne. Permet de correspondance d’expression simple ou normale.

**Syntaxe**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Arguments**

* `T`: La table d’entrée.
* `kind`: 
 * `simple`(par défaut) : le `Match` chaînes sont des chaînes de texte brut.
 * `relaxed`: si le texte n’analyse en tant que le type d’une colonne, la colonne a la valeur null et l’analyse continue 
 * `regex`: le `Match` chaînes sont des expressions régulières.
* `Text`: Une colonne ou toute autre expression qui prend la valeur ou peut être convertie en une chaîne.
* *Correspondance :* Correspond à l’étape suivante de la chaîne et mettez-le.
* *Colonne :* Affecter à l’article suivant de la chaîne à cette colonne. La colonne est créée s’il n’existe pas.
* *Type :* Analyser la partie de la chaîne suivante en tant que le type spécifié, tel qu’int, date, appuyez deux fois. 


**Renvoie**

Le tableau d’entrée, étendu en fonction de la liste des colonnes.

Les éléments dans la `with` clause sont comparés à du texte source à son tour. Chaque élément chews désactiver un morceau de texte source : 

* Une chaîne littérale ou une expression régulière replacez le curseur correspondant à la longueur de la recherche.
* Dans une analyse regex, une expression régulière peut utiliser l’opérateur minimisation ' ?' pour atteindre la correspondance suivante dès que possible.
* Un nom de colonne à un type d’analyse le texte en tant que type spécifié. À moins que type = moyenne, une analyse infructueuses invalide correspondant au modèle entier.
* Un nom de colonne avec le type « chaîne », ou sans un type de copie le nombre minimal de caractères pour accéder à la correspondance suivante.
* ' *' Ignore le nombre minimal de caractères pour accéder à la correspondance suivante. Vous pouvez utiliser «*» au début et à la fin du modèle, ou après un type différent de chaîne, ou entre les correspondances de chaînes.

Tous les éléments dans un modèle d’analyse doivent correspondre correctement ; dans le cas contraire, aucun résultat ne sera produit. Les exceptions à cette règle sont que lorsque type = moyenne, si l’analyse d’une variable typée échoue, le reste de l’analyse continue.

**Exemples**

*Simple :*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | compteur | présenter | Année
---|---|---|---
1 | 2 | SOCKS | 63

*Souple :*

Lorsque l’entrée contient une mise en correspondance appropriée pour chaque colonne tapé, une analyse souple génère le même résultat qu’une analyse simple. Mais si une des colonnes tapées n’analyse correctement, une analyse souple continue à traiter le reste du modèle, alors qu’une analyse simple s’arrête et générer les résultats.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | présenter | Année
---|---|---
1 |  SOCKS | 63


*Expressions régulières :*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

ressource | secteur | verrouillage | version finale | précédent
---|---|---|---|---
Planificateur | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>opérateur de projet

    T | project cost=price*quantity, price

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérer de nouvelles colonnes calculées. L’ordre des colonnes dans le résultat est spécifié par l’ordre des arguments. Uniquement les colonnes spécifiées dans les arguments sont inclus dans le résultat : tous les autres données d’entrée sont supprimées.  (Voir aussi `extend`.)


**Syntaxe**

    T | project ColumnName [= Expression] [, ...]

**Arguments**

* *T:* Le tableau d’entrée.
* *ColumnName :* Le nom d’une colonne doit apparaître dans le résultat. S’il n’existe aucune *Expression*, une colonne de ce nom doit apparaître dans l’entrée. [Noms](#names) respectent la casse et peuvent contenir alphabétique, numérique ou '_' caractères. Utiliser `['...']` ou `["..."]` devis noms avec d’autres caractères ou mots clés pour le code.
* *Expression :* Expression scalaire facultative faisant référence à des colonnes d’entrée. 

    Il n’est autorisé pour renvoyer une nouvelle colonne calculée avec le même nom qu’une colonne existante dans l’entrée.

**Renvoie**

Une table qui comporte les colonnes nommées en tant qu’arguments, ainsi que le nombre lignes comme étant la table d’entrée.

**Exemple**

L’exemple suivant illustre plusieurs types opérations mettent qui peut être effectuée à l’aide de la `project` opérateur. Le tableau d’entrée `T` contient trois colonnes de type `int`: `A`, `B`, et `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>opérateur absent de projet

    T | project-away column1, column2, ...

Exclure les colonnes spécifiées. Le résultat contient toutes les colonnes d’entrée à l’exception de ceux que vous nommez.

### <a name="range-operator"></a>opérateur de plage

    range LastWeek from ago(7d) to now() step 1d

Génère une table d’une seule colonne de valeurs. Notez qu’elle ne comporte un pipeline de saisie. 

|SemaineDernière|
|---|
|09:10:04.627 12/2015 / 05|
|12 / 2015 / 06 09:10:04.627|
|...|
|09:10:04.627 12 / 12 / 2015|



**Syntaxe**

    range ColumnName from Start to Stop step Step

**Arguments**

* *ColumnName :* Le nom de la colonne unique dans la table de sortie.
* *Démarrer :* La plus petite valeur dans le résultat.
* *Arrêter :* La valeur la plus élevée en cours de génération de la sortie (ou une limite sur la valeur la plus élevée, si les étapes *étape* sur cette valeur).
* *Étape :* La différence entre deux valeurs consécutifs. 

Les arguments doivent être des valeurs numériques, de date ou de timespan. Ils ne peuvent pas faire référence aux colonnes d’une table. (Si vous souhaitez calculer la plage basée sur une table d’entrée, utilisez la [ *fonction*de la plage](#range), peut-être avec l' [opérateur mvexpand](#mvexpand-operator).) 

**Renvoie**

Une table avec une colonne unique nommée *ColumnName*, dont les valeurs doivent *commencer*, *Démarrez* + *étape*,... jusqu'à et y compris *Arrêter*.

**Exemple**  

```AIQL
range Steps from 1 to 8 step 3
```

Une table avec une seule colonne appelée `Steps` dont le type est `long` et dont les valeurs sont `1`, `4`, et `7`.

**Exemple**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Table de minuit en les sept derniers jours. La fonction emplacement (sol) réduit chaque fois jusqu’au début du jour.

**Exemple**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Montre comment la `range` opérateur peut être utilisé pour créer une petite, ad hoc, une table de dimension qui est ensuite utilisé pour présenter les zéros non significatifs dans lequel la source de données n’a aucune valeur.

### <a name="reduce-operator"></a>réduire l’opérateur

    exceptions | reduce by outerMessage

Tente de regrouper des enregistrements similaires. Pour chaque groupe, l’opérateur renvoie la `Pattern` lorsqu’il pense le mieux à ce groupe et la `Count` d’enregistrements dans ce groupe.


![](./media/app-insights-analytics-reference/reduce.png)

**Syntaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Arguments**

* *ColumnName :* La colonne à examiner. Il doit être de type chaîne.
* *Seuil :* Une valeur comprise {entre 0 et 1}. Par défaut est 0,001. Pour les entrées de grande taille, seuil doit être faible. 

**Renvoie**

Deux colonnes, `Pattern` et `Count`. Dans de nombreux cas, un motif sera une valeur complète de la colonne. Dans certains cas, il peut identifier les termes courants et remplacer les parties variables avec ' *'.

Par exemple, le résultat de `reduce by city` peuvent inclure : 

|Motif | Nombre |
|---|---|
| SAN * | 5182 |
| Saint * | 2846 |
| Moscou | 3726 |
| \*-on-\* | 2730 |
| Paris | 27163 |


### <a name="render-directive"></a>rendre la directive

    T | render [ table | timechart  | barchart | piechart ]

Rendu du indique comment afficher la table à la couche de présentation. Il doit être le dernier élément du canal. Il est une alternative pratique à l’aide des contrôles de l’affichage, ce qui vous permet d’enregistrer une requête avec une méthode de présentation particulière.

### <a name="restrict-clause"></a>restreindre la clause 

Spécifie le jeu de noms de tables disponibles aux opérateurs qui suivent. Par exemple :

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>opérateur de tri 

    T | sort by country asc, price desc

Trier les lignes de la table d’entrée dans l’ordre par une ou plusieurs colonnes.

**Alias**`order`

**Syntaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Arguments**

* *T:* Le tableau d’entrée à trier.
* *Colonne :* Colonne de *T* par laquelle effectuer le tri. Le type des valeurs doit être numérique, date, heure ou chaîne.
* `asc`Trier par dans l’ordre croissant, faible sur élevé. La valeur par défaut est `desc`, décroissant élevé au moins.

**Exemple**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Toutes les lignes dans les Traces table ayant un certain `ActivityId`, triés par leur horodatage.

### <a name="summarize-operator"></a>résumer opérateur

Génère une table qui regroupe le contenu de la table d’entrée.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Une table qui affiche le numéro, durée moyenne demande et un jeu de villes dans chaque pays ou région. Il existe une ligne dans le résultat de chaque pays distinct. Les colonnes de sortie affichent le nombre, durée moyenne, villes et pays. Toutes les autres colonnes d’entrée sont ignorées.


    T | summarize count() by price_range=bin(price, 10.0)

Une table qui indique le nombre d’éléments disposent prix dans chaque intervalle [0,10.0], [10.0,20.0], et ainsi de suite. Cet exemple compose une colonne pour réduire le nombre et l’autre pour la plage de prix. Toutes les autres colonnes d’entrée sont ignorées.


**Syntaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Arguments**

* *Colonne :* Nom facultatif pour une colonne de résultats. Les valeurs par défaut à un nom dérivé de l’expression. [Noms](#names) respectent la casse et peuvent contenir alphabétique, numérique ou '_' caractères. Utiliser `['...']` ou `["..."]` devis noms avec d’autres caractères ou mots clés pour le code.
* *Agrégation :* Un appel à une fonction d’agrégation telles que `count()` ou `avg()`, les noms de colonne en tant qu’arguments. Voir [agrégations](#aggregations).
* *GroupExpression :* Une expression par rapport aux colonnes, qui fournit un jeu de valeurs distinctes. En règle générale, il est soit un nom de colonne fournissant déjà un ensemble restreint de valeurs, ou `bin()` avec une colonne numérique ou d’heure comme argument. 

Si vous fournissez une expression numérique ou d’heure sans utiliser `bin()`, Analytique applique automatiquement avec un intervalle de `1h` pour les heures, ou `1.0` pour les nombres.

Si vous ne fournissez un *GroupExpression,* l’ensemble du tableau est indiquée dans une ligne de sortie unique.



**Renvoie**

Les lignes d’entrée sont organisées en groupes qui ont les mêmes valeurs de la `by` expressions. Puis les fonctions d’agrégation spécifié sont calculées au-dessus de chaque groupe, produisant une ligne pour chaque groupe. Le résultat contient la `by` également au moins une colonne pour chacune d’elles et colonnes calculées agrégat. (Certaines fonctions d’agrégation renvoient plusieurs colonnes).

Le résultat comporte toutes les lignes sont des combinaisons distinctes de `by` valeurs. Si vous souhaitez synthétiser sur des plages de valeurs numériques, utilisez `bin()` afin de réduire les plages sur valeurs distinctes.

**Remarque**

Bien que vous pouvez fournir des expressions arbitraires pour l’agrégation et les expressions de regroupement, il est préférable d’utiliser des noms de colonne simples ou appliquer `bin()` à une colonne numérique.



### <a name="take-operator"></a>opérateur Take

Alias de [limite](#limit-operator)


### <a name="top-operator"></a>opérateur Top

    T | top 5 by Name desc nulls first

Renvoie les enregistrements *N* premières triés selon les colonnes spécifiées.


**Syntaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Arguments**

* *NumberOfRows :* Le nombre de lignes de *T* pour renvoyer.
* *Sort_expression :* Expression utilisée trier les lignes. Il s’agit d’un simple nom de colonne. Vous pouvez spécifier plusieurs sort_expression.
* `asc`ou `desc` (par défaut) peut-être s’afficher au contrôle si la sélection est en réalité à « bas » ou « haut » de la plage.
* `nulls first`ou `nulls last` permettent de contrôler l’endroit où les valeurs null apparaissent. `First`est la valeur par défaut pour `asc`, `last` est la valeur par défaut pour `desc`.


**Conseils**

`top 5 by name`en apparence équivaut à `sort by name | take 5`. Toutefois, il s’exécute plus rapidement et renvoie le tri des résultats, alors que `take` ces aucune garantie.

### <a name="top-nested-operator"></a>opérateur imbriqués haut

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Génère des résultats hiérarchiques, où chaque niveau est une exploration des niveaux inférieurs à partir du niveau précédent. Il est utile pour les réponses aux questions sembler « quelles sont les 5 principaux requêtes et pour chacun d’eux, quels sont les plages de meilleures 3 performances et pour chacun d’eux, qui sont les pays haut 3 les demandes proviennent ? »

**Syntaxe**

   T | haut-imbriqués N de colonne par agrégation [,...]

**Arguments**

* N:int - nombre de lignes à renvoyer ou passer à un niveau avancé. Dans une requête avec trois niveaux où N est 3, 3 et 5, le nombre total de lignes sera 45.
* COLONNE - une colonne à regrouper par pour l’agrégation. 
* AGRÉGATION - une [fonction d’agrégation](#aggregations) à appliquer à chaque groupe de lignes. Les résultats de ces agrégations déterminera les groupes supérieure à afficher.


### <a name="union-operator"></a>opérateur d’union

     Table1 | union Table2, Table3

Prend deux ou plusieurs tables et retourne les lignes de celles-ci. 

**Syntaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Arguments**

* *Table1*, *Table2* ...
 *  Le nom d’un tableau, tel que `requests`, ou une table définie dans une [clause de laisser](#let-clause); ou
 *  Une requête expression, telles que`(requests | where success=="True")`
 *  Un ensemble de tables spécifiées avec un caractère générique. Par exemple, `e*` serait forment l’union de toutes les tables définies dans des clauses permettent aux précédentes dont le nom commence par « e », ainsi que la table « exceptions ».
* `kind`: 
 * `inner`-Il a été le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
 * `outer`-Le résultat comporte toutes les colonnes qui se produisent dans une des entrées. Cellules qui n’ont pas été définies par une ligne d’entrée sont définies sur `null`.
* `withsource=`*ColumnName :* Si spécifié, le résultat inclut une colonne nommée *ColumnName* dont la valeur indique quelle table source a collaboré à chaque ligne.

**Renvoie**

Une table avec autant de lignes qu’il se trouvent dans toutes les tables d’entrée et autant de colonnes qu’est les noms de colonne unique dans les entrées.

**Exemple**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Union de toutes les tables dont le nom commence « tt ».


**Exemple**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Le nombre d’utilisateurs distincts qui ont produit soit un `exceptions` événement ou une `traces` événement sur le jour précédent. Dans le résultat, la colonne « SourceTable » indiquera « Requête » ou « Commande ».

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Cette version plus efficace génère le même résultat. Il filtre chaque table avant de créer l’union.

### <a name="where-operator"></a>où opérateur

     requests | where resultCode==200

Filtre une table au sous-ensemble de lignes qui répondent à un prédicat.

**Alias**`filter`

**Syntaxe**

    T | where Predicate

**Arguments**

* *T:* Entrée sous forme de tableau dont les enregistrements doivent être filtrées.
* *Prédicats :* A `boolean` [expression](#boolean) par rapport aux colonnes de *T*. Il est évaluée pour chaque ligne dans *T*.

**Renvoie**

Lignes de *T* pour laquelle *prédicats* est `true`.

**Conseils**

Pour obtenir les meilleures performances :

* **Comparaisons simples d’utilisation** entre les noms de colonnes et des constantes. (« Constante » signifie constante sur la table - afin de `now()` et `ago()` sont OK, et donc des valeurs scalaires affectés à l’aide d’un [ `let` clause](#let-clause).)

    Par exemple, vous préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Termes du contrat de Simplest première**: Si vous avez plusieurs clauses unies avec `and`, placer tout d’abord les clauses qui impliquent qu’une colonne. Donc `Timestamp > ago(1d) and OpId == EventId` est supérieure à celle de l’inverse.


**Exemple**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Enregistrements qui n’antérieurs à 1 heure et proviennent de la Source appelée « Kuskus » et ont des deux colonnes de la même valeur. 

Notez que nous placer la comparaison entre les deux colonnes dernière, tel qu’il ne peut pas utiliser l’index et force la création d’une analyse.


### <a name="where-in-operator"></a>opérateur de WHERE

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Syntaxe**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Arguments**

* `col`: Une colonne du tableau.
* `expr1`... : Une liste d’expressions scalaires.

Utiliser `in` est utilisée pour inclure uniquement les lignes dans lesquelles `col` est égal à l’une des expressions `expr1...`.

Utiliser `!in` pour inclure uniquement les lignes dans lesquelles `col` n’est pas égale à une des expressions `expr1...`.  


## <a name="aggregations"></a>Agrégations

Agrégations sont des fonctions utilisées pour combiner des valeurs dans les groupes créés dans l' [opération de résumer](#summarize-operator). Par exemple, dans cette requête, dcount() est une fonction d’agrégation :

    requests | summarize dcount(name) by success

### <a name="any"></a>tout 

    any(Expression)

Sélectionne une ligne du groupe et renvoie la valeur de l’expression spécifiée de manière aléatoire.

Ceci est utile, par exemple, lorsque certaines colonne a un grand nombre de valeurs similaires (par exemple, une colonne « texte erreur ») et que vous voulez exemple de cette colonne une fois par une valeur unique de la clé de groupe composé. 

**Exemple**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Recherche une ligne dans le groupe qui réduit/optimise la *ExprToMaximize*et renvoie la valeur de *ExprToReturn* (ou `*` pour renvoyer la ligne entière).

**Conseil**: les colonnes passé via sont automatiquement renommées. Pour vérifier que vous utilisez les noms de droite, examinez les résultats à l’aide de `take 5` avant de vous canal les résultats dans un autre opérateur.

**Exemples**

Pour chaque nom de la demande, afficher quand la cellule la plus longue demande s’est produite :

    requests | summarize argmax(duration, timestamp) by name

Afficher tous les détails de la demande de la plus longue, pas seulement l’horodatage :

    requests | summarize argmax(duration, *) by name


Trouver la plus petite valeur de chaque métrique, ainsi que son horodatage et autres données :

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>AVG

    avg(Expression)

Calcule la moyenne *d’Expression* dans le groupe.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Renvoie le schéma minimal qui n’admet toutes les valeurs de *DynamicExpression*. 

Le type de colonne paramètre doit être `dynamic` -sac d’une matrice ou une propriété. 

**Exemple**

    exceptions | summarize buildschema(details)

Résultat :

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Notez que `indexer` est utilisée pour indiquer l’endroit où vous devez utiliser un index numérique. Pour ce schéma, certains chemins d’accès valides (en cas de que ces index exemple se trouvent dans la plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Exemple**

Supposons que la colonne d’entrée possède trois valeurs dynamiques :

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Le schéma résultant serait le suivant :

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Le schéma nous indique que :

* L’objet racine est un conteneur avec quatre propriétés nommées x, y, z et t.
* La propriété appelée « x » peut être de type « int » ou de type « string ».
* La propriété appelée « y » susceptible de des options de type « double », ou un autre conteneur avec une propriété appelée « w » de type « string ».
* La ``indexer`` mot clé indique que « z » et « t » sont des tableaux.
* Chaque élément de la matrice « z » est int ou une chaîne.
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement facultative et n’importe quel tableau peut être vide.

##### <a name="schema-model"></a>Modèle de schéma

La syntaxe du schéma retourné est la suivante :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Ils sont équivalents à un sous-ensemble des annotations type machine à écrire, codée sous forme d’une valeur dynamique. Dans la machine à écrire, le schéma exemple serait le suivant :

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>nombre

    count([ Predicate ])

Retourne le nombre total de lignes pour lesquelles *prédicats* a `true`. Si aucun *prédicats* sont spécifié, renvoie le nombre total d’enregistrements dans le groupe. 

**Performances Conseil**: utilisez `summarize count(filter)` au lieu de`where filter | summarize count()`

> [AZURE.NOTE] Évitez d’utiliser NB() pour trouver le nombre de requêtes, exceptions ou d’autres événements qui se sont produites. Lorsque [échantillonnages](app-insights-sampling.md) fonctionne, le nombre de points de données conservées dans l’Application Insights sera inférieur au nombre d’événements d’origine. Au lieu de cela, `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d’événements d’origine qui sont représentés par chaque point de données conservées.

### <a name="countif"></a>Nb.Si

    countif(Predicate)

Retourne le nombre total de lignes pour lesquelles *prédicats* a `true`.

**Performances Conseil**: utilisez `summarize countif(filter)` au lieu de`where filter | summarize count()`

> [AZURE.NOTE] Évitez d’utiliser countif() pour trouver le nombre de requêtes, exceptions ou d’autres événements qui se sont produites. Lorsque [échantillonnages](app-insights-sampling.md) fonctionne, le nombre de points de données sera inférieur au nombre d’événements réels. Au lieu de cela, `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d’événements d’origine qui sont représentés par chaque point de données conservées.

### <a name="dcount"></a>BDNB

    dcount( Expression [ ,  Accuracy ])

Renvoie une estimation du nombre de valeurs distinctes de *Expr* dans le groupe. (Pour répertorier les valeurs distinctes, utilisez [`makeset`](#makeset).)

*Précision*, si spécifié, contrôles équilibrer la vitesse et de précision.

 * `0`= le calcul moins précis et la plus rapide.
 * `1`la valeur par défaut, qui équilibre temps précision et de calcul ; erreur 0,8 % sur.
 * `2`= plus précis et plus lent le calcul ; erreur 0,4 % sur.

**Exemple**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Renvoie une estimation du nombre de valeurs distinctes *Expr* de lignes dans le groupe pour lesquels *prédicats* est remplie. (Pour répertorier les valeurs distinctes, utilisez [`makeset`](#makeset).)

*Précision*, si spécifié, contrôles équilibrer la vitesse et de précision.

 * `0`= le calcul moins précis et la plus rapide.
 * `1`la valeur par défaut, qui équilibre temps précision et de calcul ; erreur 0,8 % sur.
 * `2`= plus précis et plus lent le calcul ; erreur 0,4 % sur.

**Exemple**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makeList

    makelist(Expr [ ,  MaxListSize ] )

Renvoie un `dynamic` tableau (JSON) de toutes les valeurs *Expr* dans le groupe. 

* *MaxListSize* est une limite entier facultatif sur le nombre maximal d’éléments retournés (valeur par défaut est *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Renvoie un `dynamic` tableau (JSON) du jeu de valeurs distinctes *Expr* vous permet d’accéder dans le groupe. (Conseil : pour juste compter les valeurs distinctes, utilisez [`dcount`](#dcount).)
  
*  *MaxSetSize* est une limite entier facultatif sur le nombre maximal d’éléments retournés (valeur par défaut est *128*).

**Exemple**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Voir aussi la [ `mvexpand` opérateur](#mvexpand-operator) pour la fonction inverse.


### <a name="max-min"></a>Max, min

    max(Expr)

Calcule le nombre maximal de *Expr*.
    
    min(Expr)

Calcule la valeur minimale de *Expr*.

**Conseil**: cela vous donne la min ou max sur sa propre - par exemple, la plus élevée ou de l’ordre décroissant selon le prix. Mais si vous souhaitez que les autres colonnes dans la ligne - par exemple, le nom du fournisseur avec prix le plus bas - utiliser [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>centile, centiles, percentilew, percentilesw

    percentile(Expression, Percentile)

Renvoie une estimation de l' *Expression* du centile spécifié dans le groupe. La précision dépend de la densité de population dans la région du centile.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Comme `percentile()`, mais calcule un nombre de valeurs de centiles (qui est plus rapide que le calcul chaque centile individuellement).

    percentilew(Expression, WeightExpression, Percentile)

Centile pondérée. Utilisez ces données préalable agrégées.  `WeightExpression`est un nombre entier qui indique le nombre de lignes d’origine est représenté par chaque ligne agrégé.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Comme `percentilew()`, mais calcule un nombre de valeurs de centiles.

**Exemples**


La valeur de `duration` qui est supérieure à 95 % de l’échantillon défini et inférieure à 5 % de l’ensemble de l’exemple, calculé pour chaque nom de la demande :

    request 
  	| summarize percentile(duration, 95)
      by name

Omettre » par... » pour calculer pour l’ensemble du tableau.

Calculer simultanément plusieurs centiles des noms différents demande :

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Les résultats montrent que pour le /Events/Index demande, égal à 5 % de requêtes sont répondu dans 2.44s inférieure à la moitié d'entre eux dans 3.52s, et à 5 % sont inférieures à 6.85s.

Calculer des statistiques sur plusieurs :

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Centiles pondérées

Utilisez les fonctions centile pondérée dans les cas où les données ont été préalablement agrégées. 

Par exemple, supposons que votre application effectue des milliers d’opérations par seconde, et que vous voulez connaître leur latence. La solution simple serait pour générer une demande d’analyse de l’Application ou un événement personnalisé pour chaque opération. Cela créez un grand nombre de trafic, bien que d’échantillonnage adapté prenne effet pour la réduire. Mais que vous décidez d’implémenter une solution plus efficace : vous allez écrire du code dans votre application pour regrouper les données avant d’envoyer analyse de l’Application. Résumé agrégé sera envoyé à intervalles réguliers, réduire le taux de données peut-être à quelques points par minute.

Votre code prend un flux de mesures de latence en millisecondes. Par exemple :
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Il détermine les mesures dans les emplacements suivants :`{ 10, 20, 30, 40, 50, 100 }`

Régulièrement, elle effectue une série d’appels TrackEvent, une pour chaque plage, avec des valeurs personnalisées dans chaque appel : 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

En Analytique, vous voyez un tel groupe d’événements à ceci :

`opCount` | `latency`| signification
---|---|---
8 | 10 | = 8 opérations dans l’emplacement de 10 MS
6 | 20 | = 6 operations dans l’emplacement de 20 MS
3 | 30 | = 3 operations dans la Corbeille 30ms
1 | 40 | = opérations 1 dans la Corbeille 40ms

Pour obtenir une image précise de la distribution d’origine de la latence de l’événement, nous utilisons `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Les résultats sont les mêmes que si nous avions utilisé brut `percentiles` sur l’ensemble d’origine des mesures.

> [AZURE.NOTE] Les centiles pondérées ne sont pas applicables aux [exemples de données](app-insights-sampling.md), où chaque ligne échantillonnée représente un échantillon aléatoire des lignes d’origine, au lieu d’un emplacement. Les fonctions centile simple sont appropriées pour les exemples de données.

#### <a name="estimation-error-in-percentiles"></a>Erreur d’estimation dans centiles

L’agrégat centiles fournit une valeur approximative à l’aide de [T-résumé](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Quelques points importants : 

* Les limites sur l’erreur estimation dépendent de la valeur du centile demandée. Le niveau de précision est à la fin des [0..100] échelle, centiles 0 et 100 sont l’exacte valeurs minimales et maximales de la distribution. La précision diminue progressivement vers le milieu de l’échelle. Il est moins bons en la valeur médiane et est limitée à 1 %. 
* Limites d’erreur sont observés dans le rang, pas sur la valeur. Supposons centile (X, 50) a retourné la valeur de XML. L’estimation garantit qu’au moins 49 et au maximum 51 % des valeurs de X sont inférieures à XML. Il n’existe aucune limite théorique sur la différence entre XML et la valeur médiane réelle de X.

### <a name="stdev"></a>ECARTYPE

     stdev(Expr)

Retourne l’écart type de *Expr* sur le groupe.

### <a name="variance"></a>variance

    variance(Expr)

Retourne la variance de *Expr* sur le groupe.

### <a name="sum"></a>somme

    sum(Expr)

Renvoie la somme de *Expr* sur le groupe.                      


## <a name="scalars"></a>Scalaires

[conversions](#casts) | [comparaisons](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hachage](#hash) | [données](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [NOT NULL](#notnull) | [toscalar](#toscalar)

Les types pris en charge sont :

| Type      | Nom (s) supplémentaires   | Type .NET équivalent |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Lumières

Vous pouvez effectuer un cast d’un type à l’autre. En règle générale, si la conversion a du sens, il est prises en charge :

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Vérifier si une chaîne peut être convertie en un type spécifique :

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Comparaisons scalaires

||
---|---
`<` |Moins
`<=`|Inférieur ou égal à
`>` |Supérieur
`>=`|Supérieur ou égal à
`<>`|Pas égal à
`!=`|Pas égal à 
`in`| Opérande de droite est un tableau (dynamique) et opérande gauche est égal à un de ses éléments.
`!in`| Opérande de droite est un tableau (dynamique) et opérande gauche n’est pas égale à une de ses éléments.




### <a name="gettype"></a>GetType

**Renvoie**

Chaîne représentant le type de stockage sous-jacent de cet argument unique. Ceci est particulièrement utile lorsque vous avez des valeurs de type `dynamic`: dans ce cas `gettype()` dévoilent comment une valeur est codée.

**Exemples**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hachage

**Syntaxe**

    hash(source [, mod])

**Arguments**

* *source*: la source scalaire est calculé le hachage.
* *Mod*: le modulo valeur à être appliquée au résultat de hachage.

**Renvoie**

La valeur xxhash (long) du scalaire donné, modulo la valeur donnée mod (si spécifié).

**Exemples**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>données

La `iff()` fonction évalue le premier argument (le prédicat) et renvoie la valeur de la deuxième ou troisième arguments selon que le prédicat apparaît `true` ou `false`. Les deuxième et troisième arguments doivent être du même type.

**Syntaxe**

    iff(predicate, ifTrue, ifFalse)


**Arguments**

* *prédicat :* Une expression qui prend la valeur d’un `boolean` valeur.
* *ifTrue :* Une expression évaluée et sa valeur renvoyée par la fonction si *prédicat* prend la valeur `true`.
* *ifFalse :* Une expression évaluée et sa valeur renvoyée par la fonction si *prédicat* prend la valeur `false`.

**Renvoie**

Cette fonction renvoie la valeur de *ifTrue* si *prédicat* a `true`, ou la valeur *ifFalse* autrement.

**Exemple**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, NOT NULL

    isnull(parsejson("")) == true

Prend un argument unique et indique si elle est null.

**Syntaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Renvoie**

True ou false selon si la valeur est null ou non null.


|x|IsNull(x)
|---|---
| "" | faux
|« x » | faux
|parseJSON("")|Vrai
|parseJSON("[]")|faux
|parseJSON("{}")|faux

**Exemple**

    T | where isnotnull(PossiblyNull) | count

Notez qu’il existe d’autres moyens d’obtenir cet effet :

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Évalue une requête ou une expression et retourne le résultat sous forme d’une valeur unique. Cette fonction est utile pour les calculs intermédiaires ; exemple : calculer un nombre total d’événements, puis en utilisant qui qu’un planning de référence.

**Syntaxe**

    toscalar(query)
    toscalar(scalar)

**Renvoie**

L’argument évaluée. Si l’argument est une table, renvoie la première colonne de la première ligne. (Bonnes pratiques consiste à organiser que l’argument n'ait qu’une seule colonne et ligne).

**Exemple**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Littéraux booléens

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Opérateurs booléens

    and 
    or 

    

## <a name="numbers"></a>Numéros

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Littéraux numériques

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Opérateurs arithmétiques

|| |
|---|-------------|
| + | Ajouter         |
| - | Soustraire    |
| * | Multiplier    |
| / | Diviser      |
| % | Modulo      |
||
|`<` |Moins
|`<=`|Inférieur ou égal à
|`>` |Supérieur
|`>=`|Supérieur ou égal à
|`<>`|Pas égal à
|`!=`|Pas égal à 


### <a name="abs"></a>ABS

**Syntaxe**

    abs(x)

**Arguments**

* x - entier, réel ou timespan

**Renvoie**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>emplacement, plancher

Arrondit vers le bas jusqu'à un nombre entier multiple taille d’un emplacement donné. Souvent utilisée dans les [`summarize by`](#summarize-operator) requête. Si vous avez une série de valeurs diffusée, elles sont regroupées en un plus petit ensemble de valeurs spécifiques.

Alias `floor`.

**Syntaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Arguments**

* *valeur :* Un nombre, une date ou timespan. 
* *roundTo :* « Taille de la Corbeille. » Un nombre, date ou timespan qui divise *valeur*. 

**Renvoie**

Au multiple de *roundTo* sous *valeur*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemples**

Expression | Résultat
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


L’expression suivante calcule un histogramme des durées, avec une taille de peinture de 1 seconde :

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>plancher

Un alias pour [`bin()`](#bin).

### <a name="gamma"></a>gamma

La [fonction gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Syntaxe**

    gamma(x)

**Arguments**

* *x:* Un nombre réel

Pour des entiers positifs, `gamma(x) == (x-1)!` par exemple, `gamma(5) == 4 * 3 * 2 * 1`.

Voir aussi [loggamma](#loggamma).


### <a name="log"></a>journal

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`doit être un nombre réel > 0. Dans le cas contraire, null est renvoyé.

### <a name="loggamma"></a>loggamma


Le logarithme népérien de la valeur absolue de la [fonction gamma](#gamma).

**Syntaxe**

    loggamma(x)

**Arguments**

* *x:* Un nombre réel


### <a name="rand"></a>ALEA

Générateur de nombres aléatoires.

* `rand()`-un nombre réel entre 0,0 et 1,0
* `rand(n)`-un nombre entier compris entre 0 et n-1




### <a name="sqrt"></a>racine

La fonction racine carrée.  

**Syntaxe**

    sqrt(x)

**Arguments**

* *x:* Un nombre réel > = 0.

**Renvoie**

* Un nombre positif tel que`sqrt(x) * sqrt(x) == x`
* `null`Si l’argument est un nombre négatif ou ne peut pas être converti en une `real` valeur. 




### <a name="toint"></a>ToInt

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>ToDouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Date et heure


[Il y a](#ago) | [dayofmonth](#dayofmonth) | [jour de la semaine](#dayofweek) |  [dayofyear](#dayofyear) |[PartDate](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [maintenant](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Littéraux de date et heure

|||
---|---
**date/heure**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Heures sont toujours au format UTC. L’omission de la date qui donne un temps aujourd'hui.
`now()`|L’heure actuelle.
`now(`-*TimeSpan*`)`|`now()-`*TimeSpan*
`ago(`*TimeSpan*`)`|`now()-`*TimeSpan*
**TimeSpan**|
`2d`|2 jours
`1.5h`|1 heure et demie 
`30m`|30 minutes
`10s`|10 secondes
`0.1s`|0,1 seconde
`100ms`| 100 millisecondes
`10microsecond`|
`1tick`|100 NS
`time("15 seconds")`|
`time("2")`| 2 jours
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Expressions de date et heure

Expression |Résultat
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Moins
`<=`|Inférieur ou égal à
`>` |Supérieur
`>=`|Supérieur ou égal à
`<>`|Pas égal à
`!=`|Pas égal à 




### <a name="ago"></a>Il y a

Soustrait la période donnée à partir de l’heure de l’horloge UTC actuelle. Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction et l’heure de l’horloge UTC référencé sera la même pour toutes les instanciations.

**Syntaxe**

    ago(a_timespan)

**Arguments**

* *a_timespan*: intervalle à soustraire de l’heure de l’horloge UTC actuelle (`now()`).

**Renvoie**

    now() - a_timespan

**Exemple**

Toutes les lignes avec un horodatage dans la dernière heure :

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Extrait une partie spécifiée d’une date sous forme d’entier.

**Syntaxe**

    datepart(part, datetime)

**Arguments**

* `part:String`-{« Year » « Mois », « Jour », « Heure », « Minute », « », « Millisecondes », « Microseconde », puis « Nanosecondes »}
* `datetime`

**Renvoie**

Long représentant la partie spécifiée.


### <a name="dayofmonth"></a>DayOfMonth

    dayofmonth(datetime("2016-05-15")) == 15 

Numéro du jour du mois.

**Syntaxe**

    dayofmonth(a_date)

**Arguments**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>jour de la semaine

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Le nombre de jours écoulés depuis le précédent entier dimanche, comme un `timespan`.

**Syntaxe**

    dayofweek(a_date)

**Arguments**

* `a_date`: A `datetime`.

**Renvoie**

La `timespan` depuis minuit au début de ce qui précède dimanche, arrondi au nombre entier de jours.

**Exemples**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Numéro du jour de l’année.

**Syntaxe**

    dayofyear(a_date)

**Arguments**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Obtenir le nombre de mois (1 à 12) à partir d’une valeur datetime.

**Exemple**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getYear

Obtenir l’année à partir d’une valeur datetime.

**Exemple**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>maintenant

    now()
    now(-2d)

L’heure de l’horloge UTC actuelle, vous pouvez également décalage par un timespan donné. Cette fonction peut être utilisée plusieurs fois dans une instruction et l’heure de l’horloge référencé sera la même pour toutes les instances.

**Syntaxe**

    now([offset])

**Arguments**

* *décalage :* A `timespan`, ajouté à l’heure de l’horloge UTC actuelle. Par défaut : 0.

**Renvoie**

L’heure de l’horloge UTC actuelle comme un `datetime`.

    now() + offset

**Exemple**

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Vérifier si une chaîne est une date valide :

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Le résultat d’un entier représente le numéro de semaine par la norme ISO 8601. Le premier jour de la semaine est dimanche et la première semaine de l’année est la semaine contenant premier jeudi cette an. (Les derniers jours d’une année peuvent contenir par conséquent parmi les jours de la semaine 1 de l’année suivante ou les premiers jours peuvent contenir des semaine 52 ou 53 de l’année précédente).


## <a name="string"></a>Chaîne

[countof](#countof) | [Extraire](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [Remplacer](#replace) | [Fractionner](#split) | [strcat](#strcat) | [strlen](#strlen) | [sous-chaîne](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Littéraux de chaîne

Les règles sont les mêmes que dans JavaScript.

Chaînes peuvent être placés entre soit entre apostrophes ou de guillemets doubles. 

Barre oblique inverse (`\`) est utilisé pour tels que les caractères d’échappement `\t` (onglet), `\n` (saut de ligne) et les instances d’encadrement guillemet.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Littéraux de chaîne masquée

Littéraux de chaîne masquée sont des chaînes qui Analytique masquent lors de la sortie de la chaîne (par exemple, lorsque le traçage). Le processus de masquage remplace les caractères tout masquées par un démarrage (`*`) caractère.

Pour former un littéral de chaîne masqué, ajoutez `h` ou 'H'. Par exemple :

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparaisons de chaînes

Opérateur|Description|La casse|Exemple true
---|---|---|---
`==`|Égal à |Oui| `"aBc" == "aBc"`
`<>` `!=`|Pas égal à|Oui| `"abc" <> "ABC"`
`=~`|Égal à |N°| `"abc" =~ "ABC"`
`!~`|Pas égal à |N°| `"aBc" !~ "xyz"`
`has`|Right côté (RHS) est un terme dans gauche-droite-partie entière|N°| `"North America" has "america"`
`!has`|RHS n’est pas un terme complè dans LHS|N°|`"North America" !has "amer"` 
`hasprefix`|RHS est un préfixe d’un terme dans LHS|N°|`"North America" hasprefix "ame"`
`!hasprefix`|RHS n’est pas un préfixe des termes dans LHS|N°|`"North America" !hasprefix "mer"`
`hassuffix`|RHS est un suffixe d’un terme dans LHS|N°|`"North America" hassuffix "rth"`
`!hassuffix`|RHS n’est pas un suffixe de n’importe quel contrat dans LHS|N°|`"North America" !hassuffix "mer"`
`contains` | RHS se produit comme sous-chaîne LHS|N°| `"FabriKam" contains "BRik"`
`!contains`| RHS n’apparaît pas dans LHS|N°| `"Fabrikam" !contains "xyz"`
`containscs` | RHS se produit comme sous-chaîne LHS|Oui| `"FabriKam" contains "Kam"`
`!containscs`| RHS n’apparaît pas dans LHS|Oui| `"Fabrikam" !contains "Kam"`
`startswith`|RHS est une sous-chaîne initiale de LHS.|N°|`"Fabrikam" startswith "fab"`
`!startswith`|RHS n’est pas une sous-chaîne initiale de LHS.|N°|`"Fabrikam" !startswith "abr"`
`endswith`|RHS est une sous-chaîne terminal de LHS.|N°|`"Fabrikam" endswith "kam"`
`!endswith`|RHS n’est pas une sous-chaîne terminal de LHS.|N°|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contient une correspondance pour RHS|Oui| `"Fabrikam" matches regex "b.*k"`
`in`|Égal à un des éléments|Oui|`"abc" in ("123", "345", "abc")`
`!in`|Est pas égale à un des éléments|Oui|`"bc" !in ("123", "345", "abc")`

Utiliser `has` ou `in` si vous testez la présence d’un terme tout lexical - autrement dit, un symbole ou un mot alphanumérique délimitée par des caractères non-alphanumériques ou de début ou de fin du champ. `has`effectue plus rapide `contains`, `startswith` ou `endswith`. La première de ces requêtes s’exécute plus rapidement :

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Compte nombre d’occurrences d’une sous-chaîne dans une chaîne. Chaîne simple correspondances peuvent se chevauchent ; correspondances Regex ne pas.

**Syntaxe**

    countof(text, search [, kind])

**Arguments**

* *texte :* Une chaîne.
* *recherche :* La chaîne simple ou l’expression régulière pour faire correspondre à l’intérieur de *texte*.
* *type :* `"normal"|"regex"` Par défaut `normal`. 

**Renvoie**

Le nombre de fois que la chaîne de recherche peut être mise en correspondance dans le conteneur. Chaîne simple correspondances peuvent se chevauchent ; correspondances Regex ne pas.

**Exemples**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (pas 2 !)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>extraire

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtenir une correspondance pour une [expression régulière](#regular-expressions) à partir d’une chaîne de texte. Si vous le souhaitez, il convertit ensuite la sous-chaîne extraite au type indiqué.

**Syntaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Arguments**

* *regex :* Une [expression régulière](#regular-expressions).
* *captureGroup :* Un positif `int` constante indiquant le groupe capture à extraire. 0 représente la correspondance entière, 1 pour la valeur correspondance à la première « (« parenthèse') » dans l’expression régulière, 2 ou plus de parenthèses ultérieures.
* *texte :* A `string` à rechercher.
* *typeLiteral :* Un littéral de type facultatif (par exemple, `typeof(long)`). Si fourni, la sous-chaîne extraite est convertie en ce type. 

**Renvoie**

Si *regex* trouve une correspondance dans le *texte*: la sous-chaîne correspondante contre le groupe capture indiquée *captureGroup*, vous pouvez également converti en *typeLiteral*.

S’il n’existe aucune correspondance, ou d’échec de la conversion de type : `null`. 

**Exemples**

L’exemple de chaîne `Trace` rechercher une définition pour `Duration`. La correspondance est convertie en `real`, puis multipliée par une constante (`1s`) afin que `Duration` est de type `timespan`. Dans cet exemple, il est égal à 123.45 secondes :

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Cet exemple équivaut à `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notempty

    isempty("") == true

True si l’argument est une chaîne vide ou est null.
Voir aussi [isnull](#isnull).


**Syntaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Renvoie**

Indique si l’argument est une chaîne vide ou isnull.

|x|IsEmpty(x)
|---|---
| "" | Vrai
|« x » | faux
|parseJSON("")|Vrai
|parseJSON("[]")|faux
|parseJSON("{}")|faux


**Exemple**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>ParseURL

Fractionner une URL de ses parties.

**Syntaxe**

    parseurl(urlstring)

**Arguments**

* *urlstring :* UNE URL.

**Renvoie**

Objet contenant les parties comme des chaînes.

**Exemple**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>remplacer

Remplacez toutes les correspondances regex par une autre chaîne.

**Syntaxe**

    replace(regex, rewrite, text)

**Arguments**

* *regex :* [Expressions régulières](https://github.com/google/re2/wiki/Syntax) pour rechercher du *texte*. Il peut contenir des groupes de capture entre parenthèses » (« ») ». 
* *réécriture :* Regex de remplacement pour une correspondance apportée par *matchingRegex*. Utiliser `\0` pour faire référence à la totalité de la correspondance, `\1` pour le premier groupe capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *texte :* Une chaîne.

**Renvoie**

*texte* après le remplacement de toutes les correspondances de *regex* avec les évaluations de *la réécriture de*. Des correspondances ne se chevauchent pas.

**Exemple**

Cette déclaration :

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Contient les résultats suivants :

| x    | Str | remplacer|
|---|---|---|
| 1    | Nombre est 1.000000  | Nombre était : 1.000000|
| 2    | Nombre est 2.000000  | Nombre était : 2.000000|
| 3    | Nombre est 3.000000  | Nombre était : 3.000000|
| 4    | Nombre est 4.000000  | Nombre était : 4.000000|
| 5    | Nombre est 5,000000  | Nombre était : 5,000000|
 



### <a name="split"></a>Fractionner

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Fractionner une chaîne donnée selon un délimiteur donnée et renvoie un tableau avec les sous-chaînes conatined. Vous pouvez également une sous-chaîne spécifique peut être retourné si existe.

**Syntaxe**

    split(source, delimiter [, requestedIndex])

**Arguments**

* *source*: la chaîne source qui sera fractionnée selon le délimiteur donné.
* *le délimiteur*: le séparateur qui sera utilisé pour fractionner la chaîne source.
* *requestedIndex*: un index de base zéro facultatif `int`. Si fourni, le tableau de chaîne retournée contiendra la sous-chaîne demandée si existe. 

**Renvoie**

Tableau de chaînes qui contient les sous-chaînes de la chaîne source donnée sont délimités par le délimiteur donné.

**Exemples**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatène entre 1 et 16 arguments, qui doivent être des chaînes.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Longueur d’une chaîne.

### <a name="substring"></a>sous-chaîne

    substring("abcdefg", 1, 2) == "bc"

Extraire une sous-chaîne d’une chaîne source donné à partir d’un index donné. Vous pouvez également la longueur de la sous-chaîne demandée peut être spécifiée.

**Syntaxe**

    substring(source, startingIndex [, length])

**Arguments**

* *source :* La chaîne source est extraite la sous-chaîne.
* *startingIndex :* Base zéro départ position du caractère de la sous-chaîne demandée.
* *longueur :* Paramètre facultatif qui peut être utilisé pour spécifier le nombre de caractères demandé dans la sous-chaîne. 

**Renvoie**

Une sous-chaîne à partir de la chaîne donnée. La sous-chaîne commence à la position du caractère startingIndex (base zéro) et continue jusqu'à la fin des chaîne ou longueur caractères si spécifié.

**Exemples**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>ToLower

    tolower("HELLO") == "hello"

Convertit une chaîne en minuscules.

### <a name="toupper"></a>ToUpper

    toupper("hello") == "HELLO"

Convertit une chaîne en majuscules.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Tableaux, objets et dynamique

[littéraux](#dynamic-literals) | [cast](#casting-dynamic-objects) | [opérateurs](#operators) | [permettre aux clauses](#dynamic-objects-in-let-clauses)
<br/>
[ArrayLength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [plage](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Voici le résultat d’une requête sur une exception d’analyse de l’Application. La valeur de `details` est un tableau.

![](./media/app-insights-analytics-reference/310.png)

**L’indexation :** Indexer des tableaux et les objets à l’instar d’un code JavaScript :

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mais utiliser `arraylength` et d’autres fonctions Analytique (pas « .length « !)

**Un cast** Dans certains cas, il est nécessaire convertir un élément que vous extraire à partir d’un objet, car son type peut varier. Par exemple, `summarize...to` a besoin d’un type spécifique :

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Littéraux** Pour créer un tableau explicite ou l’objet sac de propriétés, écrivez-le en tant que chaîne JSON et cast :

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand :** Pour décomposer les propriétés d’un objet dans des lignes distinctes, utilisez mvexpand :

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath :** Pour trouver tous les chemins d’accès dans un objet complexe :

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema :** Pour trouver le schéma minimal qui n’admet toutes les valeurs de l’expression dans la table :

    exceptions | summarize buildschema(details)

Résultat :

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Notez que `indexer` est utilisée pour indiquer l’endroit où vous devez utiliser un index numérique. Pour ce schéma, certains chemins d’accès valides (en cas de que ces index exemple se trouvent dans la plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Littéraux Array et object

Pour créer un littéral dynamique, utilisez `parsejson` (alias `todynamic`) avec une JSON argument de chaîne :

* `parsejson('[43, 21, 65]')`-un tableau de nombres
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-une valeur unique de type dynamique contenant un nombre
* `parsejson('"21"')`-une valeur unique de type dynamique contenant une chaîne

Notez que, contrairement à JavaScript, JSON exige l’utilisation de guillemets doubles (`"`) autour des chaînes. Par conséquent, il est généralement plus facile de devis une chaîne codée au format JSON littéraux à l’aide des guillemets simples (`'`).

Cet exemple crée une valeur dynamique, puis utilise ses champs :

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Fonctions de l’objet dynamique

|||
|---|---|
| *valeur* `in` *tableau*| True si un élément de *tableau* qui == *valeur*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valeur* `!in` *tableau*| True si aucun élément de *tableau* qui == *valeur*
|[`arraylength(`tableau`)`](#arraylength)| Null si elle n’est pas un tableau
|[`extractjson(`chemin d’accès, objet`)`](#extractjson)|Utilise le chemin d’accès pour naviguer dans l’objet.
|[`parsejson(`source`)`](#parsejson)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`à partir de, à l’étape`)`](#range)| Un tableau de valeurs
|[`mvexpand`listColumn](#mvexpand-operator) | Réplique une ligne pour chaque valeur dans une liste dans une cellule spécifiée.
|[`summarize buildschema(`colonne`)`](#buildschema) |Déduit le schéma de type de contenu de colonne
|[`summarize makelist(`colonne`)`](#makelist)| Fusionne les groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize makeset(`colonne`)`](#makeset) | Fusionne les groupes de lignes et place les valeurs de la colonne dans un tableau, sans doublons.

### <a name="dynamic-objects-in-let-clauses"></a>Objets dynamiques dans des clauses laisser


[Permettre aux clauses](#let-clause) store valeurs dynamiques comme des chaînes, auquel cas ces deux clauses sont équivalents, et les deux doivent être la `parsejson` (ou `todynamic`) avant d’être utilisé :

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>ArrayLength

Le nombre d’éléments dans un tableau dynamique.

**Syntaxe**

    arraylength(array)

**Arguments**

* *tableau :* A `dynamic` valeur.

**Renvoie**

Le nombre d’éléments du *tableau*, ou `null` si *tableau* n’est pas un tableau.

**Exemples**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtenez un élément spécifié déconnecter un texte JSON en utilisant une expression de chemin d’accès. Vous pouvez également convertir la chaîne extraite à un type spécifique.


**Syntaxe**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Renvoie**

Cette fonction exécute une requête JsonPath dans la source de données qui contient une chaîne JSON valide, vous pouvez également convertir cette valeur convertie en fonction du troisième argument.



**Exemple**

La notation [fermant] et respectant la notation sont équivalents :

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Optimiser les performances**

* Appliquer des clauses where avant d’utiliser`extractjson()`
* Envisagez d’utiliser une correspondance d’expression régulière avec [Extraire](#extract) à la place. Cette exécution beaucoup plus rapide et est efficace si le JSON est généré à partir d’un modèle.
* Utiliser `parsejson()` si vous avez besoin extraire plusieurs valeurs à partir de la JSON.
* Envisager la JSON analysé à la réception par la déclaration du type de la colonne d’être dynamique.

### <a name="json-path-expressions"></a>Expressions JSON chemin d’accès

|||
|---|---|
|`$`|Objet racine|
|`@`|Objet en cours|
|`[0]`|Indice de tableau|
|`.`ou`[0]` | Enfant|

*(Nous n’actuellement implémenter des caractères génériques, récursivité, union ou secteurs.)*




### <a name="parsejson"></a>parseJSON

Interprète un `string` sous forme de [valeur JSON](http://json.org/)) et renvoie la valeur en tant que `dynamic`. Il est supérieur à l’aide de `extractjson()` lorsque vous avez besoin d’extraire plusieurs éléments d’un objet composé JSON.

**Syntaxe**

    parsejson(json)

**Arguments**

* *json :* Un document JSON.

**Renvoie**

Un objet de type `dynamic` spécifié par *json*.

**Exemple**

Dans l’exemple suivant, lors de la `context_custom_metrics` est un `string` qui ressemble à ceci : 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

puis le fragment suivant extrait la valeur de la `duration` emplacement dans l’objet et à partir de ce qu’il récupère deux emplacements, `duration.value` et  `duration.min` (`118.0` et `110.0`, respectivement).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>plage

La `range()` fonction (à ne pas confondre avec la `range` opérateur) génère un tableau dynamique contenant une série de valeurs espacées de façon égale.

**Syntaxe**

    range(start, stop, step)

**Arguments**

* *Démarrer :* La valeur du premier élément dans le tableau qui en résulte. 
* *Arrêter :* La valeur du dernier élément dans le tableau résultant, ou la valeur minimale qui est supérieure au dernier élément dans le tableau résultant et au sein d’un nombre entier multiple de l' *étape* à partir *de début*.
* *étape :* La différence entre deux éléments consécutifs du tableau.

**Exemples**

L’exemple suivant retourne `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

L’exemple suivant retourne un tableau contenant tous les jours de l’année 2015 :

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convertit une chaîne en une valeur dynamique.

### <a name="treepath"></a>TreePath

    treepath(dynamic_object)

Énumère toutes les expressions de chemin d’accès qui identifient les feuilles en un objet dynamique. 

**Renvoie**

Un tableau d’expressions chemin d’accès.

**Exemples**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Remarque Cette « [0] » indique la présence d’un tableau, mais n’indique pas l’index utilisé par un chemin d’accès spécifique.

### <a name="zip"></a>ZIP

    zip(list1, list2, ...)

Associe un ensemble de listes dans une liste de tuples.

* `list1...`: Une liste de valeurs

**Exemples**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Noms

Noms peuvent être jusqu'à 1 024 caractères. Ils respectent la casse et peuvent contenir des lettres, des chiffres et des traits de soulignement (`_`). 

Un nom à l’aide de devis ['... '] or [" ... «] pour inclure d’autres caractères ou utiliser un mot clé comme un nom. Par exemple :

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' chemin d’accès\\fichier. utl\n\'x\''] | Utiliser \ des caractères d’échappement|
|[« d-e.=/f#\n »] | |
|[@'path\file'] | Aucun échappement - \ est littéral|
|[@"\now& puis\"] | |
|[où] | À l’aide d’un mot clé en tant que nom|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


