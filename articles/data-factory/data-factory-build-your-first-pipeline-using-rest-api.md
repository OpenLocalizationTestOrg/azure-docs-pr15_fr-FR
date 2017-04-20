<properties
    pageTitle="Créer votre première usine de données (REST) | Microsoft Azure"
    description="Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide de l’API REST de données par défaut."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Didacticiel : Créer votre première usine données Azure à l’aide de l’API REST de données par défaut
> [AZURE.SELECTOR]
- [Vue d’ensemble et conditions préalables](data-factory-build-your-first-pipeline.md)
- [Portail Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modèle de gestionnaire de ressources](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Dans cet article, vous utilisez l’API REST de données par défaut pour créer votre première usine données Azure.

## <a name="prerequisites"></a>Conditions préalables
- Lire l’article [Vue d’ensemble du didacticiel](data-factory-build-your-first-pipeline.md) et suivez les étapes de **composant requis** .
- Installer [coin](https://curl.haxx.se/dlwiz/) sur votre ordinateur. L’outil OURLÉE avec des commandes reste vous permet de créer une usine de données. 
- Suivez les instructions [cet article](../resource-group-create-service-principal-portal.md) pour : 
    1. Créer une application Web nommée **ADFGetStartedApp** dans Azure Active Directory.
    2. Obtenir les **ID de client** et **clé secrète**. 
    3. Obtenir **l’ID de client**. 
    4. Attribuer le rôle de **Collaborateur d’usine de données** de l’application **ADFGetStartedApp** .  
- Installez [PowerShell Azure](../powershell-install-configure.md).  
- Lancez **PowerShell** et exécutez la commande suivante. Laisser Azure PowerShell ouverte jusqu'à la fin de ce didacticiel. Si vous fermez et rouvrez, vous devez exécuter à nouveau les commandes.
    1. Exécutez **AzureRmAccount de connexion** et entrez le nom d’utilisateur et mot de passe que vous utilisez pour vous connecter au portail Azure.  
    2. Exécutez **Get-AzureRmSubscription** pour afficher tous les abonnements pour ce compte.
    3. Exécuter **Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription | Jeu de AzureRmContext** pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure. 
3. Créer un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans la session PowerShell :  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Parmi les étapes décrites dans ce didacticiel part du principe que vous utilisez le groupe de ressources nommé ADFTutorialResourceGroup. Si vous utilisez un groupe de ressources différent, vous devez utiliser le nom de votre groupe de ressources à la place ADFTutorialResourceGroup dans ce didacticiel.

## <a name="create-json-definitions"></a>Créer des définitions de JSON
Créer des fichiers JSON dans le dossier où se trouve curl.exe suivants. 

### <a name="datafactoryjson"></a>DataFactory.JSON 
> [AZURE.IMPORTANT] Nom doit être globalement unique, il est conseillé de préfixe/suffixe ADFCopyTutorialDF pour lui attribuer un nom unique. 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.JSON
> [AZURE.IMPORTANT] Remplacez le **nom de compte** et **accountkey** par nom et la clé de votre compte de stockage Azure. Pour savoir comment obtenir votre clé d’accès de stockage, voir [Afficher, copier et stockage régénérer les touches d’accès](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

Le tableau suivant décrit les propriétés JSON utilisées dans l’extrait de code :

| Propriété | Description |
| :------- | :---------- |
| Version | Spécifie que la version de la HDInsight créé pour être 3,2. | 
| ClusterSize | Taille du cluster HDInsight. | 
| Propriété TimeToLive | Spécifie que la durée d’inactivité pour le cluster HDInsight, avant d’être supprimé. |
| linkedServiceName | Spécifie le compte de stockage utilisé pour stocker les journaux qui sont générés par HDInsight |

Notez les points suivants : 

- Le Factory de données crée un cluster HDInsight **fonctionnant sous Windows** pour vous avec la JSON ci-dessus. Vous pourriez également lui demander de créer un cluster HDInsight **Linux** . Pour plus d’informations, consultez [Services liés à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Vous pouvez aussi utiliser **votre propre cluster HDInsight** au lieu d’utiliser un cluster de HDInsight à la demande. Pour plus d’informations, voir [Service liées HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- Le cluster HDInsight crée un **conteneur par défaut** dans le stockage blob que vous spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est voulu par la conception. Avec le service de HDInsight lié à la demande, un HDInsight cluster est créé chaque fois qu’un secteur est traité sauf s’il existe une existante live cluster (**timeToLive**) et est supprimé lorsque le traitement est terminé.

    Lorsque plusieurs sections sont traitées, vous voyez de nombreux conteneurs dans votre espace de stockage blob Azure. Si vous ne devez pas les de résoudre les problèmes des tâches, vous souhaiterez peut-être les supprimer pour réduire les coûts de stockage. Les noms de ces conteneurs suivent un modèle : « chargeur**yourdatafactoryname**-**linkedservicename**- datetimestamp ». Pour supprimer conteneurs dans votre espace de stockage blob Azure, utilisez les outils tels que [Microsoft stockage Explorer](http://storageexplorer.com/) .

Pour plus d’informations, consultez [Services liés à la demande HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 

### <a name="inputdatasetjson"></a>inputdataset.JSON

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


Le JSON définit un jeu de données nommée **AzureBlobInput**, qui représente les données d’entrée pour une activité dans le pipeline. En outre, elle indique que les données d’entrée sont trouve dans le conteneur blob appelé **adfgetstarted** et le dossier nommé **inputdata**.

Le tableau suivant décrit les propriétés JSON utilisées dans l’extrait de code :

| Propriété | Description |
| :------- | :---------- |
| type | La propriété est définie sur AzureBlob, car les données se trouvent dans le stockage blob Azure. |  
| linkedServiceName | fait référence à la StorageLinkedService que vous avez créée. |
| nom de fichier | Cette propriété est facultative. Si vous omettez cette propriété, tous les fichiers à partir de la folderPath sont sélectionnées. Dans ce cas, uniquement l’input.log est traité. |
| type | Les fichiers journaux sont disponibles au format texte, afin que nous utilisons format du texte. | 
| columnDelimiter | colonnes dans les fichiers journaux sont délimités par une virgule () |
| fréquence/intervalle | fréquence définie mois et l’intervalle est 1, ce qui signifie que les secteurs d’entrée sont disponibles tous les mois. | 
| externes | Cette propriété est définie sur true si les données d’entrée ne sont pas générées par le service de données par défaut. | 

### <a name="outputdatasetjson"></a>outputdataset.JSON

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

Le JSON définit un jeu de données nommée **AzureBlobOutput**, qui représente des données de sortie d’une activité dans le pipeline. En outre, elle indique que les résultats sont stockés dans le conteneur blob appelé **adfgetstarted** et le dossier nommé **partitioneddata**. La section **disponibilité** indique que le jeu de données de sortie est produite sur une base mensuelle.

### <a name="pipelinejson"></a>pipeline.JSON
> [AZURE.IMPORTANT] Remplacez **storageaccountname** par le nom de votre compte de stockage Azure. 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

Dans l’extrait JSON, vous créez un pipeline qui se compose d’une activité unique qui utilise Hive pour traiter les données sur un cluster HDInsight.

Le fichier de script Hive, **partitionweblogs.hql**, est stocké dans le compte de stockage Azure (spécifié par scriptLinkedService, appelé **StorageLinkedService**) et dans le dossier de **scripts** dans conteneur **adfgetstarted**.

La section **définit** spécifie les paramètres runtime qui sont passés au script hive comme valeurs de configuration Hive (par ex. ${hiveconf : inputtable}, ${hiveconf:partitionedtable}).

Les propriétés **start** et **end** du pipeline spécifie la période active du pipeline.

Dans l’activité JSON, vous indiquez que le script Hive s’exécute sur le cluster spécifié par le **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Pour plus d’informations sur les propriétés JSON utilisées dans l’exemple précédent, consultez la [structure d’un Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 

## <a name="set-global-variables"></a>Définir des variables globales

Dans Azure PowerShell, exécutez les commandes suivantes après avoir remplacé les valeurs par votre propre :

> [AZURE.IMPORTANT] Consultez la section [conditions préalables](#prerequisites) pour obtenir des instructions sur l’obtention d’ID de client, secret client, client ID et ID d’abonnement.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>Vous authentifier avec AAD

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>Créer factory de données

Dans cette étape, vous créez une usine de données Azure nommé **FirstDataFactoryREST**. Une usine de données peut avoir un ou plusieurs pipelines. Une opportunité peut avoir une ou plusieurs activités qu’il contient. Par exemple, une activité copier pour copier les données d’une source dans un magasin de données de destination et une activité HDInsight Hive à exécuter un script Hive pour transformer les données. Exécutez les commandes suivantes pour créer le factory de données : 

1. Affecter la commande variable nommée **cmd**. 

    Vérifiez que le nom de la factory de données que vous spécifiez ici (ADFCopyTutorialDF) correspond au nom spécifié dans le **datafactory.json**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.

        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le factory de données a été créé, vous voyez le JSON pour le factory de données dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.  

        Write-Host $results

Notez les points suivants :
 
- Le nom de la Factory de données Azure doit être globalement unique. Si vous voyez l’erreur dans les résultats de : **nom d’usine données « FirstDataFactoryREST » n’est pas disponible**, procédez comme suit :  
    1. Modifier le nom (par exemple, yournameFirstDataFactoryREST) dans le fichier **datafactory.json** . Consultez la rubrique de [Données Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’appellation pour les objets de données par défaut.
    2. Dans la première commande où la variable **$cmd** est affectée à une valeur, remplacez FirstDataFactoryREST par le nouveau nom et exécutez la commande. 
    3. Exécutez les deux commandes d’appeler l’API REST pour créer le factory de données et imprimer les résultats de l’opération. 
- Pour créer des instances de données par défaut, vous devez être un collaborateur/administrateur de l’abonnement Azure
- Le nom de la factory de données peut être enregistré comme un nom DNS à l’avenir et donc deviennent visible publiquement.
- Si vous recevez l’erreur : «**cet abonnement n’est pas enregistré pour utiliser l’espace de noms Microsoft.DataFactory**», effectuez l’une des opérations suivantes et essayez de republier : 

    - Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur de données par défaut : 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Vous pouvez exécuter la commande suivante pour vous assurer que le Factory de données fournisseur est inscrit : 
    
            Get-AzureRmResourceProvider
    - Connexion à l’aide de l’abonnement au [portail Azure](https://portal.azure.com) Azure et accédez à une carte de données par défaut (ou) créer une usine de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur pour vous.

Avant de créer un pipeline, vous devez tout d’abord créer quelques entités Data Factory. Vous créez tout d’abord services liés pour lier des données stores/calcule à votre magasin de données, de définir d’entrée et de sortie des jeux de données pour représenter les données dans des magasins de données liées. 

## <a name="create-linked-services"></a>Créer des services liées 
Dans cette étape, vous liez votre compte de stockage Azure et un cluster Azure HDInsight à la demande à votre usine de données. Le compte de stockage Azure conserve les données d’entrée et de sortie pour le pipeline dans cet exemple. Le service HDInsight lié est utilisé pour exécuter un script Hive spécifié dans l’activité du pipeline dans cet exemple. 

### <a name="create-azure-storage-linked-service"></a>Créer le service de stockage Azure lié
Dans cette étape, vous liez votre compte de stockage Azure à votre usine de données. Avec ce didacticiel, vous utilisez le même compte de stockage Azure pour stocker les données d’entrée/sortie et le fichier de script HQL.

1. Affecter la commande variable nommée **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le service lié a été créé avec succès, vous voyez le JSON pour le service lié dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>Créer le service Azure HDInsight lié
Dans cette étape, vous liez un cluster de HDInsight à la demande à votre usine de données. Le cluster HDInsight est automatiquement créé lors de l’exécution et supprimé une fois que procéder transformation et inactif pour le laps de temps spécifié. Vous pouvez aussi utiliser votre propre cluster HDInsight au lieu d’utiliser un cluster de HDInsight à la demande. Pour plus d’informations, voir [Calculer les Services liés](data-factory-compute-linked-services.md) .  

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.

        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le service lié a été créé avec succès, vous voyez le JSON pour le service lié dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.  

        Write-Host $results

## <a name="create-datasets"></a>Créer des groupes de données
Dans cette étape, vous créez des groupes de données pour représenter l’entrée et sortie des données pour le traitement Hive. Ces jeux de données font référence à la **StorageLinkedService** que vous avez créé précédemment dans ce didacticiel. Les points de service liées à un compte de stockage Azure et des datasets spécifient conteneur, dossier, nom de fichier dans le stockage qui contient d’entrée et sortie des données.   

### <a name="create-input-dataset"></a>Créer le jeu de données d’entrée
Dans cette étape, vous créez le jeu de données d’entrée pour représenter des données d’entrée stockées dans le stockage Blob Azure.

1. Affecter la commande variable nommée **cmd**. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.

        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le jeu de données a été créé, vous voyez le JSON pour le jeu de données dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.
  
        Write-Host $results
### <a name="create-output-dataset"></a>Créer le jeu de données de sortie
Dans cette étape, vous créez le jeu de données de sortie pour représenter des données de sortie stockées dans le stockage Blob Azure.

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.

        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le jeu de données a été créé, vous voyez le JSON pour le jeu de données dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.
  
        Write-Host $results 

## <a name="create-pipeline"></a>Créer des opportunités
Dans cette étape, vous créez votre premier pipeline avec une activité **HDInsightHive** . Secteur d’entrée n’est disponible chaque mois (fréquence : mois, intervalle : 1), secteur de sortie est généré tous les mois et la propriété du Planificateur de l’activité est également définie sur mensuel. Les paramètres pour le jeu de données de sortie et le planificateur d’activité doivent correspondre. Jeu de données de sortie n’existe actuellement que lecteurs l’Échéancier, vous devez créer un jeu de données de sortie même si l’activité ne génère pas de sortie. Si l’activité n’accepte aucune entrée, vous pouvez ignorer la création du groupe de données d’entrée.  

Vérifiez que vous consultez le fichier **input.log** dans le dossier **adfgetstarted/inputdata** dans le stockage blob Azure et exécutez la commande suivante pour déployer le pipeline. Étant donné que les heures de **début** et **fin** sont définies par le passé et **isPaused** est définie sur false, le pipeline (activité dans le pipeline) s’exécute immédiatement après le déploiement. 

1. Affecter la commande variable nommée **cmd**.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Exécutez la commande à l’aide de **Commande de l’appel**.

        $results = Invoke-Command -scriptblock $cmd;
3. Afficher les résultats. Si le jeu de données a été créé, vous voyez le JSON pour le jeu de données dans les **résultats**; dans le cas contraire, vous recevez un message d’erreur.  

        Write-Host $results
5. Félicitations, vous avez créé votre première pipeline à l’aide de PowerShell Azure.

## <a name="monitor-pipeline"></a>Pipeline de moniteur
Dans cette étape, vous utilisez l’API REST de données par défaut pour surveiller secteurs produits par le pipeline.

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> Création d’un cluster de HDInsight à la demande généralement prend un certain temps (environ 20 minutes). Par conséquent, ils doivent s’attendre le pipeline de prendre **30 minutes** pour traiter le secteur.  

Exécutez la commande appeler et celle qui suit jusqu'à ce que vous voyiez le secteur dans l’état **prêt** ou **a échoué** . Lorsque le secteur est prête, consultez le dossier **partitioneddata** dans le conteneur **adfgetstarted** dans votre espace de stockage blob pour les données de sortie.  La création d’un cluster de HDInsight à la demande prend généralement un certain temps.

![données de sortie](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] Le fichier d’entrée est supprimé lorsque le secteur est traité avec succès. Par conséquent, si vous souhaitez réexécuter le secteur ou effectuez une nouvelle fois le didacticiel, téléchargez le fichier d’entrée (input.log) dans le dossier inputdata du conteneur adfgetstarted.

Vous pouvez également utiliser portail Azure surveillance secteurs et résoudre les éventuels problèmes. Afficher des détails de [surveiller les pipelines à l’aide du portail Azure](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline) .  

## <a name="summary"></a>Résumé 
Dans ce didacticiel, vous avez créé une usine de données Azure pour traiter les données en exécutant le script Hive sur un cluster hadoop HDInsight. Vous avez utilisé l’éditeur par défaut des données dans le portail Azure pour effectuer les opérations suivantes :  

1.  Créer une **usine de données**Azure.
2.  Créé deux **services liées**:
    1.  Service de **Stockage azure** lié à lier votre stockage d’objets blob Azure qui contient les fichiers d’entrée/sortie à l’usine de données.
    2.  Service de liées à la demande **Azure HDInsight** à lier un cluster HDInsight Hadoop à la demande à l’usine de données. Azure Data Factory crée un HDInsight Hadoop cluster juste-à-temps pour traiter les données d’entrée et produire des données de sortie. 
3.  Créé deux **jeux de données**, ce qui décrivent les données d’entrée et de sortie pour l’activité HDInsight Hive dans le pipeline. 
4.  Créé un **pipeline** avec une activité **HDInsight Hive** . 

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un pipeline avec une transformation activité (HDInsight) qui exécute un script Hive sur un cluster Azure HDInsight à la demande. Pour découvrir comment utiliser une activité copier pour copier des données à partir d’un Blob Azure dans SQL Azure, consultez [didacticiel : copier des données à partir d’un Blob Azure dans SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
| :---- | :---- |
| [Référence des API REST données usine](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  Consultez la documentation complète sur les applets de commande Data Factory |
| [Activités de Transformation des données](data-factory-data-transformation-activities.md) | Cet article fournit la liste des activités de transformation de données (par exemple, de transformation HDInsight Hive vous avez utilisées dans ce didacticiel) pris en charge par Azure Data Factory. |
| [Planification et l’exécution](data-factory-scheduling-and-execution.md) | Cet article décrit les aspects de la planification et l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Cet article vous aide à comprendre les pipelines et activités dans Azure Data Factory et comment les utiliser pour créer de bout en bout pilotées par les données des flux de travail pour votre scénario ou entreprise. |
| [Jeux de données](data-factory-create-datasets.md) | Cet article vous aide à comprendre les groupes de données dans Azure Data Factory.
| [Surveiller et gérer les Pipelines à l’aide de cartes portails Azure](data-factory-monitor-manage-pipelines.md) | Cet article décrit comment contrôler, gérer et déboguer vos pipelines à l’aide de cartes portails Azure. |
| [Surveiller et gérer les pipelines à l’aide d’application de surveillance](data-factory-monitor-manage-app.md) | Cet article décrit comment contrôler, gérer et déboguer pipelines à l’aide de la surveillance et l’application de gestion. 

