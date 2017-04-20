<properties
    pageTitle="Comment modeler des types de données complexes dans Azure recherche | Recherche de Microsoft Azure"
    description="Imbriqués ou structures de données hiérarchiques peuvent être modélisation dans un index de recherche Azure à l’aide du jeu de lignes plane et type de données de collections de sites."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Comment faire pour des types de données complexes dans Azure recherche de modèle

Jeux de données externe utilisée pour renseigner un index de recherche Azure parfois inclure hiérarchiques ou imbriquées sous-structures pas répartir parfaitement en un ensemble de lignes tabulaire. Exemples de ces structures peuvent inclure plusieurs emplacements et les numéros de téléphone pour un seul client, plusieurs couleurs et les tailles pour une référence SKU unique, plusieurs auteurs d’un livre unique et ainsi de suite. En termes de modélisation, vous pouvez voir ces structures appelés *types de données complexes*, *composé de types de données*, *types de données composites*ou *agréger les types de données*, pour citer.

Types de données complexes ne sont pas pris en charge en mode natif dans recherche Azure, mais une solution de contournement éprouvée inclut une procédure de mise à plat de la structure, puis en utilisant un type de données de **collection de sites** pour reconstituer la structure intérieure. Selon la technique décrite dans cet article permet le contenu à rechercher, par facettes, filtrés et triés.

## <a name="example-of-a-complex-data-structure"></a>Exemple de structure de données complexes

En règle générale, les données en question se trouvent sous la forme d’un ensemble de documents JSON ou XML ou éléments dans un magasin NoSQL comme DocumentDB. Structurelle, le défi provient d’avoir plusieurs éléments enfants qui doivent être recherché et filtrées.  Comme point de départ pour illustrer la solution de contournement, copiez le document JSON suivant qui répertorie un ensemble de contacts par exemple :

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Bien que les champs nommé « id », « nom » et « société » peuvent facilement être chacun mappés en tant que champs dans un index de recherche Azure, le champ « lieux » contient un tableau des emplacements, ayant un jeu de codes d’emplacement, ainsi que des descriptions emplacement. Étant donné que Azure recherche n’a pas un type de données qui prend en charge cette, nous avons besoin d’une autre façon de présenter dans Azure recherche. 

> [AZURE.NOTE] Cette méthode est également décrite par Evans Kirk dans un billet de blog [DocumentDB l’indexation avec la recherche Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), qui affiche une technique appelée « mise à plat les données », selon laquelle que c’est un champ intitulé `locationsID` et `locationsDescription` qui sont des [collections de sites](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Partie 1 : Fusionner la matrice dans des champs individuels

Pour créer un index de recherche Azure qui prend en charge le jeu de données, créer des champs individuels pour l’infrastructure imbriquée : `locationsID` et `locationsDescription` avec un type de données de [collections de sites](https://msdn.microsoft.com/library/azure/dn798938.aspx) (ou un tableau de chaînes). Ces champs vous le feriez indexer les valeurs « 1 » et « 2 » dans la `locationsID` de champ pour John Smith et les valeurs « 3 » et « 4 » dans la `locationsID` champ Jen Campbell.  

Vos données dans Azure recherche présente comme suit : 

![exemples de données, 2 lignes](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Partie 2 : Ajouter un champ de la collection de sites dans la définition d’index

Dans le schéma d’index, les définitions de champ peuvent ressembler à cet exemple.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validez les comportements de recherche et vous pouvez également étendre l’index

En supposant que vous avez créé l’index et chargement des données, vous pouvez maintenant tester la solution pour vérifier l’exécution des requêtes de recherche sur le jeu de données. Le champ de chaque **collection de sites** doit être **disponible pour la recherche**, **filtrables** et **facetable**. Vous devriez pouvoir exécuter des requêtes tels que :

* Rechercher toutes les personnes qui travaillent au siège Adventureworks' '.
* Obtenir un nombre au nombre de personnes qui travaillent dans un bureau d’accueil.  
* Des personnes qui travaillent dans un bureau d’accueil, afficher les autres bureaux fonctionne-t-elle avec un nombre de personnes à chaque emplacement.  

Où cette technique tombe attention est lorsque vous avez besoin d’effectuer une recherche qui combine le code d’emplacement ainsi que la description de l’emplacement. Par exemple :

* Rechercher toutes les personnes lequel ils disposent d’un bureau à domicile et contient un code d’emplacement de 4.  

Si vous rappelez le contenu d’origine ressemble à ceci :

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Toutefois, à présent que nous avons séparés les données dans des champs distincts, nous ne pouvons pas if de connaître le bureau personnel pour Jen Campbell est lié à `locationsID 3` ou `locationsID 4`.  

Pour gérer le cas, définir un autre champ dans l’index qui combine toutes les données dans une collection unique.  Dans notre exemple, nous appelons ce champ `locationsCombined` et nous allons séparer le contenu avec un `||` bien que vous pouvez choisir n’importe quel séparateur que vous pensez que serait un ensemble unique de caractères pour votre contenu. Par exemple : 

![exemples de données, 2 lignes avec un séparateur](./media/search-howto-complex-data-types/sample-data-2.png)

L’utilisation de cette `locationsCombined` champ, nous pouvons désormais accueillir davantage de requêtes, telles que :

* Afficher un nombre de personnes qui travaillent dans « Bureau d’accueil » avec l’emplacement Id '4'.  
* Rechercher des personnes qui travaillent dans un bureau d’accueil avec l’emplacement Id '4'. 

## <a name="limitations"></a>Limitations

Cette technique est utile pour un certain nombre de scénarios, mais il n’est pas applicable dans tous les cas.  Par exemple :

1. Si vous n’avez pas un ensemble statique de champs dans votre type de données complexe et il n’a aucun moyen d’établir une correspondance entre tous les types possibles un seul champ. 
2. Mise à jour les objets imbriquées nécessite un travail supplémentaire pour déterminer exactement ce qui doit être mis à jour dans l’index de recherche Azure

## <a name="sample-code"></a>Exemples de code

Vous pouvez voir un exemple sur l’indexer une série de données JSON complexe dans Azure rechercher et effectuer un certain nombre de requêtes sur ce groupe de données à cette [GitHub mis en pension](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Étape suivante

[Vote pour la prise en charge native pour les types de données complexes](https://feedback.azure.com/forums/263029-azure-search) sur la UserVoice recherche Azure page et fournissez toute entrée supplémentaire que vous voulez nous à prendre en considération en ce qui concerne l’implémentation des fonctionnalités. Vous pouvez également communiquer aux me directement sur Twitter à @liamca.


 