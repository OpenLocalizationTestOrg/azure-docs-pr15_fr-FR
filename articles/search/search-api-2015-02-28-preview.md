<properties
   pageTitle="Azure Service reste Version de l’API 2015-02-28-aperçu de la recherche | Microsoft Azure | Aperçu de la recherche Azure API"
   description="Azure Service reste Version de l’API 2015-02-28-aperçu de la recherche inclut expériences fonctionnalités telles que les recherches en langage naturel analyseurs et moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API REST de Service recherche Azure : Version 2015-02-28-aperçu

Cet article est la documentation de référence pour `api-version=2015-02-28-Preview`. Cet aperçu étend la version actuelle sera-t-il disponible, [version de l’api = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), en fournissant les fonctionnalités expériences suivantes :

- `moreLikeThis`requête avec paramètres dans les [Documents de recherche](#SearchDocs) API. Il recherche les autres documents pertinents pour un autre document spécifique.

Quelques composants supplémentaires de la `2015-02-28-Preview` API REST sont présentées séparément. Parmi lesquels :

- [Profils d’évaluation](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexeurs](search-api-indexers-2015-02-28-preview.md)

Service de recherche Azure est disponible dans les versions multiples. Pour plus d’informations, reportez-vous au [Service de recherche du contrôle de version](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

## <a name="apis-in-this-document"></a>API dans ce document

API de service de recherche Azure prend en charge deux comprend URL pour les opérations de l’API : simple et OData (voir [prise en charge d’OData (API de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) pour plus d’informations). La liste suivante présente la syntaxe de la simple.

[Créer un Index](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Mettre à jour d’Index](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obtenir des Index](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Liste des contacts à partir d’index](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obtenir les statistiques d’Index](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Analyseur de test](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Supprimer un Index](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Ajouter, supprimer et mettre à jour des données dans un Index](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Rechercher des Documents](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Document de recherche](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Nombre de Documents](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Suggestions](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Opérations d’index

Vous pouvez créer et gérer des index dans le service de recherche Azure via des requêtes HTTP simples (POST, GET, place, supprimer) par rapport à une ressource donnée index. Pour créer un index, que vous publiez tout d’abord un document JSON qui décrit le schéma d’index. Le schéma définit les champs de l’index, leurs types de données et comment ils peuvent être utilisés (par exemple, dans les recherches en texte intégral, filtres, un tri ou faceting). Il définit également les profils score, suggesters et autres attributs pour configurer le comportement de l’index.

L’exemple suivant fournit une illustration d’un schéma utilisé pour la recherche sur les informations d’hôtel avec le champ Description défini dans deux langues. Notez comment attributs contrôlent la façon dont le champ est utilisé. Par exemple la `hotelId` est utilisé comme la clé de document (`"key": true`) et est exclu de recherches en texte intégral (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Une fois l’index créé, vous allez télécharger des documents qui remplissent l’index. Consultez [Ajouter ou mettre à jour des Documents](#AddOrUpdateDocuments) pour cette étape suivante.

Pour une vidéo d’introduction à l’indexation en Azure recherche, voir [canal 9 Cloud garde épisode Azure recherche](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Créer un Index

Un index est le principal moyen d’organiser et rechercher des documents dans recherche Azure, similaire à la manière dont une table organise les enregistrements dans une base de données. Chaque index a un ensemble de documents que tous sont conformes au schéma index (noms de champs, les types de données et propriétés), mais les index également spécifient constructions supplémentaires (suggesters, les profils score et options CORS) qui définissent les autres comportements de recherche.

Vous pouvez créer un nouvel index dans un service de recherche Azure à l’aide d’une demande HTTP POST ou place. Le corps de la demande est un schéma JSON qui spécifie les informations d’index et de configuration.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Par ailleurs, vous pouvez utiliser place et spécifier le nom de l’index de l’URI. Si l’index n’existe pas, il sera créé.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Création d’un index détermine la structure des documents stockés et utilisés dans les opérations de recherche. Remplissage de l’index est une opération distincte. Pour cette étape, vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponible pour les sources de données prises en charge) ou une opération [d’Ajout, mise à jour ou supprimer des Documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) . L’index inversée est générée lorsque les documents sont publiées.

**Remarque**: le nombre maximal d’index autorisé varie selon le niveau de prix. Le service gratuit permet jusqu'à 3 à partir d’index. Service standard permet de 50 index par le service de recherche. Pour plus d’informations, voir [spécifications et limites contraintes](http://msdn.microsoft.com/library/azure/dn798934.aspx) .

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande **Create Index** peut être créée à l’aide de la méthode un billet ou le placer. Lorsque vous utilisez billet, vous devez fournir un nom de l’index dans le corps de la requête ainsi que la définition de schéma d’index. À la place, le nom de l’index fait partie de l’URL. Si l’index n’existe pas, il est créé. S’il existe déjà, il est mis à jour à la nouvelle définition.

Le nom de l’index doit être en minuscule, commencer par une lettre ou un chiffre, comporter aucun barres obliques ou points et être dépasser 128 caractères. Après avoir démarré le nom de l’index avec une lettre ou un chiffre, le reste du nom peut inclure une lettre, le numéro et tirets, dans la mesure où les tirets ne sont pas consécutifs.

La `api-version` est requis. Voir [Service de recherche du contrôle de version](http://msdn.microsoft.com/library/azure/dn864560.aspx) pour une liste des versions disponibles.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. La `api-key` est utilisée pour
- authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande de **Créer un Index** doit comporter une `api-key` en-tête défini sur votre clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

<a name="RequestData"></a>
**Syntaxe de la demande de corps**

Le corps de la requête contient une définition de schéma qui inclut la liste des champs de données au sein des documents recevoir dans cet index, types de données, attributs, ainsi qu’une liste facultative de notation profils qui sont utilisés pour le score documents correspondant au moment de la requête.

Notez que pour une demande de publication, vous devez spécifier le nom de l’index dans le corps de la requête.

Il ne peut être un champ de clé dans l’index. Il doit être un champ de chaîne. Ce champ représente l’identificateur unique pour chaque document stocké dans l’index.

Les parties principales d’un index sont les suivants :

- `name`
- `fields`qui est chargé dans cet index, y compris les propriétés qui définissent les actions autorisées sur ce champ nom et type de données.
- `suggesters`utilisé pour les requêtes de saisie semi-automatique ou semi-automatique.
- `scoringProfiles`utilisé pour le résultat de recherche personnalisé classement. Pour plus d’informations, voir [profils score ajouter](https://msdn.microsoft.com/library/azure/dn798928.aspx) .
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` permet de définir la façon dont vos documents/requêtes sont rompues en jetons/être indexés disponible pour la recherche. Pour plus d’informations, voir [analyse dans Azure rechercher](https://aka.ms//azsanalysis) .
- `defaultScoringProfile`permet de remplacer les comportements de notation par défaut.
- `corsOptions`Pour autoriser les requêtes d’origine croisée par rapport à votre index.

La syntaxe de la charge utile de demande de structuration est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Attributs d’index**

Les attributs suivants peuvent être définis lors de la création d’un index. Pour plus d’informations sur les profils d’évaluation et score, voir [Ajouter des profils score](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Définit le nom du champ.

`type`-Définit le type de données pour le champ. Pour une liste des types pris en charge, voir [Types de données pris en charge](#DataTypes) .

`searchable`-Marque le champ en tant que texte intégral en mesure de recherche. Cela signifie qu’il est soumises à analyse comme césure lors de l’indexation. Si vous définissez une `searchable` champ à une valeur telle que « jour de soleil », en interne il sera fractionné en les jetons individuels « jour » et « beau ». Cela permet de recherches en texte intégral pour ces termes. Champs de type `Edm.String` ou `Collection(Edm.String)` sont `searchable` par défaut. Champs d’autres types ne peuvent pas être `searchable`.

  - **Remarque**: `searchable` champs consomment de l’espace supplémentaire dans votre index dans la mesure où Azure recherche stockera une version sous forme de jeton supplémentaire de la valeur du champ pour les recherches en texte intégral. Si vous souhaitez économiser de l’espace dans votre index et vous n’avez pas besoin d’un champ à inclure dans les recherches, définissez `searchable` à `false`.

`filterable`-Permet du champ à référencer dans `$filter` requêtes. `filterable`diffère `searchable` dans le traitement des chaînes. Champs de type `Edm.String` ou `Collection(Edm.String)` qui sont `filterable` ne fassent pas l’objet de césure, afin que les comparaisons sont pour uniquement les correspondances exactes. Par exemple, si vous définissez ce type de champ `f` à « jour de soleil, » `$filter=f eq 'sunny'` ne trouveront aucune correspondance, mais `$filter=f eq 'sunny day'` sera. Tous les champs sont `filterable` par défaut.

`sortable`-Par défaut le système trie les résultats par score, mais dans nombreuses expériences utilisateurs souhaiteront trier les champs dans les documents. Champs de type `Collection(Edm.String)` ne peut pas être `sortable`. Tous les autres champs sont `sortable` par défaut.

`facetable`-Généralement utilisée dans une présentation de résultats de recherche qui inclut le nombre d’accès par catégorie (par exemple, recherchez photo et voir accès en marque, en mégapixels, par prix, etc.). Cette option ne peuvent pas être utilisée avec des champs de type `Edm.GeographyPoint`. Tous les autres champs sont `facetable` par défaut.

  - **Remarque**: les champs de type `Edm.String` qui sont `filterable`, `sortable`, ou `facetable` peut être au maximum 32 Ko longueur. C’est parce que ces champs sont traités comme un terme de recherche unique, et la longueur maximale d’un terme de recherche Azure est de 32 Ko. Si vous voulez stocker plus de texte plus dans le champ chaîne unique, vous devez définir explicitement `filterable`, `sortable`, et `facetable` à `false` dans la définition de votre index.

  - **Remarque**: si un champ a aucun des attributs ci-dessus définis sur `true` (`searchable`, `filterable`, `sortable`, ou`facetable`) le champ est efficacement exclu de l’index inversée. Cette option est utile pour les champs qui ne sont pas utilisés dans les requêtes, mais sont utiles dans les résultats de recherche. Ces champs à exclure de l’index améliore les performances.

`key`-Marque le champ comme contenant des identificateurs uniques pour les documents dans l’index. Exactement un champ doit être choisi comme le `key` champ et qu’il doivent être de type `Edm.String`. Champs de clé peuvent être utilisés pour rechercher des documents directement par le biais de l' [API de recherche](#LookupAPI).

`retrievable`-Définit si le champ peut être retourné dans un résultat de recherche.  Ceci est utile lorsque vous souhaitez utiliser un champ (par exemple, la marge) en tant que filtre, un tri ou notation mécanisme mais que vous ne souhaitez pas que le champ soit visible à l’utilisateur final. Cet attribut doit être `true` pour `key` champs.

`analyzer`-Définit le nom de l’analyseur à utiliser pour le champ au moment de la recherche et la durée d’indexation. Pour l’ensemble des valeurs autorisée voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec `searchable` champs et il ne peut pas être définies avec des options `searchAnalyzer` ou `indexAnalyzer`.  Une fois que l’analyseur est sélectionnée, elle ne peut pas être modifiée pour le champ.

`searchAnalyzer`-Définit le nom de l’analyseur utilisé au moment de recherche pour le champ. Pour l’ensemble des valeurs autorisée voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec `searchable` champs. Il doit être définie avec `indexAnalyzer` et ne peut pas être définie avec la `analyzer` option. Cet utilitaire d’analyse pouvant être mis à jour sur un champ existant.

`indexAnalyzer`-Définit le nom de l’analyseur utilisé au moment de l’indexation pour le champ. Pour l’ensemble des valeurs autorisée voir [analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec `searchable` champs. Il doit être définie avec `searchAnalyzer` et ne peut pas être définie avec la `analyzer` option. Une fois que l’analyseur est sélectionnée, elle ne peut pas être modifiée pour le champ.

`suggesters`-Définit le mode de la recherche et les champs qui sont à l’origine du contenu pour obtenir des suggestions. Pour plus d’informations, voir [Suggesters](#Suggesters) .

`scoringProfiles`-Définit la notation des comportements personnalisés qui vous permettent d’influencer les éléments qui apparaissent en hauts de résultats de recherche. Profils score sont constitués de fonctions et les pondérations des champs. Pour plus d’informations sur les attributs utilisés dans un profil score, voir [Ajouter des scores profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) .

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Prise en charge multilingue**

Champs de recherche sont soumis à analyse que la plupart implique souvent césure, normalisation du texte et le filtrage des termes. Par défaut, les champs de recherche dans Azure recherche sont analysées avec l' [Analyseur Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) qui décompose le texte en éléments qui suivent les règles[« Segmentation du texte Unicode »](http://unicode.org/reports/tr29/) . En outre, l’analyseur standard convertit tous les caractères en leur minuscules. Documents indexés et termes de recherche, passez par l’analyse lors de l’indexation et le traitement des requêtes.

Recherche Azure prend en charge plusieurs langues. Chaque langue nécessite un analyseur de texte non standard qui tient compte des caractéristiques d’une langue donnée. Recherche Azure propose deux types d’analyseurs :

- 35 analyseurs bénéficient Lucene.
- 50 analyseurs sauvegardées par propriétaires Microsoft en langage naturel traitement technologie utilisée dans Office et Bing.

Certains développeurs préfèrent la solution plus convivial, simple libres de Lucene. Analyseurs Lucene sont plus rapides, mais les analyseurs Microsoft ont avancées, telles que lemmatisation, word decompounding (dans les langues telles qu’allemand, danois, néerlandais, suédois, norvégien, estonien, terminer, hongrois, slovaque) et la reconnaissance entité (URL, messages électroniques, des dates, des numéros). Si possible, vous devez exécuter des comparaisons de Microsoft et Lucene analyseurs choisir celui qui est plus adaptée.

***Comment ils comparer***

L’analyseur Lucene pour l’anglais étend l’analyseur standard. Il supprime possessifs (du à droite) mots, s’applique radical en fonction de [l’algorithme de Porter radical](http://tartarus.org/~martin/PorterStemmer/)et supprime anglais [mots vides](http://en.wikipedia.org/wiki/Stop_words).

En revanche, Microsoft analyzer effectue lemmatisation au lieu de radical. Cela signifie qu’il peut gérer les formes du mot formes et irrégulier beaucoup mieux quels sont les résultats dans les résultats de recherche plus pertinents (espion module 7 de [présentation Azure recherche MVA](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) pour plus d’informations).

L’indexation avec analyseurs Microsoft est en moyenne deux ou trois fois plus lente que leurs équivalents Lucene, en fonction de la langue. Performances de la recherche ne doivent pas être considérablement affectées pour les requêtes de taille moyenne.

***Configuration***

Pour chaque champ dans la définition d’index, vous pouvez définir la `analyzer` propriété pour un nom de l’analyseur qui spécifie la langue et un fournisseur. Le même analyseur est appliqué lors de l’indexation et la recherche de ce champ.
Par exemple, vous pouvez avoir des champs distincts pour les descriptions hôtel anglais, Français et espagnol qui existent côte à côte dans le même index. Utilisez le [paramètre de requête « searchFields »](#SearchQueryParameters) pour spécifier le champ spécifiques à une langue pour effectuer une recherche sur dans vos requêtes. Vous pouvez examiner les exemples de requête qui contiennent les `analyzer` propriété de [Rechercher des Documents](#SearchDocs). 

***Liste de l’analyseur***

Voici la liste des langues prises en charge avec les noms de l’analyseur Lucene et Microsoft.

<table style="font-size:12">
    <tr>
        <th>Langue</th>
        <th>Nom de l’analyseur Microsoft</th>
        <th>Nom de l’analyseur Lucene</th>
    </tr>
    <tr>
        <td>Arabe</td>
        <td>ar.Microsoft</td>
        <td>ar.Lucene</td>      
    </tr>
    <tr>
        <td>Arménien</td>
        <td></td>
        <td>HY.Lucene</td>
    </tr>
    <tr>
        <td>Bangla</td>
        <td>bn.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Basque</td>
        <td></td>
        <td>eu.Lucene</td>
    </tr>
    <tr>
        <td>Bulgare</td>
        <td>BG.Microsoft</td>
        <td>BG.Lucene</td>
    </tr>
    <tr>
        <td>Catalan</td>
        <td>CA.Microsoft</td>
        <td>CA.Lucene</td>          
    </tr>
    <tr>
        <td>Chinois simplifié</td>
        <td>zh Hans.microsoft</td>
        <td>zh Hans.lucene</td>     
    </tr>
    <tr>
        <td>Chinois traditionnel</td>
        <td>zh Hant.microsoft</td>
        <td>zh Hant.lucene</td>     
    <tr>
    <tr>
        <td>Croate</td>
        <td>HR.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Tchèque</td>
        <td>cs.Microsoft</td>
        <td>cs.Lucene</td>      
    </tr>    
    <tr>
        <td>Danois</td>
        <td>DA.Microsoft</td>
        <td>DA.Lucene</td>      
    </tr>    
    <tr>
        <td>Néerlandais</td>
        <td>NL.Microsoft</td>
        <td>NL.Lucene</td>  
    </tr>    
    <tr>
        <td>Anglais</td>        
        <td>en.Microsoft</td>
        <td>en.Lucene</td>      
    </tr>
    <tr>
        <td>Estonien</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finnois</td>
        <td>Fi.Microsoft</td>
        <td>Fi.Lucene</td>      
    </tr>    
    <tr>
        <td>Français</td>
        <td>fr.Microsoft</td>
        <td>fr.Lucene</td>      
    </tr>
    <tr>
        <td>Galicien</td>
        <td></td>
        <td>GL.Lucene</td>      
    </tr>
    <tr>
        <td>Allemand</td>
        <td>de.Microsoft</td>
        <td>de.Lucene</td>      
    </tr>
    <tr>
        <td>Grec</td>
        <td>EL.Microsoft</td>
        <td>EL.Lucene</td>      
    </tr>
    <tr>
        <td>Gujarâtî</td>
        <td>gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hébreu</td>
        <td>HE.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.Lucene</td>      
    </tr>
    <tr>
        <td>Hongrois</td>      
        <td>HU.Microsoft</td>
        <td>HU.Lucene</td>
    </tr>
    <tr>
        <td>Islandais</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonésien (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.Lucene</td>      
    </tr>
    <tr>
        <td>Irlandais</td>
        <td></td>
        <td>GA.Lucene</td>
    </tr>
    <tr>
        <td>Italien</td>
        <td>IT.Microsoft</td>
        <td>IT.Lucene</td>      
    </tr>
    <tr>
        <td>Japonais</td>
        <td>ja.Microsoft</td>
        <td>ja.Lucene</td>
        
    </tr>
    <tr>
        <td>Canara</td>
        <td>KA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coréen</td>
        <td>Ko.Microsoft</td>
        <td>Ko.Lucene</td>
    </tr>
    <tr>
        <td>Letton</td>        
        <td>LV.Microsoft</td>
        <td>LV.Lucene</td>  
    </tr>
    <tr>
        <td>Lituanien</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malais (Latin)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marathi</td>
        <td>MR.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norvégien</td>
        <td>Nb.Microsoft</td>
        <td>no.Lucene</td>      
    </tr>
    <tr>
        <td>Perse</td>
        <td></td>
        <td>FA.Lucene</td>      
    </tr>
    <tr>
        <td>Polonais</td>
        <td>PL.Microsoft</td>
        <td>PL.Lucene</td>      
    </tr>
    <tr>
        <td>Portugais (Brésil)</td>
        <td>pt Br.microsoft</td>
        <td>pt Br.lucene</td>       
    </tr>
    <tr>
        <td>Portugais (Portugal)</td>
        <td>pt Pt.microsoft</td>        
        <td>pt Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>PA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Roumain</td>
        <td>RO.Microsoft</td>
        <td>RO.Lucene</td>
    </tr>
    <tr>
        <td>Russe</td>
        <td>RU.Microsoft</td>
        <td>RU.Lucene</td>  
    </tr>
    <tr>
        <td>Serbe (cyrillique)</td>
        <td>SR cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Serbe (Latin)</td>
        <td>SR latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovaque</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Slovène</td>
        <td>sl.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Espagnol</td>
        <td>es.Microsoft</td>
        <td>es.Lucene</td>
    </tr>
    <tr>
        <td>Suédois</td>
        <td>SV.Microsoft</td>
        <td>SV.Lucene</td>
    </tr>

    <tr>
        <td>Tamil</td>
        <td>TA.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Telugu</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Thaï</td>
        <td>th.Microsoft</td>
        <td>th.Lucene</td>
    </tr>
    <tr>
        <td>Turc</td>
        <td>TR.Microsoft</td>
        <td>TR.Lucene</td>      
    </tr>
    <tr>
        <td>Ukrainien</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>Your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamien</td>
        <td>VI.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">En outre recherche Azure fournit des configurations analyseur indépendant du langage</td>
    <tr>
        <td>Pliage ASCII standard</td>
        <td>standardasciifolding.Lucene</td>
        <td>
        <ul>
            <li>Segmentation texte Unicode (Générateur de jetons Standard)</li>
            <li>Filtre de pliage ASCII - convertit les caractères Unicode qui ne figurent dans le jeu de caractères ASCII 127 premiers dans leurs équivalents ASCII. Ceci est utile pour éliminer les signes diacritiques.</li>
        </ul>
        </td>
    </tr>
</table>

Tous les analyseurs avec des noms annotées avec <i>lucene</i> sont optimisés par [analyseurs de langue de Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Vous pouvez trouver plus d’informations sur la ASCII pliage filtre [ici](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` définit les champs dans un index sont utilisés pour prendre en charge la saisie semi-automatique dans les recherches. En règle générale chaînes de recherche partielle sont envoyés à l' [API Suggestions](#Suggestions) tandis que l’utilisateur tape une requête de recherche et de l’API renvoie un ensemble d’expressions suggérées. Un suggester que vous définissez dans l’index détermine les champs sont utilisés pour créer les termes de recherche semi-automatique. Pour plus d’informations, voir [Suggesters](#Suggesters) .

**Profils d’évaluation**

A `scoringProfile` définit score comportements personnalisés qui vous permettent d’influencer les éléments apparaissent plus dans les résultats de recherche. Profils score sont constitués de fonctions et les pondérations des champs. Pour les utiliser, vous spécifiez un profil par un nom dans la chaîne de requête.

Une valeur par défaut score profil fonctionne en arrière-plan pour calculer un résultat de recherche pour chaque élément dans un jeu de résultats. Vous pouvez utiliser interne, sans nom profil score. Vous pouvez également définir `defaultScoringProfile` à utiliser un profil personnalisé par défaut, appelé chaque fois qu’un profil personnalisé n’est pas spécifié dans la chaîne de requête.

Pour plus d’informations, voir [profils score ajouter à un index de recherche (API REST Service Azure recherche)](search-api-scoring-profiles-2015-02-28-preview.md) .

**Options CORS**

Code Javascript côté client ne peut pas appeler n’importe quel API par défaut dans la mesure où le navigateur bloquera toutes les demandes origine croisée. Activer le CORS (Cross-Origin partage des ressources) en définissant la `corsOptions` attribut pour autoriser les requêtes d’origine croisée à votre index. Remarque Cette requête uniquement prise en charge API CORS pour des raisons de sécurité. Les options suivantes peuvent être définies pour CORS :

- `allowedOrigins`(obligatoire) : il s’agit d’une liste des origines pourra accéder à votre index. Cela signifie que tout code Javascript provenant de ces origines pourra votre index (en supposant qu’il fournit la clé API correcte) de la requête. Chaque origine est en général du formulaire `protocol://fully-qualified-domain-name:port` bien que le port est souvent omis. Consultez [cet article](http://go.microsoft.com/fwlink/?LinkId=330822) pour plus d’informations.
 - Si vous voulez autoriser l’accès à toutes les origines, inclure `*` comme un seul élément dans la `allowedOrigins` tableau. Notez que **cela n’est pas recommandé relatives aux exercices pratiques pour les services de recherche de production.** Toutefois, il peut être utile de développement ou à des fins de débogage.
- `maxAgeInSeconds`(facultatif) : navigateurs utilisent cette valeur pour déterminer la durée (en secondes) en cache les réponses en amont CORS. Cela doit être un entier négatif. Plus cette valeur est, les meilleures performances seront, mais plus qu’il faudra pour CORS stratégie modifications prennent effet. Si elle n’est pas définie, une durée par défaut de 5 minutes est utilisée.

<a name="CreateUpdateIndexExample"></a>
**Exemple de corps de requête**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Réponse**

Pour une demande réussie : « 201 créé ».

Par défaut, le corps de réponse contient le JSON pour la définition d’index qui a été créé. Si la `Prefer` en-tête de la demande est défini sur `return=minimal`, le corps de réponse sera vide et le code d’état réussite sera « 204 sans contenu » au lieu de « 201 créé ». C’est vrai, que place ou POST a été utilisé pour créer l’index.

**Remarques**

Il n’existe actuellement prise en charge limitée des mises à jour de schéma index. Les mises à jour de schéma qui nécessitent la réindexation telles que la modification des types de champs ne sont pas actuellement pris en charge. Bien que des champs existants ne peut pas être modifiés ou supprimés, les nouveaux champs peuvent être ajoutés à un index existant à tout moment. Lorsque vous ajoutez un nouveau champ, tous les documents existants dans l’index a automatiquement une valeur null pour ce champ. Aucun espace de stockage supplémentaire n’est consommée jusqu'à ce que les nouveaux documents sont ajoutés à l’index.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

La fonctionnalité de suggestions de recherche Azure est une fonctionnalité de requête semi-automatique ou saisie semi-automatique, qui fournit une liste de termes de recherche potentiels en réponse aux entrées partielle des chaînes de saisie dans une zone de recherche. Vous avez probablement remarqué des suggestions de requête lors de l’utilisation des moteurs de recherche web professionnelle : tapant «.NET » dans Bing génère une liste de termes pour « .NET 4.5 », « .NET Framework 3.5 », et ainsi de suite. Lorsque vous utilisez le service de recherche API REST, mise en œuvre des suggestions dans une application de recherche Azure personnalisée requiert les éléments suivants :

- Activer les suggestions en ajoutant une construction **suggester** dans votre index, donnant le nom, le mode de recherche et une liste de champs pour lequel semi-automatique est appelé. Par exemple, si vous spécifiez « Nom_ville » comme un champ source, à taper une chaîne de recherche partielle de « Mer » entraînera dans « Seattle », « Coucher » et « Seatac » (toutes les trois sont des noms de ville réel) offert comme suggestions de requête pour l’utilisateur.

- Appeler des suggestions par l' [API de Suggestions de](#Suggestions) code de l’application. En règle générale chaînes de recherche partielle sont envoyés au service tandis que l’utilisateur tape une requête de recherche, et cette API renvoie un ensemble d’expressions suggérées.

Cet article explique comment configurer un **suggester**. Vous devez également examiner l' [API Suggestions](#Suggestions) pour plus d’informations sur l’utilisation d’un suggester.

**Utilisation**

`Suggesters`sont créés dans l’index et le travail meilleures lorsqu’il est utilisé pour suggérer des documents spécifiques au lieu des termes libres ou des expressions. Les champs candidats meilleures sont des titres, les noms et autres expressions relativement courtes qui peuvent identifier un élément. Moins efficaces sont des champs répétitives, telles que les catégories et indicateurs, ou très longues telles que les champs descriptions ou des commentaires.

Dans le cadre de la définition d’index, vous pouvez ajouter un suggester unique pour le `suggesters` collection de sites. Propriétés qui définissent un suggester sont les suivants :

- `name`: Nom de la suggester. Vous utilisez le nom de la suggester lorsque vous appelez le `suggest` API.
- `searchMode`: La stratégie permet de rechercher des expressions candidats. Le mode uniquement pris en charge actuellement est `analyzingInfixMatching`, qui exécute spéciaux flexibles de phrases au début ou au milieu de phrases.
- `sourceFields`: Une liste d’un ou plusieurs champs qui sont à l’origine du contenu pour obtenir des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` sources pour obtenir des suggestions peuvent être. Uniquement les champs qui n’ont pas un analyseur de langage personnalisé définir peuvent être utilisés.

**Exemple de suggester**

Un suggester fait partie de l’index. Suggester qu’un seul peut exister dans la `suggesters` collection de sites dans la version actuelle, à côté de la collection de champs et `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Si vous avez utilisé la version de présentation publique de recherche Azure, `suggesters` remplace une ancienne propriété booléenne (`"suggestions": false`) qui pris en charge uniquement préfixe suggestions pour les chaînes courtes (3-25 caractères). Son remplacement, `suggesters`, prend en charge infixe qui recherche les termes correspondants au début ou au milieu de contenu du champ, avec une meilleure tolérance les erreurs dans les chaînes de recherche correspondant. Commençant par la version disponible en règle générale, il s’agit maintenant la seule implémentation des suggestions API. L’ancien `suggestions` propriété qui a été introduite dans `api-version=2014-07-31-Preview` continue à fonctionner dans cette version, mais ne fonctionne pas dans les `2015-02-28` ou versions ultérieures de recherche Azure.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Mettre à jour d’Index

Vous pouvez mettre à jour un index existant dans recherche Azure à l’aide d’une demande HTTP placer. Ajout de nouveaux champs au schéma existant, la modification des options CORS et modification des profils score peuvent inclure des mises à jour. Pour plus d’informations, voir [Ajouter des scores profils](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Vous indiquez le nom de l’index à mettre à jour sur l’URI de requête :

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importantes :** Prise en charge des mises à jour de schéma index est limitée aux opérations qui ne nécessitent pas reconstruction de l’index de recherche. Les mises à jour de schéma qui nécessitent la réindexation, telles que la modification des types de champs, ne sont actuellement pas pris en charge. Nouveaux champs peuvent être ajoutés à tout moment, bien que des champs existants ne peuvent pas être modifiées ou supprimées. Ces conditions s’appliquent à `suggesters`. Nouveaux champs peuvent être ajoutés à une suggester au moment où les champs sont ajoutés, mais les champs ne peuvent pas être supprimés de `suggesters` et des champs existants ne peut pas être ajoutées à `suggesters`.

Lorsque vous ajoutez un nouveau champ à un index, tous les documents existants dans l’index a automatiquement une valeur null pour ce champ. Aucun espace de stockage supplémentaire n’est consommée jusqu'à ce que les nouveaux documents sont ajoutés à l’index.

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Mise à jour Index** est construite à l’aide de HTTP placer. À la place, le nom de l’index fait partie de l’URL. Si l’index n’existe pas, il est créé. Si l’index existe déjà, il est mis à jour à la nouvelle définition.

Le nom de l’index doit être en minuscule, commencer par une lettre ou un chiffre, comporter aucun barres obliques ou points et être dépasser 128 caractères. Après avoir démarré le nom de l’index avec une lettre ou un chiffre, le reste du nom peut inclure une lettre, le numéro et tirets, dans la mesure où les tirets ne sont pas consécutifs.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. La `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande de **Mise à jour Index** doit comporter une `api-key` en-tête défini sur votre clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Syntaxe de la demande de corps**

Lors de la mise à jour un index existant, le corps doit inclure la définition de schéma d’origine, ainsi que les nouveaux champs que vous ajoutez, ainsi que les profils score modifiées, suggesters et options CORS, le cas échéant. Si vous ne modifiez pas les profils score et les options CORS, vous devez inclure les originaux à partir de la création de l’index. En général le meilleur modèle à utiliser les mises à jour est de récupérer la définition d’index avec une commande GET, modifiez-la, puis mettre à jour avec place.

La syntaxe de schéma permet de créer un index est reproduite ici pour faciliter la tâche. Pour plus d’informations, voir [Créer un Index](#CreateIndex) .

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
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
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Réponse**

Pour une demande réussie : « 204 sans contenu ».

Par défaut, le corps de réponse sera vide. Toutefois, si la `Prefer` en-tête de la demande est défini sur `return=representation`, le corps de réponse contiendra la JSON pour la définition d’index qui a été mis à jour. Dans ce cas, le code d’état réussite sera « 200 OK ».

**Mise à jour de définition de l’index avec analyseurs personnalisés**

Une fois un analyseur, un générateur de jetons, un filtre jeton ou un filtre car est défini, elle ne peut pas être modifiée. Nouveaux peut être ajoutés à un index existant uniquement si la `allowIndexDowntime` indicateur est défini sur true dans la demande de mise à jour d’index : 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Notez que cette opération mettra votre index en mode hors connexion pour au moins quelques secondes, à l’origine de votre indexation et requêtes échec. Performances et écriture la disponibilité de l’index peut être personnes malvoyantes pendant quelques minutes après la mise à jour de l’index ou plus de l’index de très grande taille.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Liste à partir d’index

L’opération de **Liste à partir d’index** renvoie une liste des index actuellement dans votre service de recherche Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Liste à partir d’index** peut être créée à l’aide de la méthode GET.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Voir [Service de recherche du contrôle de version](http://msdn.microsoft.com/library/azure/dn864560.aspx) pour les détails et les autres versions.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Obligatoire. La `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande de **Liste à partir d’index** doit comporter une `api-key` défini sur une clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

Voici un corps de réponse exemple :

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Notez que vous pouvez filtrer la réponse vers le bas jusqu'à uniquement les propriétés que qui vous intéresse. Par exemple, si vous voulez seulement une liste de noms d’index, utilisez la OData `$select` option de requête :

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Dans ce cas, la réponse de l’exemple ci-dessus apparaît comme suit :

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Il s’agit d’une technique utile pour enregistrer la bande passante si vous avez un grand nombre d’index dans votre service de recherche.

<a name="GetIndex"></a>
## <a name="get-index"></a>Obtenir des Index

L’opération **d’Indexation obtenir** Obtient la définition d’index de recherche Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour les demandes de service. La demande **d’Obtenir un Index** peut être créée à l’aide de la méthode GET.

[Nom index] dans la demande URI Spécifie l’index pour retourner à partir de la collection d’index.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande **d’Obtenir un Index** doit comporter une `api-key` défini sur une clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

Consultez l’exemple JSON dans [Création et la mise à jour un Index](#CreateUpdateIndexExample) pour obtenir un exemple de la charge utile de réponse.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Supprimer l’Index

L’opération **d’Indexation supprimer** supprime un index et des documents associés à partir de votre service de recherche Azure. Vous pouvez obtenir le nom de l’index dans le tableau de bord de service dans le portail Azure ou à partir de l’API. Pour plus d’informations, voir [Index de liste](#ListIndexes) .

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Supprimer l’Index** peut être créée à l’aide de la méthode DELETE.

[Nom index] dans la demande URI Spécifie l’index à supprimer de la collection d’index.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Obligatoire. La `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à l’URL de votre service. La demande de **Supprimer l’Index** doit comporter une `api-key` en-tête défini sur votre clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 204 aucun contenu est renvoyé pour une réponse réussie.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Obtenir les statistiques d’Index

L’opération **d’Obtenir des statistiques d’Index** retourne le nombre total document pour l’index en cours, ainsi que l’utilisation du stockage de la recherche Azure.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Statistiques sur la taille de stockage et le nombre de document sont collectées plusieurs minutes, pas en temps réel. Par conséquent, les statistiques renvoyées par cette API ne peuvent pas refléter modifications causés par les opérations d’indexation récentes.

**Demande**

HTTPS est requis pour toutes les demandes de services. La demande **d’Obtenir des statistiques d’Index** peut être créée à l’aide de la méthode GET.

[Nom index] dans la demande URI indique au service pour renvoyer des statistiques d’index pour l’index spécifié.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande **d’Obtenir des statistiques d’Index** doit inclure un `api-key` défini sur une clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

Le corps de réponse est au format suivant :

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Analyseur de test

L' **API analyser** montre comment un analyseur décompose le texte en jetons.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de services. La demande **d’API analyser** peut être créée à l’aide de la méthode POST.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .


**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande de **L’API analyser** doit comporter une `api-key` défini sur une clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom de l’index et le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

ou

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

La `analyzer_name`, `tokenizer_name`, `token_filter_name` et `char_filter_name` doivent être noms valides des analyseurs prédéfinis ou personnalisés, générateurs, filtres jetons et filtres car pour l’index. Pour en savoir plus sur le processus d’analyse lexicale voir [analyse dans Azure recherche](https://aka.ms/azsanalysis).

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

Le corps de réponse est au format suivant :

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analyser exemple API**

**Demande**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Réponse**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Opérations de document

Dans Rechercher Azure, un index est stocké dans le cloud et remplie à l’aide de documents JSON que vous téléchargez au service. Tous les documents que vous téléchargez constituent le corpus de vos données de recherche. Documents contiennent des champs, certaines d'entre elles sont sous forme de jetons en termes de recherche comme elles sont téléchargées. La `/docs` segment d’URL dans l’API de recherche Azure représente la collection de documents dans un index. Toutes les opérations effectuées sur la collection telle que le chargement, fusion, supprimer ou interroger des documents prendre placer dans le contexte d’un index unique, donc les URL pour ces opérations commence toujours par `/indexes/[index name]/docs` un nom d’index donné.

Votre code de l’application doit générer documents JSON pour télécharger dans Azure recherche ou vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/dn946891.aspx) pour charger des documents si la source de données est la base de données SQL Azure ou DocumentDB. En règle générale, à partir d’index est remplis à partir d’un seul dataset que vous spécifiez.

Vous devez planifier sur la présence d’un document pour chaque élément que vous souhaitez rechercher. Une application location film peut-être avoir un document par film, une application Boutique peut-être avoir un document par référence (SKU), une application de supports de cours en ligne peut-être avoir un document par cours, une société de recherche peut avoir un document pour chaque document pour l’éducation dans leur référentiel et ainsi de suite.

Documents consistent par un ou plusieurs champs. Champs peuvent contenir du texte qui est sous forme de jetons par la recherche Azure en termes de recherche, ainsi que non-sous forme de jetons ou les valeurs non textuelles pouvant être utilisées dans les filtres ou profils score. Les noms, les types de données et les fonctionnalités de recherche prises en charge pour chaque champ sont déterminées par le schéma d’index. Un des champs dans chaque schéma d’index doit être désigné comme un ID et chaque document doit comporter une valeur pour le champ ID qui identifie de ce document dans l’index. Tous les autres champs de document sont facultatifs et par défaut pour une valeur null si gauche n’est pas spécifié. Notez que les valeurs null ne prennent pas d’espace dans l’index de recherche.

Avant que vous pouvez télécharger des documents, vous devez avoir déjà créé l’index sur le service. Pour plus d’informations sur cette première étape, consultez [Créer un Index](#CreateIndex) .

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Ajouter, mettre à jour, ou supprimer des Documents

Vous pouvez télécharger, fusionner, fusion ou téléchargement ou supprimer des documents à partir d’un index spécifié à l’aide de HTTP POST. Pour un grand nombre de mises à jour, le traitement par lots de documents un (documents 1000 par lot) ou environ 16 Mo par lot est recommandé.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Demande**

HTTPS est requis pour toutes les demandes de service. Vous pouvez télécharger, fusionner, fusion ou téléchargement ou supprimer des documents à partir d’un index spécifié à l’aide de HTTP POST.

La demande URI comprend [nom de l’index], spécifiez l’index pour publier des documents. Vous pouvez uniquement publier des documents à un index à la fois.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. La `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande **d’Ajouter des Documents** doit inclure un `api-key` en-tête défini sur votre clé d’administration (plutôt que d’utiliser une clé de requête).

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Le corps de la requête contient un ou plusieurs documents à indexer. Documents sont identifiés par une clé unique. Chaque document est associé à une action : téléchargement, fusionner, mergeOrUpload ou supprimer. Demandes de téléchargement doivent inclure les données du document sous forme d’un jeu de paires clé/valeur.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Touches de document ne peuvent contenir que des lettres, des nombres, des tirets («- »), des traits de soulignement (« _ ») et le signe égal (« = »). Pour plus d’informations, voir [règles d’affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Actions de document**

- `upload`: Une action de téléchargement est similaire à un « upsert » dans laquelle le document est inséré s’il s’agit de nouvelles et mises à jour/remplacé s’il existe. Notez que tous les champs sont remplacés dans le cas de mise à jour.
- `merge`: Fusion et publipostage met à jour un document existant avec les champs spécifiés. Si le document n’existe pas, la fusion échoue. N’importe quel champ que vous spécifiez dans une fusion remplacera le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ « balises » avec valeur `["budget"]` et que vous exécutez une fusion avec valeur `["economy", "pool"]` pour « balises », la valeur du champ « balises » finale sera `["economy", "pool"]`. Il sera **pas** être `["budget", "economy", "pool"]`.
- `mergeOrUpload`: se comporte comme `merge` s’il existe déjà un document avec la touche donnée dans l’index. Si le document n’existe pas il se comporte comme `upload` avec un nouveau document.
- `delete`: Supprimer supprime le document spécifié à partir de l’index. Notez que tous les champs que vous spécifiez dans un `delete` opération autre que le champ de clé sont ignorée. Si vous voulez supprimer un champ individuel à partir d’un document, utilisez `merge` à la place et simplement définir le champ explicitement à `null`.

**Réponse**

Code d’état 200 (OK) est renvoyée pour une réponse réussie, ce qui signifie que tous les éléments ont été indexés. Ceci est indiqué par le `status` propriété qui est défini sur true pour tous les éléments, ainsi que la `statusCode` à la propriété 201 (pour les documents que vous venez de télécharger) ou 200 (pour les documents fusionnés ou supprimés) :

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Code d’état 207 (à plusieurs état) est renvoyé lorsqu’au moins un élément n’a pas été indexé. Éléments qui n’ont pas été indexés ont la `status` champ la valeur false. La `errorMessage` et `statusCode` propriétés indique la raison pour laquelle l’erreur d’indexation :

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

Le tableau suivant répertorie les codes d’état par document différents qui peuvent être retournés dans la réponse. Notez que certaines indiquent les problèmes avec la requête elle-même, tandis que d’autres indiquent les conditions d’erreur temporaire. Vous devriez réessayer après un délai de ce dernier.

<table style="font-size:12">
    <tr>
        <th>Code d’état</th>
        <th>Signification</th>
        <th>Renouvelable</th>
        <th>Notes</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Document a été correctement modifié ou supprimé.</td>
        <td>n/a</td>
        <td>Opérations de suppression sont <a href="https://en.wikipedia.org/wiki/Idempotence">idempotents</a>. Autrement dit, même si une clé de document n’existe pas dans l’index, essayez d’une opération de suppression avec cette clé entraîne un code de 200 état.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Document a été créé.</td>
        <td>n/a</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Une erreur s’est produite dans le document qui a empêché son en cours d’indexation.</td>
        <td>N°</td>
        <td>Le message d’erreur dans la réponse indique quel est le problème avec le document.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Le document n’a pas pu être fusionné, car la clé donnée n’existe pas dans l’index.</td>
        <td>N°</td>
        <td>Cette erreur ne se produit pas pour les téléchargements dans la mesure où créer de nouveaux documents, et il ne se produit pas pour les suppressions, car ils sont <a href="https://en.wikipedia.org/wiki/Idempotence">idempotents</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Un conflit de version a été détecté lorsque vous tentez d’un document d’index.</td>
        <td>Oui</td>
        <td>Cela peut se produire lorsque vous essayez d’indexer le même document plusieurs fois simultanément.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>L’index est temporairement indisponible, car il a été mis à jour avec l’indicateur 'allowIndexDowntime' défini sur « true ».</td>
        <td>Oui</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Votre service de recherche est temporairement indisponible, éventuellement en raison de charge élevée.</td>
        <td>Oui</td>
        <td>Votre code doit attendre avant de réessayer dans ce cas, ou vous risquez de prolonger l’indisponibilité de service.</td>
    </tr>
</table> 

**Remarque**: Si votre code client rencontre fréquemment une réponse 207, il se peut que le système est en charge. Vous pouvez le confirmer en vérifiant la `statusCode` propriété pour 503. Si c’est le cas, nous vous recommandons de ***la limitation des demandes d’indexation***. Dans le cas contraire, si l’indexation le trafic ne perdure, le système peut démarrer en refusant toutes les requêtes avec 503 erreurs.

Code d’état 429 indique que vous avez dépassé votre quota du nombre de documents par index. Vous devez créer un nouvel index ou mettre à niveau les limites de capacité supérieure.

**Exemple :**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Rechercher des Documents

Une opération de **recherche** est émise comme une requête GET ou POST et spécifie les paramètres qui donnent des critères de sélection des documents correspondants.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quand utiliser billet au lieu de GET**

Lorsque vous utilisez HTTP GET pour appeler l’API de **recherche** , vous devez tenir compte que la longueur de l’URL de requête ne peut pas dépasser 8 Ko. Il s’agit généralement suffisant pour la plupart des applications. Toutefois, certaines applications produisent des requêtes très grands ou des expressions de filtre OData. Pour ces applications, à l’aide de HTTP POST est un meilleur choix, car elle permet la plus grande filtres et requêtes à obtenir. Avec billet, le nombre de termes ou des clauses dans une requête est le facteur de limitation, pas la taille de la requête brute étant donné que la limite de taille de demande pour valider est d’environ 16 Mo.

> [AZURE.NOTE] Même si la limite de taille de demande de billet est très volumineux, les expressions de filtre et les requêtes de recherche ne peut pas être arbitraire complexes. Pour plus d’informations sur les limitations de la complexité de requêtes et de filtre de recherche, voir [Lucene une syntaxe de requête](https://msdn.microsoft.com/library/mt589323.aspx) et [la syntaxe des expressions OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Demande**

HTTPS est requis pour les demandes de service. La demande de **recherche** peut être créée à l’aide des méthodes GET ou POST.

L’URI de requête spécifie l’index Query, pour tous les documents qui correspondent aux paramètres. Les paramètres sont spécifiés dans la chaîne de requête dans le cas des demandes GET, et dans la demande de corps dans le cas de billet demande.

Meilleurs résultats lors de la création de requêtes GET, n’oubliez pas de paramètres de la requête spécifique [encodage URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) lors de l’appel de l’API REST directement. Pour les opérations de **recherche** , cela inclut :

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

Codage de l’URL est recommandé uniquement dans les paramètres de requête ci-dessus. Si vous encodage URL par inadvertance la chaîne de requête entière (tout ce qui suit le ?), demandes de sauts de page.

En outre, codage de l’URL est nécessaire uniquement lorsque vous appelez l’API REST directement à l’aide de GET. Aucun codage URL n’est nécessaire lors de l’appel de **recherche** à l’aide de billet ou lors de l’utilisation de la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), qui gère le codage de l’URL pour vous.

<a name="SearchQueryParameters"></a>
**Paramètres de la requête**

**Recherche** accepte plusieurs paramètres qui fournissent des critères de requête et également spécifier le comportement de recherche. Vous fournir que ces paramètres dans l’URL de la chaîne de requête lors de l’appel **recherche** via GET et en tant que propriétés JSON dans le corps de requête lors de l’appel de **recherche** via le billet. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées selon le cas ci-dessous :

`search=[string]`(facultatif) - le texte à rechercher. Tous les `searchable` champs de recherche par défaut, sauf si `searchFields` est spécifié. Lors de la recherche `searchable` jetons de champs, le texte de recherche, afin de plusieurs termes peuvent être séparés par des espaces blancs (par exemple : `search=hello world`). Pour faire correspondre les termes, utilisez `*` (Cela peut être utile pour les requêtes de filtre booléen). L’omission de ce paramètre est le même effet que la valeur `*`. Voir [Syntaxe de requête Simple](https://msdn.microsoft.com/library/dn798920.aspx) pour les caractéristiques de l’objet sur la syntaxe de la recherche.

  - **Remarque**: les résultats peuvent parfois être étonnants lorsque vous interrogez sur `searchable` champs. Le Générateur de jetons inclut une logique pour gérer les cas communes à texte anglais tels que les apostrophes, les virgules dans les nombres, etc.. Par exemple, `search=123,456` correspond à un seul terme 123,456 plutôt que les termes individuels 123 et 456, étant donné que des virgules sont utilisés comme séparateurs de milliers pour un grand nombre en anglais. Pour cette raison, nous recommandons d’utiliser des espaces blancs au lieu des signes de ponctuation pour séparer les termes dans le `search` paramètre.

`searchMode=any|all`(facultatif, par défaut est `any`) - si tout ou partie des termes recherchés doit correspondre afin de compter le document comme une correspondance.

`searchFields=[string]`(facultatif) - la liste des noms de champs séparés par des virgules pour rechercher le texte spécifié. Champs cible doivent être marqués comme `searchable`.

`queryType=simple|full`(facultatif, par défaut est `simple`) - lorsqu’il est défini sur texte de recherche « simple » est interprété à l’aide d’un langage de requête simple qui autorise des symboles tels que +, * et « ». Les requêtes sont évaluées dans tous les champs que vous pourrez recherches (ou champs indiqués dans `searchFields`) dans chaque document par défaut. Lorsque le type de requête est défini sur `full` recherche le texte est interprété à l’aide du langage de requête Lucene qui permet d’effectuer des recherches et spécifiques au champ et pondérées. Voir [Simple syntaxe de requête](https://msdn.microsoft.com/library/dn798920.aspx) et la [Syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) pour caractéristiques sur la syntaxe de la recherche. 
 
> [AZURE.NOTE] Plage de recherche dans le Lucene langage de requête n’est pas prise en charge et privilégie $filter qui offre une fonctionnalité similaire.

`moreLikeThis=[key]`(facultatif) **Importantes :** Cette fonctionnalité est disponible uniquement dans `2015-02-28-Preview`. Cette option ne peuvent pas être utilisée dans une requête qui contient le paramètre de recherche de texte, `search=[string]`. La `moreLikeThis` paramètre trouve tous les documents qui sont semblables au document spécifié par la clé de document. Lorsqu’une demande de recherche est effectuée avec `moreLikeThis`, une liste des termes de recherche est générée en fonction de la fréquence et rareté de termes dans le document source. Ces termes sont ensuite utilisés pour effectuer la demande. Par défaut, le contenu de toutes `searchable` champs sont considérés comme, sauf si `searchFields` est utilisé pour restreindre des champs de recherche.  

`$skip=#`(facultatif) - le nombre de résultats de recherche permet d’ignorer ; Ne peut pas être supérieur à 100 000. Si vous avez besoin pour numériser des documents dans l’ordre mais que vous ne pouvez pas utiliser `$skip` en raison de cette limitation, envisagez d’utiliser `$orderby` sur une clé totalement commandé et `$filter` avec une plage de requête à la place.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `skip` au lieu de `$skip`.

`$top=#`(facultatif) - le nombre de résultats de recherche pour récupérer. Cela peut être utilisé conjointement avec `$skip` pour implémenter la pagination côté client des résultats de recherche.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `top` au lieu de `$top`.

`$count=true|false`(facultatif, par défaut est `false`)-indique s’il faut extraire le nombre total de résultats. Ceci est le nombre de tous les documents qui correspondent à la `search` et `$filter` paramètres, en ignorant les `$top` et `$skip`. Définir cette valeur sur `true` peut avoir un impact sur les performances. Notez que le compte retourné est une approximation.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `count` au lieu de `$count`.

`$orderby=[string]`(facultatif) - une liste d’expressions séparées par des virgules pour trier les résultats par. Chaque expression peut être un nom de champ ou un appel à la `geo.distance()` fonction. Chaque expression peut être suivie `asc` indiquées par ordre croissant, et `desc` pour indiquer l’ordre décroissant. La valeur par défaut est l’ordre croissant. TIES seront subdivisées par les scores de correspondance des documents. Si aucune `$orderby` est spécifié, l’ordre de tri par défaut est décroissant par score de correspondance de document. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `orderby` au lieu de `$orderby`.

`$select=[string]`(facultatif) - une liste des champs séparés par des virgules à récupérer. Si rien n’est spécifié, tous les champs marqués comme récupérables dans le schéma sont incluses. Vous pouvez également explicitement demander tous les champs en définissant ce paramètre à `*`.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `select` au lieu de `$select`.

`facet=[string]`(zéro ou plus) - facette par un champ. Vous pouvez également la chaîne peut-être contenir des paramètres pour personnaliser la faceting exprimé sous forme de séparées par des virgules `name:value` paires. Paramètres valides sont :

- `count`(max nombre de termes facette ; valeur par défaut est 10). Absence de maximum, mais des valeurs élevées subir une baisse des performances correspondante, surtout si le champ par facettes contient un grand nombre de termes uniques.
  - Par exemple : `facet=category,count:5` Obtient les catégories de cinq premiers dans les résultats de facette.  
  - **Remarque**: si le `count` paramètre est inférieur au nombre de termes uniques, les résultats ne peuvent pas être correctes. Il s’agit en raison de la façon dont les requêtes faceting sont réparties sur milieu des fragments. Augmentation `count` généralement améliore la précision des nombres à terme, mais en une baisse des performances.
- `sort`(une des `count` tri *décroissant* en fonction du nombre, `-count` pour effectuer un tri *par ordre croissant* en fonction du nombre, `value` pour effectuer un tri *par ordre croissant* par valeur, ou `-value` pour trier par valeur, *décroissant* )
  - Par exemple : `facet=category,count:3,sort:count` Obtient les catégories de trois principaux dans les résultats de facette dans l’ordre décroissant par le nombre de documents avec chaque nom de la ville. Par exemple, si les trois premières catégories sont Budget, Motel et haut de gamme et Budget ayant 5 correspondances, Motel 6 ou haut de gamme a 4, puis les paquets seront dans l’ordre Motel, Budget, haut de gamme.
  - Par exemple : `facet=rating,sort:-value` génère des plages pour tous les évaluations possibles, dans l’ordre décroissant par valeur. Par exemple, si les évaluations sont comprises entre 1 et 5, les paquets seront classées 5, 4, 3, 2, 1, quel que soit le nombre de documents correspondent à chaque évaluation.
- `values`(barre verticale par des virgules numérique ou `Edm.DateTimeOffset` valeurs spécifiant un jeu de valeurs d’entrée facette dynamique)
  - Par exemple : `facet=baseRate,values:10|20` génère trois plages : une pour le taux de base 0 jusqu’au mais sans inclure jusqu'à 10, l’autre pour 10, mais ne pas y compris 20 et l’autre pour 20 ou une version ultérieure.
  - Par exemple : `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` génère deux plages : une pour l’hébergement rénové avant février 2010 et l’autre pour l’hébergement rénovéent février 1er, 2010 ou version ultérieure.
- `interval`(intervalle d’entier supérieur à 0 pour les nombres, ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` pour les valeurs de temps date)
  - Par exemple : `facet=baseRate,interval:100` génère des plages en fonction des plages de taux de base de taille 100. Par exemple, si le taux de base sont toutes comprises entre 60 $ et $600, il sera plages pour 0-100, 100 et 200, 200-300, 300-400, 400-500 et 500 et 600.
  - Par exemple : `facet=lastRenovationDate,interval:year` génère une peinture pour chaque année lorsque hôtel ont été rénové.
- `timeoffset`([+-] HH : mm, [+-] hh, ou [+-] hh) `timeoffset` est facultative. Il peut uniquement être associé à la `interval` option et lorsqu’elles sont appliquées à un champ de type `Edm.DateTimeOffset`. La valeur spécifie le décalage UTC au compte pour lors de la configuration des limites de durée.
  - Par exemple : `facet=lastRenovationDate,interval:day,timeoffset:-01:00` utilise la limite de jours qui commence à 01:00:00 UTC (minuit dans le fuseau horaire cible)
- **Remarque**: `count` et `sort` peuvent être combinées dans la même spécification facette, mais ils ne peuvent pas être combinés avec `interval` ou `values`, et `interval` et `values` ne peuvent pas être combinées.
- **Remarque**: facettes intervalle sur date heure sont calculées en fonction de date au format UTC si `timeoffset` n’est pas spécifié. Par exemple : pour `facet=lastRenovationDate,interval:day`, le jour limite commence à 00:00:00 UTC. 

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `facets` au lieu de `facet`. En outre, vous définissez-le comme étant une matrice JSON de chaînes où chaque chaîne est une expression facette distincte.

`$filter=[string]`(facultatif) - une expression de recherche structurées dans la syntaxe OData standard. Pour plus d’informations sur le sous-ensemble de la grammaire OData expression qui prend en charge de la recherche Azure, consultez [La syntaxe des expressions OData](#ODataExpressionSyntax) .

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `filter` au lieu de `$filter`.

`highlight=[string]`(facultatif) - un ensemble de noms de champs séparés par des virgules utilisés pour test met en surbrillance. Uniquement `searchable` champs peuvent être utilisés pour la mise en surbrillance.

`highlightPreTag=[string]`(facultatif, par défaut est `<em>`) - une balise qui ajoute pour atteindre extraits de chaîne. Doit être définie avec `highlightPostTag`.

> [AZURE.NOTE] Lorsque appel de **recherche** à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, 23 % au lieu de #).

`highlightPostTag=[string]`(facultatif, par défaut est `</em>`)-un indicateur de chaîne qui ajoute pour atteindre extraits. Doit être définie avec `highlightPreTag`.

> [AZURE.NOTE] Lorsque appel de **recherche** à l’aide de GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, 23 % au lieu de #).

`scoringProfile=[string]`(facultatif) - le nom d’un profil score doit être évaluée correspondre scores pour la correspondance des documents afin de trier les résultats.

`scoringParameter=[string]`(zéro ou plus) - indique les valeurs pour chaque paramètre défini dans une fonction de notation (par exemple, `referencePointParameter`) en utilisant le format `name-value1,value2,...`.

- Par exemple, si le profil score définit une fonction avec un paramètre appelé « mylocation » l’option de chaîne de requête est `&scoringParameter=mylocation--122.2,44.8`. La première tiret sépare le nom de la liste de valeurs, tandis que le second tiret fait partie de la première valeur (longitude dans cet exemple).
- Pour les paramètres de la notation tels que balise pour accroître qui peut contenir des virgules, vous pouvez ignorer tout ces valeurs dans la liste à l’aide de guillemets simples. Si les valeurs elles-mêmes contiennent des guillemets simples, vous pouvez d’échappement en double.
  - Par exemple, si vous avez une balise augmentation de paramètre appelé « mytag » et que vous voulez accroître la balise les valeurs « Hello, o ' Brien » et « Toile », la requête option chaîne serait `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Notez que les devis sont uniquement requis pour les valeurs qui contiennent des virgules.

> [AZURE.NOTE] Lorsque vous appelez **recherche** à l’aide de billet, ce paramètre est appelé `scoringParameters` au lieu de `scoringParameter`. En outre, vous spécifiez sous forme de tableau JSON de chaînes où chaque chaîne est un distinct `name-values` paire.

`minimumCoverage`(facultatif, les valeurs par défaut à 100)-un nombre compris entre 0 et 100 qui indique le pourcentage de l’index doit être couverte par une requête de recherche dans l’ordre de la requête à être signalé comme étant réussi. Par défaut, la totalité de l’index doit être disponible ou `Search` renverra code d’état HTTP 503. Si vous définissez `minimumCoverage` et `Search` a réussi, il renverra HTTP 200 et inclure un `@search.coverage` valeur dans la réponse qui indique le pourcentage de l’index qui a été inclus dans la requête.

> [AZURE.NOTE] Peut être utile pour garantir la disponibilité de recherche même pour les services avec une copie de ce paramètre à une valeur inférieure à 100. Toutefois, pas tous les documents correspondants sont garanties soient présents dans les résultats de recherche. Si le rappel recherche est plus important à votre application que disponibilité, puis il est préférable de laisser `minimumCoverage` à sa valeur par défaut de 100.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour que cette opération, la `api-version` est spécifié comme un paramètre de requête dans l’URL indépendamment si vous appelez **recherche** POST ou GET.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à l’URL de votre service. La demande de **recherche** peut spécifier une clé d’administration ou une clé de requête pour `api-key`.

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Pour obtenir : aucune.

Pour valider :

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuation de réponses de recherche partielle**

Parfois Azure recherche ne peut pas retourner tous les résultats demandées dans une seule réponse de recherche. Cela peut se produire pour différentes raisons, par exemple quand la requête demande trop de documents en spécifiant ne pas `$top` ou en spécifiant une valeur pour `$top` qui est trop volumineux. Dans ce cas, recherche Azure doit inclure le `@odata.nextLink` annotation dans le corps de réponse et également `@search.nextPageParameters` s’il s’agissait d’une requête POST. Vous pouvez utiliser les valeurs de ces annotations pour formuler une autre requête de recherche pour obtenir de l’étape suivante de la réponse de la recherche. Il s’agit d’une ***suite*** de la demande de recherche d’origine et les annotations sont généralement appelées ***jetons continuation***. Voir [l’exemple ci-dessous](#SearchResponse) pour plus d’informations sur la syntaxe de ces annotations et où ils apparaissent dans le corps de réponse. 

Les raisons pourquoi Azure recherche peut retourner des jetons de continuation sont spécifiques à l’implémentation et peuvent être modifiées. Clients robustes doivent toujours être prêts à gérer les cas où les documents moins que prévu sont retournés et un jeton de continuation est inclus pour continuer l’extraction de documents. Notez également que vous devez utiliser la même méthode HTTP en tant que la requête d’origine afin de pouvoir pour continuer. Par exemple, si vous envoyez une demande GET, toutes les demandes de continuation que vous envoyez doivent également utiliser GET (et de la même manière pour valider).

<a name="SearchResponse"></a>
**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemples :**

Vous pouvez trouver des exemples supplémentaires dans la page de [La syntaxe des expressions OData pour la recherche Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)  Recherche l’Index trié par ordre décroissant par date.


    OBTENIR /indexes/hotels/docs ? recherche = * & $orderby = lastRenovationDate desc & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « * », « orderby » : « lastRenovationDate desc »}

2)  Dans une recherche par facettes, l’index de recherche et de récupérer facettes de catégories, évaluation, indicateurs, ainsi que les éléments avec baseRate dans des plages spécifiques :


    OBTENIR /indexes/hotels/docs ? recherche = test & facette = catégorie & facette = évaluation & facette = balises & facette = baseRate, valeurs : 80 | 150 | 220 et version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « tester », « facettes » : [« catégorie », « évaluation », « balises », « baseRate, valeurs : 80 | 150 | 220"]}

3)  À l’aide d’un filtre, affiner les résultats de requête par facettes précédent après que l’utilisateur clique sur évaluation de 3 et la catégorie « Motel » :


    OBTENIR /indexes/hotels/docs ? recherche = test & facette = balises & facette = baseRate, valeurs : 80 | 150 | 220 & $filter = évaluation é 3 et de catégorie eq 'Motel' & de version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « tester », « facettes » : [« balises », « baseRate, valeurs : 80 | 150 | 220"], « filtre » : « évaluation eq 3 et category eq « Motel » »}

4) Dans une recherche par facettes, définissez une limite supérieure unique termes renvoyés dans une requête. La valeur par défaut est 10, mais vous pouvez augmenter ou diminuer cette valeur à l’aide du `count` paramètre sur la `facet` attribut :


    OBTENIR /indexes/hotels/docs ? recherche = test & facette = ville, NB : 5 et version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « tester », « facettes » : [« ville, NB : 5 »]}

5)  Effectuer une recherche dans l’Index dans des champs spécifiques ; Par exemple, un champ spécifique à la langue :


    OBTENIR /indexes/hotels/docs ? recherche = hôtel & searchFields = description_fr & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « hôtel », « searchFields » : « description_fr »}

6) Rechercher l’Index dans plusieurs champs. Par exemple, vous pouvez stocker et champs dans plusieurs langues, tout dans le même index de recherche de la requête.  Si les descriptions anglais et Français coexister dans le même document, vous pouvez retourner tout ou partie dans les résultats de la requête :


    OBTENIR /indexes/hotels/docs ? recherche = hôtel & searchFields = description, description_fr & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « hôtel », « searchFields » : « description, description_fr »}

Notez que vous ne pouvez interroger qu’un seul index à la fois. Ne créez pas plusieurs index pour chaque langue, sauf si vous prévoyez d’interroger une à la fois.

7)  Pagination - obtenir la 1ère page d’éléments (taille de la page est de 10) :


    OBTENIR /indexes/hotels/docs ? recherche = * & $skip = 0 & $top = 10 & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « * », « ignorer » : 0, « supérieure » : 10}

8)  Pagination - obtenir la page 2ème d’éléments (taille de la page est de 10) :


    OBTENIR /indexes/hotels/docs ? recherche = * & $skip = 10 & $top = 10 & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « * », « ignorer » : 10, « supérieure » : 10}

9)  Récupérer un ensemble spécifique de champs :


    OBTENIR /indexes/hotels/docs ? recherche = * & $select = hotelName, description et version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « * », « select » : « hotelName, description »}

10)  Récupérer des documents correspondant à une expression de filtre spécifique :


    OBTENIR /indexes/hotels/docs ? $filter = (baseRate page 60 et baseRate lt 300) ou é de hotelName 'Rester fantaisie' & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« filtre » : « (baseRate page 60 et baseRate lt 300) ou hotelName eq « Restez fantaisie » «}

11) Recherche les fragments index ou de l’expéditeur avec extraits de positionnement


    OBTENIR /indexes/hotels/docs ? recherche = quelque chose et mettez en surbrillance = description & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « un », « mettre en surbrillance » : « description »}

12) Effectuer une recherche dans l’index et renvoyer des documents triées de la plus proche d’éloignée référence emplacement


    OBTENIR /indexes/hotels/docs ? recherche quelque chose = & $orderby=geo.distance (emplacement, geography'POINT(-122.12315 47.88121) ») & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « un », « orderby » : « geo.distance (emplacement, geography'POINT(-122.12315 47.88121) ») »}

13) Rechercher dans l’index en supposant qu’il est un score profil appelé « geo » avec deux fonctions score distance, une définition d’un paramètre appelé « currentLocation » et une définition d’un paramètre appelé « lastLocation »


    OBTENIR /indexes/hotels/docs ? recherche quelque chose = & scoringProfile = geo & scoringParameter = currentLocation--122.123,44.77233 & scoringParameter = lastLocation--121.499,44.2113 & version de l’api = 2015-02-28-aperçu

    /Indexes/hotels/docs/search billet ? version de l’api = 2015-02-28-aperçu {« search » : « un », « scoringProfile » : « geo », « scoringParameters » : [ » currentLocation--122.123,44.77233 », « lastLocation--121.499,44.2113 »]}

14) Rechercher des documents dans l’index à l’aide de la [syntaxe de la requête simple](https://msdn.microsoft.com/library/dn798920.aspx). Cette requête renvoie hôtel où les champs de recherche contiennent les termes « reprenez » et « emplacement », mais pas « motel » :


    OBTENIR /indexes/hotels/docs ? recherche = reprenez + emplacement-motel & searchMode = all & version de l’api = 2015-02-28-aperçu

    BILLET /indexes/hotels/docs/search ? version de l’api = 2015-02-28-aperçu {« search » : « reprenez + emplacement-motel », « searchMode » : « tout »}

Notez l’utilisation de `searchMode=all` ci-dessus. Notamment ce paramètre remplace le paramètre par défaut `searchMode=any`, ce qui garantit que `-motel` signifie « Et pas » au lieu de « OR NOT ». Sans `searchMode=all`, à « Ou pas » qui s’étend au lieu de restreint les résultats de recherche et il peut s’agir intuitif à certains utilisateurs.

15) Rechercher des documents dans l’index à l’aide de la [syntaxe de la requête lucene](https://msdn.microsoft.com/library/mt589323.aspx). Cette requête renvoie hôtel dont le champ catégorie contient le terme « budget » et de tous les champs de recherche contenant l’expression « récemment rénovée ». Documents contenant l’expression « récemment rénovée » sont classées par ordre croissant en raison de la valeur d’Ampli terme (3)

    OBTENIR /indexes/hotels/docs?search = catégorie : budget et \"récemment rénové\"^ 3 & searchMode = all & version de l’api = 2015-02-28-Aperçu & querytype = complet

    BILLET /indexes/hotels/docs/search?api-version = 2015-02-28-aperçu {« search » : « catégorie : budget et \"récemment rénové\"^ 3", « queryType » : « complète », « searchMode » : « tout »}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Document de recherche

L’opération de **Document recherche** récupère un document à partir d’Azure recherche. Ceci est utile lorsqu’un utilisateur clique sur un résultat de recherche spécifiques, et que vous voulez rechercher des détails spécifiques sur ce document.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Document recherche** peut être construite comme suit.

    GET /indexes/[index name]/docs/key?[query parameters]

Par ailleurs, vous pouvez utiliser la syntaxe OData traditionnelle pour la recherche de clé :

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

La demande URI comprend un [nom de l’index] et [clé], spécifiant le document à extraire à partir de l’index. Vous pouvez uniquement obtenir un seul document à la fois. Utiliser **la recherche** pour obtenir plusieurs documents dans une seule demande.

**Paramètres de la requête**

`$select=[string]`(facultatif) - une liste des champs séparés par des virgules à récupérer. Si rien n’est spécifié ou la valeur `*`, tous les champs marqués comme récupérables dans le schéma sont incluses dans la projection.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour que cette opération, la `api-version` est spécifié comme un paramètre de requête.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à l’URL de votre service. La demande de **Document recherche** pouvez spécifier une clé d’administration ou une clé de requête pour `api-key`.

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemple**

Recherche le document qui a clé « 2 »

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Recherche le document qui a clé « 3 » à l’aide d’OData syntaxe :

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Nombre de Documents

L’opération **Compter Documents** récupère le nombre de documents dans un index de recherche. La `$count` syntaxe fait partie du protocole OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Count Documents** peut être créée à l’aide de la méthode GET.

[Nom index] dans la demande URI indique au service pour renvoyer le nombre de tous les éléments dans la collection de documents de l’index spécifié.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs.

- `Accept`: Cette valeur doit être définie sur `text/plain`.
- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à l’URL de votre service. La demande de **Documents Nb** pouvez spécifier une clé d’administration ou une clé de requête pour `api-key`.

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Aucun.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

Le corps de réponse contient la valeur de nombre sous forme d’entier mis en forme en texte brut.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Suggestions

L’opération de **Suggestions** récupère suggestions selon l’entrée de recherche partielle. Il est généralement utilisé dans les zones de recherche pour fournir des suggestions semi-automatique que les utilisateurs entrent des termes de recherche.

Demandes de suggestion visent à suggérant des documents cible, afin que les suggestions de texte peut être répété si plusieurs documents candidate correspond à la même recherche d’entrée. Vous pouvez utiliser `$select` pour extraire les autres champs de document (y compris la clé de document) afin que vous sachiez quel document est la source pour chaque suggestion.

Une opération de **Suggestions** est publiée dans une requête GET ou POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quand utiliser billet au lieu de GET**

Lorsque vous utilisez HTTP GET pour appeler les **Suggestions de** l’API, vous devez tenir compte que la longueur de l’URL de requête ne peut pas dépasser 8 Ko. Il s’agit généralement suffisant pour la plupart des applications. Toutefois, certaines applications produisent des requêtes de très grandes, OData spécifiquement les expressions de filtre. Pour ces applications, à l’aide de HTTP POST est un meilleur choix, car elle permet des filtres plus grande que GET. Avec billet, le nombre de clauses d’un filtre est le facteur de limitation, pas la taille de la chaîne de filtrage brutes étant donné que la limite de taille de demande pour valider est d’environ 16 Mo.

> [AZURE.NOTE] Même si la limite de taille de demande de billet est très volumineux, les expressions de filtre ne peut pas être arbitraire complexes. Pour plus d’informations sur les limitations de la complexité de filtre, consultez [la syntaxe des expressions OData](https://msdn.microsoft.com/library/dn798921.aspx) .

**Demande**

HTTPS est requis pour les demandes de service. La demande de **Suggestions** de peut être créée à l’aide des méthodes GET ou POST.

L’URI de requête spécifie le nom de l’index à la requête. Paramètres, tels que les termes partiellement d’entrée, sont spécifiées dans la chaîne de requête dans le cas des demandes GET et dans la demande de demandes de corps dans le cas de billet.

Meilleurs résultats lors de la création de requêtes GET, n’oubliez pas de paramètres de la requête spécifique [encodage URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) lors de l’appel de l’API REST directement. Pour les opérations de **Suggestions** , cela inclut :

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

Codage de l’URL est recommandé uniquement dans les paramètres de requête ci-dessus. Si vous encodage URL par inadvertance la chaîne de requête entière (tout ce qui suit le ?), demandes de sauts de page.

En outre, codage de l’URL est nécessaire uniquement lorsque vous appelez l’API REST directement à l’aide de GET. Aucun codage URL n’est nécessaire lors de l’appel des **Suggestions** à l’aide de billet ou lors de l’utilisation de la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), qui gère le codage de l’URL pour vous.

**Paramètres de la requête**

**Suggestions** accepte plusieurs paramètres qui fournissent des critères de requête et également spécifier le comportement de recherche. Vous fournir que ces paramètres dans l’URL de la chaîne de requête lors de l’appel **Suggestions** via GET et, en tant que propriétés JSON dans le corps de requête lors de l’appel **Suggestions** via le billet. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées selon le cas ci-dessous :

`search=[string]`-le texte de recherche à utiliser pour suggérer des requêtes. Doit être au moins 1 caractère et pas plus de 100 caractères.

`highlightPreTag=[string]`(facultatif) - une chaîne de balise qui ajoute pour rechercher des correspondances. Doit être définie avec `highlightPostTag`.

> [AZURE.NOTE] Lorsque appelant **Suggestions** en utilisant GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, 23 % au lieu de #).

`highlightPostTag=[string]`(facultatif) - une chaîne de balise qui ajoute pour rechercher des correspondances. Doit être définie avec `highlightPreTag`.

> [AZURE.NOTE] Lorsque appelant **Suggestions** en utilisant GET, les caractères réservés dans l’URL doit être encodés en pourcentage (par exemple, 23 % au lieu de #).

`suggesterName=[string]`-le nom de la suggester comme indiqué dans le `suggesters` collection qui fait partie de la définition d’index. A `suggester` détermine les champs qui sont analysés pour les termes de requête suggéré. Pour plus d’informations, voir [Suggesters](#Suggesters) .

`fuzzy=[boolean]`(facultatif, par défaut = false)-lorsqu’elle est définie sur true cette API recherche suggestions même s’il existe un caractère de remplacement ou manquant dans le texte à rechercher. Alors que cela fournit une meilleure expérience dans certains scénarios elle intervient à un coût comme suggestion floue recherches ne sont plus lentes et consomment davantage de ressources de performances.

`searchFields=[string]`(facultatif) - la liste des noms de champs séparés par des virgules pour rechercher le texte de recherche spécifié. Champs cible doivent être activés pour obtenir des suggestions.

`$top=#`(facultatif, par défaut = 5)-le nombre de suggestions à récupérer. Doit être un nombre compris entre 1 et 100.

> [AZURE.NOTE] Lorsque vous appelez **Suggestions** à l’aide de billet, ce paramètre est appelé `top` au lieu de `$top`.

`$filter=[string]`(facultatif) - une expression qui permet de filtrer les documents considérés comme pour obtenir des suggestions.

> [AZURE.NOTE] Lorsque vous appelez **Suggestions** à l’aide de billet, ce paramètre est appelé `filter` au lieu de `$filter`.

`$orderby=[string]`(facultatif) - une liste d’expressions séparées par des virgules pour trier les résultats par. Chaque expression peut être un nom de champ ou un appel à la `geo.distance()` fonction. Chaque expression peut être suivie `asc` indiquées par ordre croissant, et `desc` pour indiquer l’ordre décroissant. La valeur par défaut est l’ordre croissant. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Lorsque vous appelez **Suggestions** à l’aide de billet, ce paramètre est appelé `orderby` au lieu de `$orderby`.

`$select=[string]`(facultatif) - une liste des champs séparés par des virgules à récupérer. Si rien n’est spécifié, seul le document clé et suggestion de texte est renvoyé. Vous pouvez demander explicitement tous les champs en définissant ce paramètre `*`.

> [AZURE.NOTE] Lorsque vous appelez **Suggestions** à l’aide de billet, ce paramètre est appelé `select` au lieu de `$select`.

`minimumCoverage`(facultatif, les valeurs par défaut à 80)-un nombre compris entre 0 et 100 qui indique le pourcentage de l’index doit être couverte par une requête suggestions afin que la requête à être signalé comme étant réussi. Par défaut, au moins 80 % de l’index doit être disponible ou `Suggest` renverra code d’état HTTP 503. Si vous définissez `minimumCoverage` et `Suggest` a réussi, il renverra HTTP 200 et inclure un `@search.coverage` valeur dans la réponse qui indique le pourcentage de l’index qui a été inclus dans la requête.

> [AZURE.NOTE] Peut être utile pour garantir la disponibilité de recherche même pour les services avec une copie de ce paramètre à une valeur inférieure à 100. Toutefois, pas toutes les suggestions correspondantes sont garanties soient présents dans les résultats. Si le rappel est plus important à votre application que disponibilité, puis il est préférable de ne pas abaisser `minimumCoverage` en dessous de sa valeur par défaut 80.

`api-version=[string]`(obligatoire). La version d’évaluation est `api-version=2015-02-28-Preview`. Pour plus d’informations et d’autres versions, consultez [Versioning de Service de recherche](http://msdn.microsoft.com/library/azure/dn864560.aspx) .

Remarque : pour que cette opération, la `api-version` est spécifié comme un paramètre de requête dans l’URL indépendamment si vous appelez **Suggestions** POST ou GET.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs

- `api-key`: Le `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à l’URL de votre service. La demande de **Suggestions** de pouvez spécifier une clé d’administration ou une clé de requête en tant que la `api-key`.

Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le portail Azure. Consultez [créer un service de recherche Azure dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

**Corps de la requête**

Pour obtenir : aucune.

Pour valider :

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Si l’option de projection est utilisée pour récupérer des champs que sont inclus dans chaque élément du tableau :

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemple**

Récupérer 5 suggestions où l’entrée de recherche partielle est « lux »

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
