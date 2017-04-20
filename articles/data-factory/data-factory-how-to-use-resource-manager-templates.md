<properties 
    pageTitle="Utiliser le Gestionnaire de ressources modèles dans Data Factory | Microsoft Azure" 
    description="Découvrez comment créer et utiliser le Gestionnaire de ressources Azure modèles pour créer des entités Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utiliser des modèles pour créer des entités Azure Data Factory

## <a name="overview"></a>Vue d’ensemble
Lors de l’utilisation d’Azure Data Factory à vos besoins d’intégration de données, vous pourrez être amené à réutiliser le même modèle travers différents environnements ou mise en œuvre de la même tâche répétitive dans la même solution. Modèles de vous aident à mettre en œuvre et gérer ces scénarios d’une manière facile. Modèles dans Azure Data Factory sont idéales pour les scénarios qui impliquent réutilisation et la périodicité.
 
Prenons le cas où une organisation a 10 installations de fabrication dans le monde. Les journaux de chaque plante sont stockés dans une base de données SQL Server distincte en local. La société souhaite créer un même data warehouse dans le nuage pour analytique ad hoc. Il souhaite également avoir le même logique mais des configurations différentes pour les environnements de développement, de test et de production. 

Dans ce cas, une tâche doit être répété dans le même environnement, mais avec des valeurs différentes sur les références de 10 données pour chaque usine de fabrication. En vigueur, **répétition** est présente. Création de modèles permet l’abstraction de ce flux générique (autrement dit, les pipelines ayant les mêmes activités dans chaque usine de données), mais utilise un fichier de paramètres distincts pour chaque usine de fabrication.

En outre, comme l’organisation souhaite déployer ces références 10 données plusieurs fois travers différents environnements, modèles peuvent utiliser cette **réutilisation** en utilisant des fichiers de paramètres distincts pour les environnements de développement, de test et de production.

## <a name="templating-with-azure-resource-manager"></a>Création de modèles avec le Gestionnaire de ressources Azure
[Gestionnaire de ressources Azure modèles](../azure-resource-manager/resource-group-overview.md#template-deployment) constituent un excellent moyen pour obtenir des modèles dans Azure Data Factory. Modèles de gestionnaire de ressources définissent l’infrastructure et la configuration de votre solution Azure via un fichier JSON. Parce que le Gestionnaire de ressources Azure modèles fonctionnent avec Azure/plus tous les services, il peut être utilisé largement pour gérer facilement toutes les ressources de vos ressources Azure. Voir [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md) pour en savoir plus sur les modèles de gestionnaire de ressources en général. 

## <a name="tutorials"></a>Didacticiels
Les didacticiels suivants pour obtenir des instructions étape par étape créer des entités Data Factory en utilisant le Gestionnaire de ressources des modèles, voir :

- [Didacticiel : Créer une opportunité pour copier les données à l’aide du Gestionnaire de ressources Azure modèle](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Didacticiel : Créer un pipeline pour traiter les données à l’aide du Gestionnaire de ressources Azure modèle](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modèles d’usine de données sur Github
Consultez les modèles de démarrage rapide Azure suivants sur Github : 

- [Créer une usine de données pour copier des données depuis le stockage Blob Azure à base de données SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Créer une usine de données avec une activité Hive sur cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Créer une usine de données pour copier des données depuis Salesforce vers des objets BLOB Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

N’hésitez pas à partager vos modèles Azure Data Factory au [démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/). Reportez-vous au [guide de contribution](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) lors du développement de modèles qui peuvent être partagés via ce référentiel. 

Les sections suivantes fournissent des détails sur la définition des ressources de données par défaut dans un modèle de gestionnaire de ressources. 

## <a name="defining-data-factory-resources-in-templates"></a>Définition des ressources de données par défaut dans les modèles
Le modèle de niveau supérieur de définir une usine de données est la suivante :

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Définir par défaut des données

Vous définissez une usine de données dans le modèle de gestionnaire de ressources comme le montre l’exemple suivant :

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

Le dataFactoryName est défini dans « variables » comme :

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Définir des services liées 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Voir [Service de stockage sur liées](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Calculer des Services liés](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour plus d’informations sur les propriétés de JSON pour le service liée spécifique que vous souhaitez déployer. Le paramètre « dependsOn » indique le nom de la factory de données correspondantes. Exemple de définition d’un service lié pour le stockage Azure est montrée dans la définition de JSON suivante :

### <a name="define-datasets"></a>Définir des groupes de données

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Pour plus d’informations sur les propriétés JSON pour le type de jeu de données spécifique que vous souhaitez déployer, voir [prise en charge de banques de données](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Remarque le paramètre « dependsOn » indique le nom du correspondant factory de données et du stockage lié service. Exemple de définition de type jeu de données de stockage d’objets blob Azure est montrée dans la définition de JSON suivante :

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Définir des pipelines

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Voir la rubrique [définition pipelines](data-factory-create-pipelines.md#pipeline-json) pour plus d’informations sur les propriétés de JSON pour définir les pipeline spécifique et les activités que vous souhaitez déployer. Remarque le paramètre « dependsOn » indique le nom de la factory de données, et tout correspondant lié services ou des groupes de données. Exemple d’un pipeline qui copie les données depuis le stockage Blob Azure à base de données SQL Azure est montrée dans l’extrait de JSON suivant :

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Paramétrage d’un modèle de données par défaut
Meilleures pratiques sur le paramétrage, voir l’article de [meilleures pratiques pour la création de modèles Azure le Gestionnaire de ressources](../resource-manager-template-best-practices.md#parameters) . En règle générale, l’utilisation du paramètre doit être réduite en particulier si variables peuvent être utilisées à la place. Fournir uniquement des paramètres dans les scénarios suivants :

- Paramètres varient selon l’environnement (exemple : développement, de test et de production)
- Secrets (par exemple, les mots de passe)

Si vous avez besoin extraire des secrets de [L’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md) lors du déploiement d’entités Azure Data Factory à l’aide de modèles, spécifiez le **nom secret** et **l’archivage sécurisé clé** comme le montre l’exemple suivant :

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Tandis que l’exportation de modèles pour les références de données existant n’est actuellement pas encore pris en charge, il est riches du programme works. 


