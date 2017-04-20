### <a name="type-conversion-sample"></a>Exemples de conversion de type
L’exemple suivant est pour copier les données à partir d’un objet Blob à SQL Azure avec les conversions de types.

Supposons que le jeu de données Blob est au format CSV et contient 3 colonnes. Un d'entre eux est une colonne datetime avec un format de date/heure personnalisés à l’aide de noms Français abrégés pour le jour de la semaine.

Vous devez définir le jeu de données Source Blob comme suit ainsi que des définitions de type pour les colonnes.

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Étant donné l’instruction SQL type à tableau de mappage de type .NET ci-dessus, vous définissez la table SQL Azure avec le schéma suivant.

| Nom de colonne | Type de SQL |
| ----------- | -------- |
| ID d’utilisateur | bigint |
| nom | texte |
| LastLoginDate | date/heure |

Ensuite vous définirez le jeu de données SQL Azure comme suit. Remarque : Vous n’avez pas besoin de spécifier section « structure » avec les informations de type dans la mesure où les informations de type sont déjà spécifiées dans le magasin de données sous-jacentes.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

Dans ce cas usine données fera automatiquement le type de mettre en forme de conversions ainsi que le champ date/heure avec la date/heure personnalisé à l’aide de la culture fr-fr lors du déplacement de données à partir d’objets Blob à SQL Azure.


