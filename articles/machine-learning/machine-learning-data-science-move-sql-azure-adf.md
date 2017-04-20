<properties
    pageTitle="Déplacer des données d’un serveur SQL localement à SQL Azure avec Azure Data Factory | Azure"
    description="Configurer un pipeline chargeur qui se compose de deux activités de migration des données qui ensemble déplacement des données sur une base quotidienne entre bases de données locales et dans le cloud."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Déplacer des données d’un serveur SQL localement à SQL Azure avec Azure Data Factory

Cette rubrique vous explique comment déplacer des données d’une base de données locales SQL Server à une base de données SQL Azure via Azure Blob Storage à l’aide de la définition d’Azure données usine (application).

Les liens de **menu** suivants vers des rubriques qui décrivent l’acquisition de données dans des environnements cible où les données peuvent être stockées et traitées au cours du processus d’équipe données scientifique.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introduction : Qu’est chargeur et lorsqu’il convient pour migrer les données ?

Azure Data Factory est un service d’intégration de données entièrement gérée sur le nuage qui orchestre et automatisation déplacement et transformation de données. Le concept clé dans le modèle chargeur est pipeline. Un pipeline est un regroupement logique d’activités, chacune d’elles définit les actions à effectuer sur les données contenues dans des groupes de données. Services liées sont utilisés pour définir les informations nécessaires pour les données par défaut pour vous connecter aux ressources de données.

Avec chargeur automatique, les services de traitement des données existantes peuvent être composées dans les pipelines de données qui sont hautement disponibles et gérées dans le cloud. Ces pipelines de données peuvent être planifiées d’acquisition, préparer, transformer, leur analyse et publier des données et définition d’application gère et orchestre les données complexes et les dépendances de traitement. Solutions peuvent être rapidement générées et déployées dans le cloud, connexion d’un nombre croissant en local et de sources de données en nuage.

Vous pouvez utiliser la définition d’application :

- Lorsque les données devant être migrées en permanence dans un scénario hybride qui accède à des ressources locales et cloud 
- Lorsque les données sont traitées ou devant être modifié ou que la logique métier pour l’ajouter en cours de migration. 

Définition d’application permet la planification et le suivi des tâches à l’aide des scripts JSON simples que gérer le déplacement des données de façon régulière. Chargeur a également d’autres fonctionnalités, telles que la prise en charge pour les opérations complexes. Pour plus d’informations sur la définition d’application, consultez la documentation à [Azure données usine (chargeur)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>Le scénario

Nous allons configurer un pipeline chargeur qui se compose de deux activités de migration des données. Ensemble ils déplacement des données sur une base quotidienne entre une base de données SQL sur site et une base de données SQL Azure dans le cloud. Les deux activités sont :

* copier des données d’une base de données SQL Server localement à un compte de stockage d’objets Blob Azure
* Copiez les données à partir du compte de stockage d’objets Blob Azure dans une base de données SQL Azure.

>[AZURE.NOTE] Les étapes présentées ici ont été adaptées dans le didacticiel plus détaillé fourni par l’équipe chargeur : [déplacer des données entre sources locales et nuage avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) des références aux sections correspondantes de cette rubrique sont fournies le cas échéant.


## <a name="prereqs"></a>Conditions préalables
Ce didacticiel part du principe que vous avez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous utilisez un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devez obtenir la clé de compte utilisée pour accéder au stockage. Voir [Afficher, copier et stockage régénérer les touches d’accès](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accès à une **base de données SQL Azure**. Si vous devez configurer une base de données SQL Azure, la phrase sujet [Mise en route de la base de données SQL Microsoft Azure](../sql-database/sql-database-get-started.md) fournit des informations sur la mise en service d’une nouvelle instance d’une base de données SQL Azure.
* Installé et configuré **PowerShell Azure** localement. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Cette procédure utilise le [portail Azure](https://portal.azure.com/).


##<a name="upload-data"></a>Téléchargez les données locales SQL Server

Nous utilisons le [dataset Taxi NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) pour montrer le processus de migration. Le dataset NYC Taxi est disponible, comme indiqué dans la mesure où billet, sur Azure blob storage [NYC Taxi données](http://www.andresmh.com/nyctaxitrips/). Les données contient deux fichiers, le fichier trip_data.csv, qui contient des informations de voyage, et le fichier trip_far.csv, qui contient des informations de tarif payé pour chaque voyage. Un exemple et une description de ces fichiers sont fournies dans la zone [NYC Taxi voyages Dataset Description](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Vous pouvez adapter la procédure pour un ensemble de vos propres données ou suivez les étapes comme décrit à l’aide du dataset NYC Taxi. Pour télécharger le dataset Taxi NYC dans votre base de données SQL Server sur site, suivez la procédure décrite dans [En bloc importer des données dans la base de données SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Ces instructions s’appliquent à un serveur SQL Server sur une Machine virtuelle Azure, mais la procédure pour le téléchargement vers le serveur SQL sur site est la même.


##<a name="create-adf"></a>Créer une usine de données Azure

Les instructions permettant de créer un nouveau Data Factory Azure et un groupe de ressources dans le [portail Azure](https://portal.azure.com/) sont fournies [créer une usine de données Azure](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Nom de l' instance de nouveau chargeur *adfdsp* et le nom du groupe créé de ressources *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Installation et configuration de la passerelle de gestion des données

Pour activer vos pipelines dans une usine de données Azure pour fonctionner avec un serveur SQL locales, vous devez ajouter un service liées à l’usine de données. Pour créer un Service liés pour un serveur SQL locales, vous devez :

- Téléchargez et installez la passerelle de gestion des données de Microsoft sur l’ordinateur sur site. 
- configurer le service lié pour la source de données locale à l’utilisation de la passerelle. 

La passerelle de gestion des données sérialise et désérialise les données source et le récepteur sur l’ordinateur où il est hébergé.

Pour plus d’informations sur la passerelle de gestion des données et des instructions d’installation, voir [déplacer des données entre sources locales et nuage avec la passerelle de gestion des données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>Créer des services pour vous connecter aux ressources de données liées

Un service lié définit les informations nécessaires pour Azure Data Factory pour vous connecter à une ressource de données. La procédure pas à pas de création de services liées est fournie dans [créer des services liées](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Nous avons trois ressources dans ce scénario pour lequel services liées sont nécessaires.

1. [Services liés pour locales SQL Server](#adf-linked-service-onprem-sql)
2. [Services liés pour stockage d’objets Blob Azure](#adf-linked-service-blob-store)
3. [Service liée pour la base de données SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Services liés pour base de données locales SQL Server

Pour créer le service lié pour SQL Server sur site :

- Cliquez sur le **Magasin de données** dans la page d’accueil chargeur sur portail classique Azure 
- Sélectionnez **SQL** , entrez les informations d’identification *nom d’utilisateur* et *mot de passe* pour SQL Server localement. Vous devez entrer le nom du serveur comme un **nom d’instance nom du serveur barre oblique inversée (nomserveur\nominstance) complet**. Nommez service liées *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Service liée pour les objets Blob

Pour créer le service lié pour le compte de stockage d’objets Blob Azure :

- Cliquez sur le **Magasin de données** dans la page d’accueil chargeur sur portail classique Azure
- Sélectionnez le **Compte de stockage Azure** 
- Entrez le nom du compte Azure Blob Storage clé et conteneur. Nommez Service liées *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Service liée pour la base de données SQL Azure

Pour créer le service lié pour la base de données SQL Azure :

- Cliquez sur le **Magasin de données** dans la page d’accueil chargeur sur portail classique Azure
- Sélectionnez **SQL Azure** , entrez les informations d’identification *nom d’utilisateur* et *mot de passe* pour la base de données SQL Azure. *nom d’utilisateur* doit être spécifié en tant que *user@servername*.   


##<a name="adf-tables"></a>Définir et créer des tables pour spécifier comment accéder aux jeux de données

Créer des tables qui spécifient la structure, emplacement et la disponibilité des jeux de données avec des procédures suivantes en fonction de script. Fichiers JSON sont utilisés pour définir les tables. Pour plus d’informations sur la structure de ces fichiers, voir [jeux de données](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Vous devez exécuter le `Add-AzureAccount` applet de commande avant d’exécuter l’applet de commande [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) pour vérifier que l’abonnement droite Azure est activée pour l’exécution de commande. Pour la documentation de cette applet de commande, voir [Ajouter AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Les définitions basé sur JSON dans les tableaux utilisent les noms suivants :

* le **nom de la table** dans SQL server localement est *nyctaxi_data*
* le **nom du conteneur** dans le compte de stockage d’objets Blob Azure est *containername*  

Trois définitions de table sont nécessaires pour ce pipeline de définition d’application :

1. [Table locales SQL](#adf-table-onprem-sql)
2. [Table Blob](#adf-table-blob-store)
3. [SQL Azure Table](#adf-table-azure-sql)

> [AZURE.NOTE]  Ces procédures utilisent Azure PowerShell pour définir et créer les activités de définition d’application. Mais ces tâches peuvent également être effectuées à l’aide du portail Azure. Pour plus d’informations, voir [créer d’entrée et de sortie des jeux de données](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Table locales SQL

La définition de table pour le serveur SQL sur site spécifiée dans le fichier JSON suivant :

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Les noms de colonne n’étaient pas inclus ici. Vous pouvez sélectionner des noms de colonnes en les incluant ici (pour plus d’informations, vérifiez la rubrique [documentation chargeur](../data-factory/data-factory-data-movement-activities.md ) .

Copiez la définition JSON du tableau dans un fichier appelé *onpremtabledef.json* et enregistrez-le dans un emplacement connu (ici supposé égal à *C:\temp\onpremtabledef.json*). Créer une table dans la définition d’application avec l’applet de commande PowerShell Azure suivant :

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Table Blob
Définition de la table à l’emplacement d’objets blob de sortie est la suivante (cela mappe les données locales ingérées au blob Azure) :

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copiez la définition JSON du tableau dans un fichier appelé *bloboutputtabledef.json* et enregistrez-le dans un emplacement connu (ici supposé égal à *C:\temp\bloboutputtabledef.json*). Créer une table dans la définition d’application avec l’applet de commande PowerShell Azure suivant :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure Table
Définition de la table pour SQL Azure de sortie est la suivante (ce schéma mappe les données à partir du blob) :

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copiez la définition JSON du tableau dans un fichier appelé *AzureSqlTable.json* et enregistrez-le dans un emplacement connu (ici supposé égal à *C:\temp\AzureSqlTable.json*). Créer une table dans la définition d’application avec l’applet de commande PowerShell Azure suivant :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Définir et créer le pipeline

Spécifier les activités qui appartiennent au pipeline et créer le pipeline des procédures suivantes en fonction de script. Un fichier JSON est utilisé pour définir les propriétés du pipeline.

* Le script suppose que le **nom de pipeline** est *AMLDSProcessPipeline*.
* Notez également que nous la périodicité du pipeline d’être exécutées quotidiennement et utiliser le temps d’exécution par défaut pour la tâche (12 : 00 UTC).

> [AZURE.NOTE]Les procédures suivantes utilisent Azure PowerShell pour définir et créer le pipeline de définition d’application. Mais cette tâche peut également être réalisée à l’aide du portail Azure. Pour plus d’informations, voir [créer et exécuter un pipeline](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

En utilisant les définitions de table fournies précédemment, la définition du pipeline pour la définition d’application est spécifiée comme suit :

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copiez cette définition JSON du pipeline dans un fichier appelé *pipelinedef.json* et enregistrez-le dans un emplacement connu (ici supposé égal à *C:\temp\pipelinedef.json*). Créer le pipeline dans chargeur avec l’applet de commande PowerShell Azure suivant :

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Vérifiez que vous pouvez voir la canalisation sur la définition d’application dans le portail classique Azure afficher sous la forme suivante (lorsque vous cliquez sur le diagramme)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Démarrer le Pipeline
Le pipeline de peut désormais être exécuté à l’aide de la commande suivante :

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Les valeurs de paramètre *startdate* et *enddate* doivent être remplacés par les dates réelles entre lesquels vous souhaitez que le pipeline pour exécuter.

Une fois que le pipeline s’exécute, vous devez être en mesure de voir les données apparaissent-elles dans le conteneur pour l’objets blob, un seul fichier par jour.

Notez que nous avons réutilisé pas les fonctionnalités fournies par chargeur aux données de barre verticale par incréments. Pour plus d’informations sur la façon de faire cela et autres fonctionnalités fournies par la définition d’application, consultez la [documentation de la définition d’application](https://azure.microsoft.com/services/data-factory/).
