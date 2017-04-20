## <a name="specifying-formats"></a>Spécification des formats

Azure Data Factory prend en charge les types de formats suivants : 

- [Format de texte](#specifying-textformat)
- [Format JSON](#specifying-jsonformat)
- [Format Avro](#specifying-avroformat)
- [Format ORC](#specifying-orcformat)
- [Format parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Spécification de format du texte

Si le format est défini sur **format du texte**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format** .

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | Le caractère utilisé pour séparer des colonnes dans un fichier. | Qu’un seul caractère est autorisé. La valeur par défaut est une virgule («, »). <br/><br/>Pour utiliser un caractère Unicode, reportez-vous à [Caractères Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) pour obtenir le code correspondant pour lui. Par exemple, vous pouvez envisager d’utiliser un caractère non imprimable rare qui n’existe pas probablement dans vos données : spécifiez « \u0001 » qui représente le début du titre (intégrité). | N° |
| rowDelimiter | Le caractère utilisé pour séparer les lignes dans un fichier. | Qu’un seul caractère est autorisé. La valeur par défaut est une des valeurs suivantes à la lecture : [« \r\n », « \r », « \n »] et « \r\n » lors de l’écriture. | N° |
| Dont | Le caractère spécial permettant d’ignorer un séparateur de colonnes dans le contenu du fichier d’entrée. <br/><br/>Vous ne pouvez pas spécifier dont et quoteChar pour une table. | Qu’un seul caractère est autorisé. Aucune valeur par défaut. <br/><br/>Exemple : Si vous disposez d’une virgule (', ') comme le séparateur de colonnes, mais vous voulez que la virgule dans le texte (exemple : « Bonjour, monde »), vous pouvez définir '$' comme caractère d’échappement et utiliser la chaîne « $Bonjour, monde » dans la source. | N° | 
| quoteChar | Le caractère utilisé pour une valeur de chaîne de devis. Les séparateurs de ligne et de colonne à l’intérieur de guillemets seraient traités dans le cadre de la valeur de chaîne. Cette propriété s’applique aux groupes de données d’entrée et de sortie.<br/><br/>Vous ne pouvez pas spécifier dont et quoteChar pour une table. | Qu’un seul caractère est autorisé. Aucune valeur par défaut. <br/><br/>Par exemple, si vous disposez d’une virgule (', ') comme le séparateur de colonnes mais que vous souhaitez que virgule dans le texte (exemple : < Bonjour, monde >), vous pouvez définir » (les guillemets) en tant que le caractère guillemet et l’utilisation de la chaîne « Bonjour, monde » dans la source. | N° |
| nullValue | Un ou plusieurs caractères utilisés pour représenter une valeur null. | Un ou plusieurs caractères. Les valeurs par défaut sont « \N » et « NULL » sur lecture et « \N » lors de l’écriture. | N° |
| encodingName | Spécifiez le nom de codage. | Un nom de codage valide. consultez [Encoding.EncodingName propriété](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemple : 1 250 windows ou shift_jis. La valeur par défaut est UTF-8. | N° | 
| firstRowAsHeader | Spécifie s’il faut considérer la première ligne comme en-tête. Pour un jeu de données d’entrée, Data Factory lit première ligne comme en-tête. Pour un jeu de données de sortie, Data Factory écrit première ligne comme en-tête. <br/><br/>Voir [scénarios d’utilisation **firstRowAsHeader** et **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) pour les exemples de scénarios. | Vrai<br/>Faux (par défaut) | N° |
| skipLineCount | Indique le nombre de lignes à ignorer lors de la lecture des données à partir de fichiers d’entrée. Si skipLineCount et firstRowAsHeader sont spécifiés, les lignes sont ignorés tout d’abord et ensuite les informations d’en-tête sont en lecture à partir du fichier d’entrée. <br/><br/>Voir [scénarios d’utilisation firstRowAsHeader et skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) pour les exemples de scénarios. | Nombre entier | N° | 
| treatEmptyAsNull | Spécifie s’il faut considérer chaîne nulle ou vide comme une valeur null lors de la lecture des données à partir d’un fichier d’entrée. | Vrai (par défaut)<br/>Faux | N° |  

#### <a name="textformat-example"></a>Exemple de format du texte
L’exemple suivant montre certaines des propriétés de format pour le format du texte.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Pour utiliser dont au lieu de quoteChar, remplacez la ligne par quoteChar avec la dont suivant :

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scénarios d’utilisation firstRowAsHeader et skipLineCount

- Vous effectuez la copie d’une source de fichier non vers un fichier texte et que vous souhaitez pour ajouter une ligne d’en-tête contenant les métadonnées de schéma (par exemple : schéma SQL). Spécifiez **firstRowAsHeader** en tant que true dans le groupe de données de sortie pour ce scénario. 
- Vous effectuez la copie d’un fichier texte contenant une ligne d’en-tête pour un récepteur de fichier non et que vous souhaitez supprimer cette ligne. Spécifiez **firstRowAsHeader** en tant que true dans le jeu de données d’entrée.
- Vous effectuez la copie d’un fichier texte et que vous souhaitez ignorer quelques lignes au début qui ne contiennent aucune information d’en-tête ou de données. Spécifiez **skipLineCount** pour indiquer le nombre de lignes à ignorer. Si le reste du fichier contient une ligne d’en-tête, vous pouvez également spécifier **firstRowAsHeader**. Si **skipLineCount** et **firstRowAsHeader** sont spécifiés, les lignes sont ignorés tout d’abord et ensuite les informations d’en-tête sont en lecture à partir du fichier d’entrée

### <a name="specifying-avroformat"></a>Si vous spécifiez AvroFormat
Si le format est défini sur AvroFormat, vous n’avez pas besoin spécifier les propriétés de la section Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "AvroFormat",
    }

Pour utiliser le format Avro dans une table Hive, vous pouvez consulter [de la ruche Apache didacticiel](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Notez les points suivants :  

- [Types de données complexes](http://avro.apache.org/docs/current/spec.html#schema_complex) ne sont pas prises en charge (enregistrements, enums, tableaux, cartes, unions et fixe)

### <a name="specifying-jsonformat"></a>Si vous spécifiez JsonFormat

Si le format est défini sur **JsonFormat**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format** .

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| filePattern | Indiquer le modèle de données stockées dans chaque fichier JSON. Valeurs autorisées sont : **setOfObjects** et **arrayOfObjects**. La valeur **par défaut** est : **setOfObjects**. Voir suivre des sections pour plus d’informations sur ces modèles.| N° |
| encodingName | Spécifiez le nom de codage. Pour la liste des noms de codage valides, voir : [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) propriété. Par exemple : 1 250 windows ou shift_jis. La valeur **par défaut** est : **UTF-8**. | N° | 
| nestingSeparator | Caractère qui est utilisé pour séparer les niveaux d’imbrication. La valeur par défaut est «. » (point). | N° | 


#### <a name="setofobjects-file-pattern"></a>modèle de fichier setOfObjects

Chaque fichier contient objet unique ou concaténées délimité par des courbes de multiples objets. Lorsque cette option est sélectionnée dans un jeu de données de sortie, copie activité génère un seul fichier JSON avec chaque objet par ligne (ligne par des virgules).

**objet unique** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**JSON ligne par des virgules** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concaténée**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>modèle de fichier arrayOfObjects. 

Chaque fichier contient un tableau d’objets. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Exemple de JsonFormat

Si vous avez un fichier JSON avec le contenu suivant :  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

et que vous voulez copier dans une table SQL Azure au format suivant : 

ID  | Name.First | Name.Middle | Name.Last | Balises
--- | ---------- | ----------- | --------- | ----
1 | John | null | Dupont | [« Données Factory », « Azure »]

Le jeu de données d’entrée avec type JsonFormat est définie comme suit : (définition partielle avec que les parties)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Si la structure n’est pas définie, l’activité de copie fusionne la structure par défaut et copiez chaque élément. 

#### <a name="supported-json-structure"></a>Structure de JSON pris en charge
Notez les points suivants : 

- Chaque objet avec une collection de paires nom/valeur correspond à une seule ligne de données dans un format tabulaire. Objets peuvent être imbriquées et vous pouvez définir comment fusionner la structure dans un jeu de données avec le séparateur d’imbrication (.) par défaut. Consultez l' [exemple JsonFormat](#jsonformat-example) qui précède la section pour obtenir un exemple.  
- Si la structure n’est pas définie dans le dataset Data Factory, l’activité de copie détecte le schéma du premier objet et fusionner la totalité de l’objet. 
- Si l’entrée JSON a un tableau, l’activité de copie convertit la valeur de tableau entier dans une chaîne. Vous pouvez choisir d’ignorer en utilisant le [mappage de colonnes ou de filtrage](#column-mapping-with-translator-rules).
- S’il existe des noms en double au même niveau, l’activité de copie sélectionne la dernière.
- Noms des propriétés respectent la casse. Deux propriétés avec le même nom, mais différentes enveloppes sont considérées comme deux propriétés distinctes. 

### <a name="specifying-orcformat"></a>Si vous spécifiez OrcFormat
Si le format est défini sur OrcFormat, vous n’avez pas besoin spécifier les propriétés de la section Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Si vous ne voulez pas copier fichiers ORC **comme-est** entre en local et nuage de banques de données, vous devez installer le JRE 8 (environnement d’exécution Java) sur votre ordinateur passerelle. Une passerelle 64 bits nécessite JRE 64 bits et 32 bits passerelle nécessite JRE 32 bits. Vous pouvez trouver les deux versions [d’ici](http://go.microsoft.com/fwlink/?LinkId=808605). Sélectionnez l’option appropriée.

Notez les points suivants :

-   Données complexes types ne sont pas prises en charge (structure, carte, liste, UNION)
-   Fichier ORC comporte trois [options liés à la compression](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): aucun, ZLIB, SNAPPY. Données usine prend en charge la lecture de données à partir du fichier ORC dans un des formats suivants compressés. Il utilise la compression codec est dans les métadonnées pour lire les données. Toutefois, lorsque vous écrivez dans un fichier ORC, Data Factory choisit ZLIB, qui représente la valeur par défaut pour ORC. Il n’existe actuellement aucune option pour ignorer ce comportement. 

### <a name="specifying-parquetformat"></a>Si vous spécifiez ParquetFormat
Si le format est défini sur ParquetFormat, vous n’avez pas besoin spécifier les propriétés de la section Format dans la section typeProperties. Exemple :

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Si vous ne copiez pas les fichiers Parquet **en tant que-est** entre en local et nuage magasins de données, vous devez installer le JRE 8 (environnement d’exécution Java) sur votre ordinateur passerelle. Une passerelle 64 bits nécessite JRE 64 bits et 32 bits passerelle nécessite JRE 32 bits. Vous pouvez trouver les deux versions [d’ici](http://go.microsoft.com/fwlink/?LinkId=808605). Sélectionnez l’option appropriée.

Notez les points suivants :

-   Données complexes types ne sont pas prises en charge (carte, liste)
-   Fichier de parquet inclut les options de compression suivantes : aucun, SNAPPY, GZIP et LZO. Données usine prend en charge la lecture de données à partir du fichier ORC dans un des formats suivants compressés. Il utilise le codec de compression dans les métadonnées pour lire les données. Toutefois, lors de l’écriture dans un fichier Parquet, Data Factory choisit SNAPPY, qui représente la valeur par défaut pour le format Parquet. Il n’existe actuellement aucune option pour ignorer ce comportement. 
