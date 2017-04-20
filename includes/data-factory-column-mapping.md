## <a name="column-mapping-with-translator-rules"></a>Mappage des colonnes avec les règles de la translator
Colonne mappage peut être utilisé pour spécifier comment les colonnes spécifiées dans la « structure » de la carte de la table source aux colonnes spécifiée dans la structure » « de table récepteur. La propriété **columnMapping** est disponible dans la section **typeProperties** de l’activité de copie.

Mappage des colonnes prend en charge les scénarios suivants :

- Toutes les colonnes de la table source « structure » sont mappés à toutes les colonnes du tableau récepteur « structure ».
- Un sous-ensemble des colonnes de la table source « structure » sont mappés à toutes les colonnes du tableau récepteur « structure ».

Les éléments suivants sont les conditions d’erreur et peuvent provoquer une exception :

- Moins de colonnes ou plus de colonnes dans la « structure » de la table récepteur que spécifié dans le mappage.
- Mappage en double.
- Résultat de la requête SQL n’a pas un nom de colonne spécifié dans le mappage.

## <a name="column-mapping-samples"></a>Exemples de mappage de colonne
> [AZURE.NOTE] Les exemples ci-dessous sont appliquent aux SQL Azure et Blob Azure mais sont appliquent à n’importe quel magasin de données qui prend en charge les jeux de données rectangulaire. Vous devrez ajuster dataset et les définitions de service liées dans les exemples ci-dessous pour pointer vers des données dans la source de données pertinentes. 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemple 1 – colonne mappage à partir de SQL Azure aux objets blob Azure
Dans cet exemple, la table d’entrée présente une structure et qu’il pointe vers une table SQL dans une base de données SQL Azure.

    {
        "name": "AzureSQLInput",
        "properties": {
            "structure": 
             [
               { "name": "userid"},
               { "name": "name"},
               { "name": "group"}
             ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Dans cet exemple, la table de sortie présente une structure et qu’il pointe vers un blob dans un espace de stockage blob Azure.

    {
        "name": "AzureBlobOutput",
        "properties":
        {
             "structure": 
              [
                    { "name": "myuserid"},
                    { "name": "myname" },
                    { "name": "mygroup"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Vous trouverez ci-dessous le JSON pour l’activité. Colonnes de la source mappés aux colonnes de récepteur (**columnMappings**) à l’aide de propriété **Translator** .

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "Copy",
        "inputs":  [ { "name": "AzureSQLInput"  } ],
        "outputs":  [ { "name": "AzureBlobOutput" } ],
        "typeProperties":    {
            "source":
            {
                "type": "SqlSource"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
            }
        },
       "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

**Flux de mappage de colonne :**

![Flux de mappage de colonne](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemple 2-colonne mappage avec une requête SQL à partir de SQL Azure à blob Azure
Dans cet exemple, une requête SQL est utilisée pour extraire les données à partir de SQL Azure au lieu de simplement spécifiant le nom de table et les noms de colonne dans la section « structure ». 

    {
        "name": "CopyActivity",
        "description": "description", 
        "type": "CopyActivity",
        "inputs":  [ { "name": " AzureSQLInput"  } ],
        "outputs":  [ { "name": " AzureBlobOutput" } ],
        "typeProperties":
        {
            "source":
            {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
            },
            "sink":
            {
                "type": "BlobSink"
            },
            "Translator": 
            {
                "type": "TabularTranslator",
                "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
            }
        },
        "scheduler": {
              "frequency": "Hour",
              "interval": 1
            }
    }

Dans ce cas, les résultats de la requête sont mappés tout d’abord aux colonnes spécifiées dans « structure » de source. Ensuite, colonnes de la source « structure » sont mappés aux colonnes de récepteur « structure » avec les règles spécifiées dans columnMappings.  Supposons que la requête renvoie 5 colonnes, deux colonnes supplémentaires, puis ceux qui sont spécifiés dans la structure » « de source.

**Flux de mappage de colonne**

![Colonne mappage flux-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)







