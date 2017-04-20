<properties 
    pageTitle="Déplacer des données de Cassandra à l’aide de Data Factory | Microsoft Azure" 
    description="Découvrez comment déplacer des données d’une base de données locale Cassandra à l’aide d’Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Déplacer des données d’une base de données locale Cassandra à l’aide d’usine de données Azure
Cet article explique comment vous pouvez utiliser l’activité de copie dans une usine de données Azure pour copier les données d’une base de données locale Cassandra dans le magasin de données répertorié sous récepteur de colonne dans la section [Sources pris en charge et les récepteurs](data-factory-data-movement-activities.md#supported-data-stores) . Cet article s’appuie sur l’article [activités de déplacement des données](data-factory-data-movement-activities.md) , qui présente une vue d’ensemble de déplacement de données avec l’activité de copie et les combinaisons de magasin de données prises en charge.

Usine données prend actuellement en charge uniquement déplacement des données à partir d’une base de données Cassandra pris en charge de [banques de données récepteur](data-factory-data-movement-activities.md#supported-data-stores), mais pas de déplacement des données provenant d’autres banques de données à une base de données Cassandra.

## <a name="prerequisites"></a>Conditions préalables
Service Azure Data Factory pouvoir vous connecter à votre base de données locale Cassandra, vous devez installer les éléments suivants : 

- Passerelle de gestion des données 2.0 ou une version ultérieure sur le même ordinateur qui héberge la base de données ou sur un ordinateur distinct afin d’éviter la concurrence des ressources avec la base de données. Passerelle de gestion des données est un logiciel qui se connecte des sources de données locales aux services cloud de façon sécurisée et gérée. Voir [déplacer des données entre en local et nuage](data-factory-move-data-between-onprem-and-cloud.md) article pour plus d’informations sur la passerelle de gestion des données.
  
    Lorsque vous installez la passerelle, il installe automatiquement un pilote ODBC Cassandra Microsoft utilisé pour vous connecter à la base de données Cassandra. 

> [AZURE.NOTE] Voir [passerelle résoudre des problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle problèmes correspondants. 

## <a name="copy-data-wizard"></a>Copier l’Assistant de données
Pour créer une opportunité qui copie les données à partir d’une base de données Cassandra à n’importe quel récepteur pris en charge banques de données, le plus simple consiste à utiliser l’Assistant copie de données. Voir [didacticiel : créer une opportunité à l’aide de l’Assistant copie](data-factory-copy-data-wizard-tutorial.md) pour un bref aperçu sur la création d’un pipeline à l’aide de l’Assistant copie de données. 

L’exemple suivant fournit des exemples de définitions JSON que vous pouvez utiliser pour créer une opportunité à l’aide de [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md). Ils montrent comment copier des données à partir de la base de données Cassandra au stockage d’objets Blob Azure. Toutefois, les données peuvent être copiées à certaines de la récepteurs indiquée [ici](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Exemple : Copier des données à partir de Cassandra vers Blob
L’exemple copie les données à partir d’une base de données Cassandra à un blob Azure chaque heure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivantes les exemples. Données peuvent être copiées directement à un des récepteurs indiqués dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory. 

- Un service de type [OnPremisesCassandra](#onpremisescassandra-linked-service-properties)lié.
- Un service de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)lié.
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [CassandraTable](#cassandratable-properties).
- Un résultat [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) avec une activité copie qui utilise [CassandraSource](#cassandrasource-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Service Cassandra lié**

Cet exemple utilise le service **Cassandra** lié. Consultez la section [Cassandra lié service](#onpremisescassandra-linked-service-properties) pour les propriétés pris en charge par ce service lié.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
            }
        }
    }


**Service de stockage lié Azure**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
        "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

**Jeu de données d’entrée Cassandra**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
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

Le fait de définir **externes** **true** indique le service de données par défaut que le jeu de données externe à l’usine de données et n’est pas produit par une activité dans le factory de données.

**Jeu de données de sortie Blob Azure**

Les données sont écrites dans un nouveau blob toutes les heures (fréquence : heure, l’intervalle : 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Pipeline avec une activité copie**

Le pipeline contient une activité de copie qui est configuré pour utiliser les jeux de données d’entrée et de sortie et planifiée pour chaque heure. Dans le pipeline de définition de JSON, le type de **source** est défini sur **CassandraSource** et **récepteur de** type est défini sur **BlobSink**. 

Voir [les propriétés de type RelationalSource](#cassandrasource-type-properties) pour la liste des propriétés pris en charge par le RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
            ]   
        }
    }
## <a name="onpremisescassandra-linked-service-properties"></a>Propriétés du service OnPremisesCassandra lié

Le tableau suivant fournit la description pour les éléments JSON spécifiques au service Cassandra lié.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- | 
| type | La propriété doit être définie : **OnPremisesCassandra** | Oui |
| hôte | Une ou plusieurs adresses IP ou les noms d’hôte des serveurs Cassandra.<br/><br/>Spécifiez une liste d’adresses IP ou les noms d’hôtes pour vous connecter à tous les serveurs simultanément séparées par des virgules. | Oui | 
| port | Port TCP utilisé par le serveur Cassandra pour écouter les connexions client. | Non, valeur par défaut : 9042 |
| authenticationType | Base ou anonyme | Oui |
| nom d’utilisateur |Spécifiez le nom d’utilisateur pour le compte d’utilisateur. | Oui, si authenticationType est défini sur Basic. |
| mot de passe | Spécifiez le mot de passe pour le compte d’utilisateur.  | Oui, si authenticationType est défini sur Basic. |
| nom de la passerelle | Le nom de la passerelle est utilisé pour vous connecter à la base de données locale Cassandra. | Oui |
| encryptedCredential | Informations d’identification chiffrées par la passerelle. | N° | 

## <a name="cassandratable-properties"></a>Propriétés CassandraTable

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont identiques pour tous les types de jeu de données (SQL Azure, blob Azure, table Azure, etc..).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **CassandraTable** possède les propriétés suivantes

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| keyspace | Nom de la keyspace ou le schéma de base de données Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |
| tableName | Nom de la table de base de données Cassandra. | Oui (si la **requête** pour **CassandraSource** n’est pas définie). |


## <a name="cassandrasource-type-properties"></a>Propriétés de type CassandraSource
Pour une liste complète des sections et les propriétés disponibles pour la définition des activités, voir l’article [Création Pipelines](data-factory-create-pipelines.md) . Propriétés telles que nom, description, entrée et sortie tables et stratégie sont disponibles pour tous les types d’activités. 

Propriétés disponibles dans la section typeProperties de l’activité dépendent quant à eux de chaque type d’activité. Activité de copie, ils varient selon les types de sources et les récepteurs.

Lors de la source est de type **CassandraSource**, les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| requête | Utiliser la requête personnalisée pour lire les données. | Requête SQL-92 ou CQL. Voir les [informations de référence CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Lorsque vous utilisez la requête SQL, spécifiez **keyspace name.table nom** pour représenter la table que vous voulez interroger. | N° (si tableName et keyspace sur jeu de données sont définis).  |
| consistencyLevel | Le niveau de cohérence indique combien réplicas doivent répondre à une demande de lecture avant de renvoyer des données à l’application cliente. Cassandra vérifie le nombre spécifié de réplica pour les données répondre à la demande de lecture. | UN, DEUX, TROIS, QUORUM, TOUTES LES, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Pour plus d’informations, voir [configuration la cohérence des données](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) . | Non. Valeur par défaut est 1. |  


### <a name="type-mapping-for-cassandra"></a>Mappage du type pour Cassandra
Type Cassandra | Type basé sur .net
--------------- | ---------------
ASCII | Chaîne 
BIGINT | Int64
OBJETS BLOB | Byte]
VALEUR BOOLÉENNE | Valeur booléenne
DECIMAL | Decimal
DOUBLE | Double
RENDRE FLOTTANT | Unique
INET | Chaîne
ENT | Int32
TEXTE | Chaîne
HORODATAGE | Date/heure
TIMEUUID | GUID
UUID | GUID
VARCHAR | Chaîne
VARINT | Decimal

> [AZURE.NOTE]  
> Pour la collection de sites types (carte, jeu, liste, etc.), reportez-vous à [utiliser des types de collection de sites Cassandra à l’aide de table virtuelle](#work-with-collections-using-virtual-table) section. 
> 
> Types définis par l’utilisateur ne sont pas pris en charge.
> 
> La longueur de colonne binaire et colonne de type chaîne les longueurs ne peut pas être supérieure à et 4 000. 

## <a name="work-with-collections-using-virtual-table"></a>Travailler avec des collections à l’aide de la table virtuelle
Azure Data Factory utilise un pilote ODBC intégré pour se connecter à et copier des données à partir de votre base de données Cassandra. Pour les types de collection de sites, y compris la carte, un ensemble et liste, le pilote convertit à nouveau les données en tables virtuelles correspondants. En particulier, si une table contient des colonnes de la collection de sites, le pilote génère les tables virtuelles suivantes :

-   Une **table de base**, qui contient les mêmes données sous forme de la table réelle à l’exception des colonnes de la collection de sites. La table de base utilise le même nom que la table réelle qu’elle représente.
-   **Table virtuelle** pour chaque colonne de collection de sites, qui se développe les données imbriquées. Les tables virtuelles qui représentent des collections de sites sont nommés en utilisant le nom de la table réelle, un séparateur de «_vt_» et le nom de la colonne.

Tables virtuelles font référence aux données dans la table réelle, l’activation du pilote accéder aux données dénormalisées. Pour plus d’informations, reportez-vous à la section exemple. Vous pouvez accéder le contenu des collections Cassandra par l’interrogation et le joindre les tables virtuels.

Vous pouvez utiliser l' [Assistant copie](data-factory-data-movement-activities.md#data-factory-copy-wizard) pour intuitive afficher la liste des tables de base de données Cassandra, y compris les tables virtuelles et afficher un aperçu des données à l’intérieur. Vous pouvez également créer une requête dans l’Assistant copie et valider pour afficher le résultat.

### <a name="example"></a>Exemple
Par exemple, le suivant « ExampleTable » est une table de base de données Cassandra qui contient une colonne clé primaire d’entiers nommé « pk_int », une colonne de texte nommé value, une colonne de liste, une colonne de table et une colonne de jeu (appelées « StringSet »).

pk_int | Valeur | Liste | Carte |   StringSet
------ | ----- | ---- | --- | --------
1 | « valeur exemple 1 » | ["1", "2", "3"]  | {« S1 » : « A », « S2 » : « b »} | {"A", "B", "C"}
3 | « valeur exemple 3 » | [« 100 », « 101 », « 102 », « 105 »] | {« S1 » : « t »} | {"A", "E"}

Le pilote génère plusieurs tables virtuelles pour représenter ce tableau unique. Les colonnes de clé étrangère dans les tables virtuelles faire référence à la clé primaire dans la table réelle et indiquent les lignes de table réelle la ligne du tableau virtuel correspond à. 

La première table virtuelle est la table de base nommée « ExampleTable » est présentée dans le tableau suivant. La table de base contient les mêmes données sous forme de la table de base de données d’origine à l’exception des collections de sites, qui sont omises de ce tableau et développés dans d’autres tables virtuelles.

pk_int | Valeur
------ | -----
1 | « valeur exemple 1 »
3 | « valeur exemple 3 »

Les tableaux suivants présentent les tables virtuelles qui renormalize les données à partir des colonnes de liste, carte et StringSet. Les colonnes avec des noms qui se terminent par « _index » ou « _key » indiquent la position des données dans la liste ou la carte d’origine. Les colonnes avec des noms qui se terminent par « _value » contiennent les données développées à partir de la collection de sites.

#### <a name="table-exampletablevtlist"></a>Table « ExampleTable_vt_List » :
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Table « ExampleTable_vt_Map » :
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Table « ExampleTable_vt_StringSet » :
pk_int | StringSet_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Voir [Guide d’optimisation et des performances d’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les principaux facteurs impact sur les performances de déplacement de données (copie activité) dans Azure Data Factory et de différentes manières d’optimiser.
