## <a name="invoking-stored-procedure-for-sql-sink"></a>Appel de procédure stockée pour recevoir SQL

Lorsque vous copiez des données dans SQL Server ou un serveur de base de données SQL/SQL Azure, un utilisateur spécifié procédure stockée peut être configurée et appelée à l’aide des paramètres supplémentaires. 

Une procédure stockée peut être exploitée lors des mécanismes de copie intégrée n’ont pas de la fonction. Ceci est généralement optimisée lorsqu’un traitement supplémentaire (fusion de colonnes, recherchez les valeurs supplémentaires, insertion dans plusieurs tables...) doivent être effectués avant le point d’insertion final des données sources dans la table de destination. 

Vous pouvez appeler une procédure stockée de choix. L’exemple suivant montre comment utiliser une procédure stockée pour effectuer un point d’insertion simple dans une table de la base de données. 

**Jeu de données de sortie**

Dans cet exemple, le type est défini sur : SqlServerTable. Attribuez-lui AzureSqlTable à utiliser avec une base de données SQL Azure. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Définir la section SqlSink dans l’activité copie JSON comme suit. Pour appeler une procédure stockée lors insérer des données, les propriétés SqlWriterStoredProcedureName et SqlWriterTableType sont nécessaires.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Dans votre base de données, définissez la procédure stockée portant le même nom en tant que SqlWriterStoredProcedureName. Il gère les données d’entrée à partir de votre source spécifiée et insertion dans la table de sortie. Notez que le nom du paramètre de la procédure stockée doit être identique tableName défini dans le fichier de tableau JSON.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Dans votre base de données, définissez le type de table portant le même nom en tant que SqlWriterTableType. Notez que le schéma de type tableau doit être identique dans le schéma retourné par vos données d’entrée.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

La fonctionnalité de procédure stockée tire parti des [Paramètres de la table](https://msdn.microsoft.com/library/bb675163.aspx).