## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Spécification de définition de la structure des groupes de données rectangulaire
La section structure dans les jeux de données JSON est une section **facultative** pour les tableaux rectangulaire (avec lignes et colonnes) et constituée une collection de colonnes de la table. Vous allez utiliser la section structure pour des informations de type fournissant pour les conversions ou effectuant des mappages de colonnes. Les sections suivantes décrivent ces fonctionnalités en détail. 

Chaque colonne contienne les propriétés suivantes :

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| nom | Nom de la colonne. | Oui |
| type | Type de données de la colonne. Consultez la section des conversions de type ci-dessous pour plus d’informations savoir à quel moment devez-vous spécifier des informations de type | N° |
| culture | .NET en fonction de la culture à utiliser lors de type n’est spécifié et est de type .NET Datetime ou Datetimeoffset. Valeur par défaut est « en-us ».  | N° |
| mettre en forme | Mettre en forme de chaîne à utiliser lors de type n’est spécifié et est de type .NET Datetime ou Datetimeoffset. | N° |

L’exemple suivant illustre la section structure JSON pour une table qui comporte trois colonnes ID d’utilisateur, nom et lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilisez les instructions suivantes pour quand inclure les informations « structure » et les éléments à inclure dans la section **structure** .

- **Pour les sources de données structurées** qui stockent les informations de schéma et de type de données ainsi que les données elles-mêmes (sources comme table Azure SQL Server, Oracle, etc.), vous devez spécifier la section « structure » uniquement si vous voulez suivre le mappage des colonnes de colonnes source spécifique à des colonnes spécifiques dans récepteur et leurs noms ne sont pas les mêmes (voir les détails dans la section de mappage de colonne ci-dessous). 

    Comme indiqué ci-dessus, les informations de type sont facultatives dans la section « structure ». Structurées sources, informations sur le type sont déjà disponibles dans le cadre de définition du jeu de données dans le magasin de données, pour ne pas inclure les informations de type lorsque vous n’incluez pas la section « structure ».
- **Pour le schéma de sources de données lecture (spécifiquement Azure blob)** vous pouvez choisir de stocker les données sans stocker toute information de type ou de schéma avec les données. Pour ces types de sources de données, vous devez inclure « structure » dans les 2 cas suivants :
    - Vous voulez faire mappage de colonnes.
    - Lorsque le jeu de données est une source d’une activité de copie, vous pouvez fournir des informations de type dans « structure » et usine données utilisera ces informations de type pour la conversion en types natifs pour le récepteur. Voir [déplacer des données vers et à partir d’objets Blob Azure](../articles/data-factory/data-factory-azure-blob-connector.md) article pour plus d’informations.

### <a name="supported-net-based-types"></a>Prise en charge. Types basés sur un réseau 
Usine de données prend en charge CLS compatible .NET en fonction de valeurs type suivantes pour fournir des informations de type dans « structure » pour schéma de sources de données lecture comme blob Azure.

- Int16
- Int32 
- Int64
- Unique
- Double
- Decimal
- Byte]
- Bool
- Chaîne 
- GUID
- Date/heure
- DateTimeOffset
- TimeSpan 

Pour Datetime et Datetimeoffset, vous pouvez également spécifier chaîne « culture » et « format » pour faciliter l’analyse de la chaîne de date/heure personnalisée. Voir des exemples de conversion de type ci-dessous.

