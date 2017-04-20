<properties
    pageTitle="Notation profils (Azure reste Version de l’API 2015-02-28-aperçu de la recherche) | Microsoft Azure | Aperçu de la recherche Azure API"
    description="Recherche Azure est un service de recherche de cloud hébergé qui prend en charge l’optimisation de résultats classés en fonction des profils score définis par l’utilisateur."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Profils score (Azure reste Version de l’API 2015-02-28-aperçu de la recherche)

> [AZURE.NOTE] Cet article décrit les profils score dans [2015-02-28-aperçu](search-api-2015-02-28-preview.md). Il n’existe actuellement aucune différence entre la `2015-02-28` version présentée sur [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) et la `2015-02-28-Preview` version décrites ici, mais nous proposons quand même ce document afin de fournir la couverture du document au sein de l’API entière.

## <a name="overview"></a>Vue d’ensemble

Notation fait référence au calcul d’un résultat de recherche pour tous les éléments renvoyés dans les résultats de recherche. Le score est un indicateur d’importance d’un élément dans le contexte de l’opération de recherche en cours. Les plus pertinentes, plus le score l’élément. Dans les résultats de recherche, les éléments sont rang classée de haute à faible, en fonction de la note recherche calculée pour chaque élément.

Recherche Azure utilise par défaut score pour calculer un score initial, mais vous pouvez personnaliser le calcul à travers un profil score. Profils score vous offrent un meilleur contrôle sur le classement d’éléments dans les résultats de recherche. Par exemple, vous souhaiterez peut-être améliorer les éléments en fonction de leur potentiel de chiffre d’affaires, promouvoir les éléments les plus récents ou peut-être améliorer les éléments qui ont été en stock trop long.

Un profil score fait partie de la définition d’index composé de champs, fonctions et les paramètres de recherche.

Pour vous donner une idée de ce à quoi ressemble un profil score, l’exemple suivant montre un profil simple nommé « geo ». Celui-ci améliore les éléments qui ont le terme de recherche dans les `hotelName` champ. Elle utilise également le `distance` fonction pour favoriser les éléments qui se trouvent dans dix kilomètres de l’emplacement actuel. Si un utilisateur effectue une recherche sur le terme « DCI » et « DCI » se trouve le nom hôtel du, documents incluant hôtel avec 'DCI' apparaissent en hauts dans les résultats de recherche.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Pour utiliser ce profil score, votre requête est conçue pour spécifier le profil dans la chaîne de requête. Dans la requête ci-dessous, notez le paramètre de requête `scoringProfile=geo` dans la demande.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Cette requête effectue une recherche sur le terme « DCI » et passe à l’emplacement actuel. Notez que cette requête contient d’autres paramètres, tels que `scoringParameter`. Paramètres de la requête sont décrites dans [Rechercher des Documents (API de recherche Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Cliquez sur l' [exemple](#example) pour passer en revue un exemple plus détaillé d’un profil score.

## <a name="what-is-default-scoring"></a>Qu’est score par défaut ?

Notation calcule un score de recherche pour chaque élément dans un jeu de résultats classés rang. Chaque élément dans un jeu de résultats de recherche est affecté un résultat de recherche, puis classé plus élevée à la plus basse. Éléments dont le score le plus élevé sont retournés à l’application. Par défaut, les 50 premières sont retournés, mais vous pouvez utiliser la `$top` paramètre à retourner un réduire ou agrandir le nombre d’éléments (jusqu'à 1000 dans une seule réponse).

Par défaut, un résultat de recherche est calculée en fonction de propriétés statistiques des données et la requête. Recherche Azure trouve tous les documents qui incluent les termes de recherche dans la chaîne de requête (tout ou partie, selon `searchMode`), favorise documents contenant plusieurs instances du terme de recherche. Le résultat de la recherche remonte accrue si le terme est rare entre le corpus de données, mais commune au sein du document. La base de cette approche à la pertinence informatique est appelée TF fil ou (fréquence de document fréquence inverse termes).

En supposant qu’il n’est pas un tri personnalisé, puis de classement des résultats par score recherche avant d’être renvoyés à l’application d’appel. Si `$top` n’est pas spécifié, 50 éléments ayant la plus élevée recherche score est retournés.

Valeurs de résultat de recherche peuvent être répétés dans l’ensemble d’un jeu de résultats. Par exemple, vous devrez 10 éléments dont le score de 1,2 en 20 éléments dont le score de 1,0 et 20 éléments dont le score de 0,5. Lorsque plusieurs demandes d’accès ont la même note de recherche, l’ordre des mêmes éléments évaluées n’est pas définie et n’est pas stable. Exécuter la requête à nouveau et vous pouvez voir les éléments MAJ position. Étant donné deux éléments avec un score identique, il n’existe aucune garantie que celui qui s’affiche en premier.

## <a name="when-to-use-custom-scoring"></a>Quand utiliser score personnalisé

Vous devez créer un ou plusieurs profils score lorsque la valeur par défaut classement comportement je ne parviens pas suffisamment loin en réunion objectifs de votre entreprise. Par exemple, vous pouvez décider de pertinence de la recherche doit de préférence éléments nouvellement ajoutés. De même, vous pouvez avoir un champ qui contient les pertes et profits, ou tout autre champ indiquant que le chiffre d’affaires potentiel. Augmentation accès par votre entreprise tirer parti peut être un facteur important de décider d’utiliser des profils score.

Classement en fonction de la pertinence est également implémenté par notation profils. Prendre en compte les pages de résultats de recherche que vous avez utilisées par le passé qui vous permettent de trier par prix, date, classement ou pertinence. Dans Rechercher Azure, profils score lecteur l’option « pertinence ». La définition de la pertinence est contrôlée par vous, basée sur les objectifs de l’entreprise et le type de l’expérience de recherche que vous voulez effectuer.

<a name="example"></a>
## <a name="example"></a>Exemple

Comme indiqué, notation personnalisées est implémentée par notation profils définis dans un schéma d’index.

Cet exemple montre le schéma d’un index avec deux profils score (`boostGenre`, `newAndHighlyRated`). Une requête avec cet index qui comprend un profil comme un paramètre de requête utilisera le profil pour évaluer le jeu de résultats.

[Essayez de cet exemple](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Flux de travail

Pour implémenter un comportement score personnalisé, ajouter un profil score au schéma qui définit l’index. Vous pouvez avoir plusieurs profils score au sein d’un index, mais vous ne pouvez spécifier un profil au moment dans une requête donnée.

Commencer avec le [modèle](#bkmk_template) fournies dans cette rubrique.

Attribuez un nom. Profils score sont facultatives, mais si vous ajoutez une, le nom est obligatoire. Veillez à respecter les conventions d’appellation pour les champs (commence par une lettre, permet d’éviter les caractères spéciaux et des mots clés réservés). Pour plus d’informations, voir [Règles d’appellation](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

Le corps du profil score est construit à partir des champs pondérées et fonctions.

### <a name="weights"></a>Poids ###

La `weights` propriété d’un profil score spécifie les paires nom-valeur qui assigner une épaisseur relative à un champ. Dans l' [exemple](#example), les champs albumTitle, genre et artistName sont augmentée 1,5, 5 et 2, respectivement. Pourquoi est genre augmentée beaucoup plus élevé que les autres colonnes ? Si la recherche est effectuée sur des données sont un peu homogènes (comme c’est le cas avec 'genre' dans la `musicstoreindex`), vous devrez peut-être une plus grande variation dans les pondérations relatives. Par exemple, dans le `musicstoreindex`, « rock » apparaît en tant que les deux un genre et dans les descriptions de manière identique exprimée genre. Si vous voulez genre à emporter sur description genre, le champ genre devrez une épaisseur relative beaucoup plus élevée.

### <a name="functions"></a>Fonctions ###

Fonctions sont utilisées lorsque des calculs supplémentaires sont requises pour des contextes spécifiques. Types de fonction valide sont `freshness`, `magnitude`, `distance` et `tag`. Chaque fonction a des paramètres qui sont spécifiques.

  - `freshness`doit être utilisé lorsque vous voulez accroître par la nouvel ou l’ancien un élément est. Cette fonction peut uniquement être utilisée avec des champs de date/heure (`Edm.DataTimeOffset`). Remarque la `boostingDuration` attribut est utilisé uniquement avec la fonction de l’actualisation.
  - `magnitude`doit être utilisé lorsque vous voulez améliorer l’aspect en fonction de la hauteur ou est une valeur numérique faible. Scénarios d’appel pour cette fonction incluent augmentation par pertes et profits, prix le plus élevé, prix le plus bas ou un compteur de téléchargements. Vous pouvez inverser la plage, haute sur faible, si vous souhaitez que le modèle inverse (par exemple, pour les éléments Ampli prix plus bas plusieurs éléments est visible). Étant donné une plage de prix de 100 euros à $1, vous devez définir `boostingRangeStart` à 100 et `boostingRangeEnd` à 1 pour améliorer les éléments à prix plus bas. Cette fonction peut uniquement être utilisée avec des champs de type double et entier.
  - `distance`doit être utilisé lorsque vous souhaitez augmenter à proximité ou emplacement géographique. Cette fonction peut uniquement être utilisée avec `Edm.GeographyPoint` champs.
  - `tag`doit être utilisé lorsque vous souhaitez améliorer l’aspect de balises en commun entre des documents et des requêtes de recherche. Cette fonction peut uniquement être utilisée avec `Edm.String` et `Collection(Edm.String)` champs.
  
#### <a name="rules-for-using-functions"></a>Règles d’utilisation des fonctions ####

  - Type de fonction (l’actualisation, ampleur, distance, balise) doit être en minuscules.
  - Fonctions ne peut pas inclure des valeurs nulles ou vides. Plus précisément, si vous incluez fieldname, vous devez configurer pour qu’elle soit.
  - Fonctions peuvent uniquement être appliquées aux champs filtrables. Pour plus d’informations sur les champs filtrables, voir [Créer un Index](search-api-2015-02-28-preview.md#CreateIndex) .
  - Fonctions peuvent uniquement être appliquées aux champs qui sont définis dans la collection de champs d’un index.

Une fois que l’index est défini, générer l’index en téléchargeant le schéma d’index, suivi de documents. Pour plus d’informations sur ces opérations, voir [Créer un Index](search-api-2015-02-28-preview.md#CreateIndex) et [Ajouter ou mettre à jour des Documents](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) . Une fois l’index créé, vous devez avoir un profil notation fonctionnel qui fonctionne avec vos données de recherche.

<a name="bkmk_template"></a>
## <a name="template"></a>Modèle
Cette section présente la syntaxe et le modèle pour le score profils. Reportez-vous à la [référence de l’attribut Index](#bkmk_indexref) dans la section suivante pour obtenir une description des attributs.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
            ...
          }
        },
        "functions": (optional) [
          {
            "type": "magnitude | freshness | distance | tag",
            "boost": # (positive number used as multiplier for raw score != 1),
            "fieldName": "...",
            "interpolation": "constant | linear (default) | quadratic | logarithmic",

            "magnitude": {
              "boostingRangeStart": #,
              "boostingRangeEnd": #,
              "constantBoostBeyondRange": true | false (default)
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Notation de référence des propriétés de profil

**Remarque** Une fonction de notation peut uniquement être appliquée aux champs filtrables.

| Propriété | Description |
|----------|-------------|
| `name`   | Obligatoire. Il s’agit du nom du profil score. Il suit les mêmes conventions d’attribution de nom d’un champ. Il doit commencer par une lettre, ne peut pas contenir des points, deux-points ou @ symboles et ne peut pas démarrer avec l’expression « azureSearch » (la casse). |
| `text` | Contient la propriété épaisseurs. |
| `weights` | Facultatif. Paire nom-valeur qui spécifie un nom de champ et une épaisseur relative. Épaisseur relative doit être un nombre entier positif ou un nombre à virgule flottante. Vous pouvez spécifier le nom du champ sans un poids correspondant. Poids sont utilisées pour indiquer l’importance d’un champ par rapport aux autres. |
| `functions` | Facultatif. Notez qu’une fonction de notation peut uniquement être appliquée aux champs filtrables. |
| `type` | Requis pour les fonctions de score. Indique le type de fonction à utiliser. Les valeurs valides incluent `magnitude`, `freshness`, `distance` et `tag`. Vous pouvez inclure plusieurs fonctions dans chaque profil score. Le nom de la fonction doit être en minuscules. |
| `boost` | Requis pour les fonctions de score. Un nombre positif utilisé comme multiplicateur pour score brut. Il ne peut pas être égale à 1. |
| `fieldName` | Requis pour les fonctions de score. Une fonction de notation peut uniquement être appliquée aux champs qui font partie de la collection de champs de l’index, et qui filtrables. En outre, chaque type de la fonction présente des restrictions supplémentaires (l’actualisation est utilisée avec des champs de date/heure, ampleur avec entier ou appuyez deux fois sur champs, distance avec des champs de l’emplacement et la balise avec chaîne ou champs de chaîne de collection de sites). Vous ne pouvez spécifier un seul champ par définition de la fonction. Par exemple, pour utiliser ampleur deux fois dans le même profil, vous devez inclure deux ampleur définitions, un pour chaque champ. |
| `interpolation` | Requis pour les fonctions de score. Définit la pente dont le score augmentation augmentations à partir du début de la plage à la fin de la plage. Les valeurs valides incluent `linear` (par défaut), `constant`, `quadratic`, et `logarithmic`. Pour plus d’informations, voir [définir interpolations](#bkmk_interpolation) . |
| `magnitude` | L’amplitude score fonction permet de modifier les classements en fonction de la plage de valeurs d’un champ numérique. Parmi les plus courants l’utilisation de ce sont :<ul><li>Classements avec des étoiles : modifier le système d’évaluation en fonction de la valeur dans le champ « Étoiles ». Lorsque deux éléments sont pertinentes, l’élément avec le contrôle d’accès supérieur s’affichera tout d’abord.</li><li>Marge : Lorsque deux documents sont pertinentes, un revendeur souhaiterez améliorer l’aspect de documents qui ont tout d’abord les marges supérieures.</li><li>Cliquez sur nombre : pour les applications qui effectuent le suivi, cliquez à l’aide du produits ou des pages, vous pouvez aussi utiliser ampleur aux éléments Ampli susceptibles d’obtenir le meilleur du trafic.</li><li>Télécharger des décomptes : pour les applications que le suivi des téléchargements, la fonction permet à ampleur vous améliorer l’aspect des éléments qui ont le meilleur parti des téléchargements.</li></ul> |
| `magnitude:boostingRangeStart` | Définit la valeur de départ de la plage sur laquelle ampleur est un score. La valeur doit être un nombre entier ou un nombre à virgule flottante. Pour les évaluations de 1 à 4, il s’agit de 1. Pour plus de 50 % des marges, il s’agit de 50. |
| `magnitude:boostingRangeEnd` | Définit la valeur de fin de la plage sur laquelle ampleur est un score. La valeur doit être un nombre entier ou un nombre à virgule flottante. Pour les évaluations de 1 à 4, il s’agit 4. |
| `magnitude:constantBoostBeyondRange` | Les valeurs valides sont VRAI ou faux (par défaut). Lorsque la valeur true, l’Ampli complète continuent à appliquer aux documents qui ont une valeur du champ cible qui est supérieure à l’extrémité supérieure de la plage. Si faux, l’augmentation de cette fonction ne seront pas appliquée à des documents ayant une valeur du champ cible qui se trouve en dehors de la plage. |
| `freshness` | L’actualisation score fonction permet de modifier les informations de classement pour les éléments en fonction des valeurs dans les champs DateTimeOffset. Par exemple, un élément avec une date la plus récente peut être classé plus élevé que les éléments plus anciens. (Notez qu’il est également possible de classement éléments tels que des événements de calendrier avec des dates futures telles que les éléments plus près du présent peuvent être classés supérieure à celle des éléments supplémentaires à l’avenir.) Dans la version actuelle de service, une extrémité de la plage sera corrigée à l’heure actuelle. L’autre extrémité est une fois par le passé en fonction de la `boostingDuration`. Pour augmenter un intervalle de temps à l’avenir utiliser une valeur négative `boostingDuration`. La fréquence à laquelle l’augmentation les modifications provenant de maximum et plage minimale est déterminé par l’Interpolation appliqué au profil score (voir l’illustration ci-dessous). Pour inverser le facteur d’augmentation appliqué, choisissez un facteur Ampli inférieur à 1. |
| `freshness:boostingDuration` | Définit une période d’expiration après les augmentation arrêtera pour un document spécifique. Voir [jeu boostingDuration] [bkmk_boostdur #] dans la section suivante pour la syntaxe et des exemples. |
| `distance` | La distance fonction score est utilisée pour affecter la note de documents basés sur la façon de ferme ou bien qu’ils sont par rapport à un emplacement géographique de référence. L’emplacement de référence est donnée dans le cadre de la requête dans un paramètre (à l’aide de la `scoringParameter` paramètre de requête) comme un lon, argument locales. |
| `distance:referencePointParameter` | Paramètre à transmettre dans les requêtes à utiliser comme emplacement de référence. scoringParameter est un paramètre de requête. Pour obtenir une description des paramètres de requête, voir [Rechercher des Documents](search-api-2015-02-28-preview.md#SearchDocs) . |
| `distance:boostingDistance` | Nombre qui indique la distance en kilomètres à partir de l’emplacement de référence où se termine la plage augmentation. |
| `tag` | La balise score fonction est utilisée pour affecter le score de documents en fonction de balises dans des documents et des requêtes de recherche. Documents qui ont des balises en commun avec la requête de recherche seront être augmentées. Les balises de la requête de recherche est fourni sous forme d’un paramètre score dans chaque demande de recherche (à l’aide de la `scoringParameter` paramètre de requête). |
| `tag:tagsParameter` | Paramètre à transmettre dans des requêtes pour spécifier des balises à une requête particulière. `scoringParameter`est un paramètre de requête. Pour obtenir une description des paramètres de requête, voir [Rechercher des Documents](search-api-2015-02-28-preview.md#SearchDocs) . |
| `functionAggregation` | Facultatif. S’applique uniquement lorsque les fonctions sont spécifiées. Les valeurs valides incluent : `sum` (par défaut), `average`, `minimum`, `maximum`, et `firstMatching`. Un résultat de recherche est une valeur unique qui est calculée à partir de plusieurs variables, y compris plusieurs fonctions. Cette attributs indique comment l’optimisation de toutes les fonctions est combinées dans un seul Ampli agrégation puis appliqué au résultat de la base de document. Le score de base est basé sur la valeur tf fil calculée à partir du document et la requête de recherche. |
| `defaultScoringProfile` | Lorsque vous exécutez une requête de recherche, si aucun profil score n’est spécifié, puis score par défaut est utilisé (tf-fil uniquement). Notation nom du profil par défaut peut être définie ici, à l’origine de la recherche Azure pour utiliser ce profil lorsqu’aucun profil spécifique n’est indiquée dans la demande de recherche. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Définir des interpolations

Interpolations permettent de définir la pente dont le score augmentation augmentations à partir du début de la plage à la fin de la plage. Les interpolations suivantes peuvent être utilisées :

  - `Linear`: Pour les éléments qui se trouvent dans la plage max et min, l’Ampli appliqué à l’élément est effectué dans une quantité constamment décroissante. Linéaire est l’interpolation par défaut pour un profil score.
  - `Constant`: Pour les éléments qui se trouvent dans le début et de fin de la plage, une constante Ampli est appliquée aux résultats rangées.
  - `Quadratic`: Par rapport à une interpolation linéaire comportant un Ampli constamment décroissante, quadratique diminuera initiale s’exécute plus petites et puis elle approche de la plage de fin, elle diminue à intervalles beaucoup plus élevé. Cette option interpolation n’est pas autorisée dans balise score fonctions.
  - `Logarithmic`: Par rapport à une interpolation linéaire comportant un Ampli constamment décroissante, logarithmique diminuera initiale s’exécute une version ultérieure et puis elle approche de la plage de fin, il diminue intervalles une quantité inférieure. Cette option interpolation n’est pas autorisée dans balise score fonctions.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Jeu de boostingDuration

`boostingDuration`est un attribut de la fonction de l’actualisation. Il permet de définir un délai d’expiration point après les augmentation arrêtera pour un document spécifique. Par exemple, pour améliorer l’aspect une ligne ou une marque pendant la période promotionnelle 10 jours, vous devez spécifier la période de 10 jours que « P10D » pour les documents. Ou à améliorer l’aspect des événements à venir de la semaine suivante spécifient «-P7D ».

`boostingDuration`doit être mis en forme comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de durée ISO8601). Le modèle pour cela est : `[-]P[nD][T[nH][nM][nS]]`.

Le tableau suivant fournit plusieurs exemples.

| Durée | boostingDuration |
|----------|------------------|
| jour 1 | « P1D » |
| 2 jours et 12 heures | « P2DT12H » |
| 15 minutes | « PT15M » |
| 30 jours, 5 heures 10 minutes, et 6.334 secondes | « P30DT5H10M6.334S » |

Pour plus d’exemples, voir [schéma XML : types de données (W3.org le site web)](http://www.w3.org/TR/xmlschema11-2/).

**Voir aussi**
[l’API REST de Service de recherche Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) sur MSDN <br/>
[Créer un Index (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) sur MSDN<br/>
[Ajouter un profil score à un index de recherche](http://msdn.microsoft.com/library/azure/dn798928.aspx) sur MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
