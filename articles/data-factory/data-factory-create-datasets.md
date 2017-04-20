<properties 
    pageTitle="Créer des groupes de données dans des données Azure usine | Microsoft Azure" 
    description="Apprenez à créer des groupes de données dans Azure Data Factory avec des exemples qui utilisent des propriétés telles que décalage et anchorDateTime."
    keywords="créer le jeu de données, jeu de données exemple, offset-exemple"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="shlo"/>

# <a name="datasets-in-azure-data-factory"></a>Jeux de données dans des données Azure usine
Cet article décrit les jeux de données dans Azure Data Factory et inclut des exemples de tels que décalage anchorDateTime et bases de données de décalage/style.

Lorsque vous créez un jeu de données, vous créez un pointeur vers les données que vous souhaitez traiter. Les données sont traitées (entrée/sortie) dans une activité et une activité est contenue dans un pipeline. Un jeu de données d’entrée représente l’entrée d’une activité dans le pipeline et un jeu de données de sortie représente le résultat de l’activité.

Jeux de données identifient les données dans différents magasins de données, tels que des tables, des fichiers, des dossiers et des documents. Après avoir créé un jeu de données, vous pouvez l’utiliser avec les activités dans un pipeline. Par exemple, un jeu de données peut être un jeu de données d’entrée/sortie d’une activité de copie ou d’une activité HDInsightHive. Le portail Azure vous donne une présentation visuelle de tous vos pipelines et les données entrées et sorties. En un clin de œil, vous voyez toutes les relations et les dépendances de vos pipelines dans toutes les sources de votre afin que vous sachiez toujours d'où proviennent les données et où il va.

Dans Azure Data Factory, vous pouvez obtenir des données à partir d’un jeu de données à l’aide de copie activité dans un pipeline.

> [AZURE.NOTE] Si vous débutez dans Azure Data Factory, voir [présentation Azure Data Factory](data-factory-introduction.md) pour une vue d’ensemble du service Azure Data Factory. Consultez [créer votre première usine de données](data-factory-build-your-first-pipeline.md) pour un didacticiel pour créer votre première usine de données. Ces deux articles fournissent des qu'informations nécessaires pour mieux comprendre cet article vous d’arrière-plan. 

## <a name="define-datasets"></a>Définir des groupes de données
Un jeu de données dans Azure Data Factory est définie comme suit : 


    {
        "name": "<name of dataset>",
        "properties": {
            "type": "<type of dataset: AzureBlob, AzureSql etc...>",
            "external": <boolean flag to indicate external data. only for input datasets>,
            "linkedServiceName": "<Name of the linked service that refers to a data store.>",
            "structure": [
                {
                    "name": "<Name of the column>",
                    "type": "<Name of the type>"
                }
            ],
            "typeProperties": {
                "<type specific property>": "<value>",
                "<type specific property 2>": "<value 2>",
            },
            "availability": {
                "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
                "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
            },
           "policy": 
            {      
            }
        }
    }

Le tableau suivant décrit les propriétés dans le JSON ci-dessus :   

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- |
| nom | Nom du groupe de données. Voir [Azure Data Factory - dénomination des règles](data-factory-naming-rules.md) pour les règles d’appellation. | Oui | NA |
| type | Type de dataset. Spécifier l’un des types pris en charge par Azure Data Factory (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Pour plus d’informations, voir [Dataset Type](#Type) . | Oui | NA |
| structure | Schéma du dataset<br/><br/>Pour plus d’informations, voir la [Structure d’un Dataset](#Structure) section. | Non. | NA |
| typeProperties | Propriétés correspondant au type sélectionné. Pour plus d’informations sur les types pris en charge et leurs propriétés, reportez-vous à la section [Type de Dataset](#Type) . | Oui | NA |
| externes | Indicateur booléen pour indiquer si un jeu de données est explicitement obtenu par un pipeline d’usine données ou non.  | N° | faux | 
| disponibilité | Définit la fenêtre de traitement ou le modèle de découpage de production dataset. <br/><br/>Pour plus d’informations, consultez [La disponibilité Dataset](#Availability) section. <br/><br/>Pour plus d’informations sur le jeu de données modèle, voir l’article de [planification et l’exécution](data-factory-scheduling-and-execution.md) de séparation. | Oui | NA
| stratégie | Définit les critères ou la condition que les secteurs du jeu de données doivent remplir. <br/><br/>Pour plus d’informations, consultez [Stratégie Dataset](#Policy) section. | N° | NA |

## <a name="dataset-example"></a>Exemple de jeu de données
Dans l’exemple suivant, le dataset représente une table nommée **MyTable** d’une **base de données SQL Azure**. 

    {
        "name": "DatasetSample",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": 
            {
                "tableName": "MyTable"
            },
            "availability": 
            {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

Notez les points suivants : 

- type est défini sur AzureSqlTable.
- propriété de type tableName (spécifique au type AzureSqlTable) est définie sur MyTable.
- linkedServiceName fait référence à un service de type AzureSqlDatabase lié. Voir la définition du service liée suivante. 
- fréquence de disponibilité est définie sur jour et intervalle est défini sur 1, ce qui signifie que le secteur est produit quotidiennement.  

AzureSqlLinkedService est définie comme suit :

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }

Dans le JSON ci-dessus : 

- type est défini sur AzureSqlDatabase
- propriété de type connectionString spécifie les informations pour vous connecter à une base de données SQL Azure.  


Comme vous pouvez le voir, le service lié définit la connexion à une base de données SQL Azure. Le dataset définit quelle table est utilisé comme une entrée/sortie pour l’activité dans un pipeline. La section activité dans votre [pipeline](data-factory-create-pipelines.md) JSON indique si le jeu de données est utilisé comme un jeu de données d’entrée ou en sortie.


> [AZURE.IMPORTANT] À moins d’avoir un jeu de données en cours de fabrication en usine de données Azure, il doit être marqué comme **externe**. En règle générale, ce paramètre s’applique aux entrées de la première activité dans un pipeline.   

## <a name="Type"></a>Type de jeu de données
Les sources de données prises en charge et les types de jeu de données sont alignés. Voir les rubriques figurant dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores) pour plus d’informations sur les types et la configuration des jeux de données. Par exemple, si vous utilisez des données à partir d’une base de données SQL Azure, cliquez sur base de données SQL Azure dans la liste des banques de données prises en charge pour afficher des informations détaillées.  

## <a name="Structure"></a>Structure d’un DataSet
La section **structure** définit le schéma du dataset. Elle contient une collection de noms et les types de données des colonnes.  Dans l’exemple suivant, le jeu de données a trois colonnes slicetimestamp NomProjet et pageviews et qu’ils sont de type : chaîne, chaîne et décimal respectivement.

    structure:  
    [ 
        { "name": "slicetimestamp", "type": "String"},
        { "name": "projectname", "type": "String"},
        { "name": "pageviews", "type": "Decimal"}
    ]

## <a name="Availability"></a>Disponibilité du jeu de données
La section **disponibilité** dans un jeu de données définit la fenêtre de traitement (horaire, quotidienne, hebdomadaire, etc.) ou le modèle de découpage pour le dataset. Consultez [planification et l’exécution de](data-factory-scheduling-and-execution.md) l’article pour plus d’informations sur le modèle de séparation et de dépendance dataset. 

La section disponibilité suivante indique que le jeu de données de sortie est produit toutes les heures (ou) entrée dataset est toutes les heures disponible :

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 1   
    }

Le tableau suivant décrit les propriétés que vous pouvez utiliser dans la section disponibilité : 

| Propriété | Description | Obligatoire | Par défaut |
| -------- | ----------- | -------- | ------- |
| fréquence | Spécifie l’unité de temps de fabrication secteur de dataset.<br/><br/>**Fréquence pris en charge**: Minute, heure, jour, semaine, mois | Oui | NA |
| intervalle | Spécifie un multiplicateur pour la fréquence<br/><br/>« Intervalle de fréquence x » détermine la fréquence à laquelle le secteur se produit.<br/><br/>Si vous devez le jeu de données pour découper toutes les heures, vous définissez la **valeur fréquence** **heure**, et l' **intervalle** **1**.<br/><br/>**Remarque :** Si vous spécifiez fréquence minute, nous vous recommandons de définir l’intervalle pas moins de 15 | Oui | NA |
| style | Indique si le secteur doit être produit à la début/fin de l’intervalle.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si fréquence est définie sur mois et style est défini sur EndOfInterval, le secteur se produit le dernier jour du mois. Si le style est défini sur StartOfInterval, le secteur se produit le premier jour du mois.<br/><br/>Si fréquence est définie à jour et style est défini sur EndOfInterval, le secteur se produit dans la dernière heure du jour.<br/><br/>Si fréquence est définie sur heure et style est défini sur EndOfInterval, le secteur se produit à la fin de l’heure. Par exemple, pour un secteur pour PM 1 – 2 PM période, le secteur est généré à 14. | N° | EndOfInterval |
| anchorDateTime | Définit la position absolue dans le temps permet de calculer les limites de la section dataset par le planificateur. <br/><br/>**Remarque :** Si la AnchorDateTime comprend les éléments de date qui sont plus de précision que la fréquence les parties plus granulaires sont ignorées. <br/><br/>Par exemple, si l' **intervalle** est **toutes les heures** (fréquence : heure et intervalle : 1) et **AnchorDateTime** contient **minutes et secondes**, puis les parties **minutes et secondes** de la AnchorDateTime sont ignorés. | N° | 01/01/0001 |
| DECALER | TimeSpan par lequel le début et fin de tous les secteurs du jeu de données sont déplacées. <br/><br/>**Remarque :** Si anchorDateTime et offset sont spécifiés, le résultat est le décalage combiné. | N° | NA |

### <a name="offset-example"></a>exemple de décalage

Tous les jours découpe qui commencent à 6 heures au lieu de la minuit par défaut. 

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

La **fréquence** est définie sur **jour** et **l’intervalle** est défini sur **1** (une fois par jour) : Si vous souhaitez que le secteur à produire à 6 heures plutôt qu’à l’heure par défaut : 12 : 00. N’oubliez pas que cette fois est une heure au format UTC. 

## <a name="anchordatetime-example"></a>exemple d’anchorDateTime

**Exemple :** secteurs du jeu de données 23 heures qui commencent sur 2007-04-19T08:00:00

    "availability": 
    {   
        "frequency": "Hour",        
        "interval": 23, 
        "anchorDateTime":"2007-04-19T08:00:00"  
    }

## <a name="offsetstyle-example"></a>Exemple de décalage/style

Si vous devez dataset sur base mensuelle sur date et une heure (supposons 3e de chaque mois à 8:00 AM), utilisez la balise de **décalage** pour définir la date et l’heure il doit s’exécuter. 

    {
      "name": "MyDataset",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "availability": {
          "frequency": "Month",
          "interval": 1,
          "offset": "3.08:10:00",
          "style": "StartOfInterval"
        }
      }
    }


## <a name="Policy"></a>Stratégie de groupe de données

La section de **stratégie** dans la définition du jeu de données définit les critères ou la condition que les secteurs du jeu de données doivent remplir.

### <a name="validation-policies"></a>Stratégies de validation

| Nom de la stratégie | Description | Appliqué à | Obligatoire | Par défaut |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Vérifie que les données dans un **blob Azure** répond à la configuration requise de taille minimale (en mégaoctets). | Objets Blob Azure | N° | NA |
|minimumRows | Vérifie que les données dans une **base de données SQL Azure** ou une une **table Azure** contient le nombre minimal de lignes. | <ul><li>Base de données SQL Azure</li><li>Table Azure</li></ul> | N° | NA

#### <a name="examples"></a>Exemples

**minimumSizeMB :**

    "policy":
    
    {
        "validation":
        {
            "minimumSizeMB": 10.0
        }
    }

**minimumRows**

    "policy":
    {
        "validation":
        {
            "minimumRows": 100
        }
    }

### <a name="external-datasets"></a>Jeux de données externes

Jeux de données externes est ceux qui n’est pas obtenus par un pipeline en cours d’exécution dans le factory de données. Si le jeu de données est marquée comme étant **externes**, la stratégie **ExternalData** peut-être être définie pour influencer le comportement de la disponibilité de secteur dataset. 

À moins d’avoir un jeu de données en cours de fabrication en usine de données Azure, il doit être marqué comme **externe**. Ce paramètre s’applique généralement et les entrées de la première activité dans un pipeline à moins d’avoir activité ou pipeline chaîne est utilisé. 

| Nom | Description | Obligatoire | Valeur par défaut  |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Durée du délai de la vérification de la disponibilité des données externes pour le secteur donné. Par exemple, si les données sont censée pour être disponible pour toutes les heures, le contrôle pour afficher les données externes sont disponibles et le secteur correspondant est prêt peut être retardé à l’aide de dataDelay.<br/><br/>S’applique uniquement à l’heure actuelle.  Par exemple, si elle est 1:00 PM immédiatement et cette valeur est de 10 minutes, la validation commence à 1:10 PM.<br/><br/>Ce paramètre n’affecte pas les secteurs passée (secteurs avec l’heure de fin secteur + dataDelay < maintenant) sont traitées sans aucun retard.<br/><br/>Durée supérieure à 23:59 heures doivent spécifiée en utilisant le format day.hours:minutes:seconds. Par exemple, pour spécifier les 24 heures, n’utilisez pas 24:00:00 ; à la place, utilisez 1.00:00:00. Si vous utilisez 24:00:00, il est considéré comme 24 jours (24.00:00:00). Pour 1 jour et 4 heures, spécifiez 1:04:00:00. | N° | 0 |
| retryInterval | Délai d’attente entre une défaillance et la prochaine renouveler tentative. S’applique à l’heure actuelle ; Si le précédent essayez échec, nous attendre longtemps après la dernière tentative. <br/><br/>S’il s’agit de 1:00 pm immédiatement, nous commençons la première tentative. Si la durée pour terminer le premier contrôle de validation est une minute et échoué de l’opération, la nouvelle tentative suivante est auprès de 1:00 + 1 min (durée) + 1min (intervalle) = 1:02 pm. <br/><br/>Pour les secteurs par le passé, il n’y a aucun délai. La nouvelle tentative a lieu immédiatement. | N° | 01:00:00 (une minute) | 
| retryTimeout | Le délai d’expiration pour chaque nouvelle tentative.<br/><br/>Si cette propriété est définie à 10 minutes, la validation doit être effectuée dans les 10 minutes. S’il faut plu de 10 minutes pour effectuer la validation, la nouvelle tentative arrive à expiration.<br/><br/>Si toutes les tentatives de la validation arrive à expiration, le secteur est marqué comme avec délai dépassé. | N° | 10:00:00 (10 minutes) |
| maximumRetry | Nombre de fois pour vérifier la disponibilité des données externes. La valeur maximale autorisée est égal à 10. | N° | 3 | 

## <a name="scoped-datasets"></a>Jeux de données dans l’étendue
Vous pouvez créer des groupes de données qui sont étendus à un pipeline à l’aide de la propriété de **jeux de données** . Ces jeux de données peut uniquement être utilisées par activités au sein de ce pipeline mais pas par activités dans d’autres pipelines. L’exemple suivant définit un pipeline avec deux jeux de données - InputDataset rdc et OutputDataset-rdc - devant être utilisé dans le pipeline :  

> [AZURE.IMPORTANT] Dans l’étendue des groupes de données sont pris en charge uniquement avec les pipelines ponctuel (**pipelineMode** défini sur **OneTime**). Pour plus d’informations, voir [Onetime pipeline](data-factory-scheduling-and-execution.md#onetime-pipeline) .

    {
        "name": "CopyPipeline-rdc",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset-rdc"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset-rdc"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1,
                        "style": "StartOfInterval"
                    },
                    "name": "CopyActivity-0"
                }
            ],
            "start": "2016-02-28T00:00:00Z",
            "end": "2016-02-28T00:00:00Z",
            "isPaused": false,
            "pipelineMode": "OneTime",
            "expirationTime": "15.00:00:00",
            "datasets": [
                {
                    "name": "InputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "InputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/input",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": true,
                        "policy": {}
                    }
                },
                {
                    "name": "OutputDataset-rdc",
                    "properties": {
                        "type": "AzureBlob",
                        "linkedServiceName": "OutputLinkedService-rdc",
                        "typeProperties": {
                            "fileName": "emp.txt",
                            "folderPath": "adftutorial/output",
                            "format": {
                                "type": "TextFormat",
                                "rowDelimiter": "\n",
                                "columnDelimiter": ","
                            }
                        },
                        "availability": {
                            "frequency": "Day",
                            "interval": 1
                        },
                        "external": false,
                        "policy": {}
                    }
                }
            ]
        }
    }