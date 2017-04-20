<properties 
    pageTitle="Comment configurer des points de terminaison apprentissage automatique Azure dans flux Analytique | Microsoft Azure" 
    description="Fonctions définies par l’utilisateur de langage machine dans flux Analytique"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"
/>

# <a name="machine-learning-integration-in-stream-analytics"></a>Intégration d’apprentissage dans Analytique de flux de l’ordinateur

Flux Analytique prend en charge les fonctions définies par l’utilisateur et faire appel à des points de terminaison apprentissage automatique Azure. Prise en charge de l’API REST de cette fonctionnalité est décrite dans la [bibliothèque de l’API REST flux Analytique](https://msdn.microsoft.com/library/azure/dn835031.aspx). Cet article fournit des informations supplémentaires nécessaires pour la mise en œuvre de cette fonctionnalité de flux de données Analytique. Un didacticiel a également été validé et n’est disponible [ici](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Vue d’ensemble : Terminologie d’apprentissage automatique Azure

Microsoft Azure Machine Learning fournit un outil de collaboration, faites glisser-déplacer que vous pouvez utiliser pour créer, tester et déployer des solutions analytique prédictive sur vos données. Cet outil s’appelle le *Azure Machine apprentissage Studio*. Le studio est utilisé pour interagir avec les ressources de formation Machine facilement créer, tester et modifions votre conception. Ces ressources et leurs définitions sont en dessous.

- **Espace de travail**: l' *espace de travail* est un conteneur qui contient toutes les autres ressources d’apprentissage automatique collaborer dans un conteneur de gestion et de contrôle.
- **Expérience**: *expériences* sont créés par scientifiques de données utilisent des jeux de données et la formation d’un modèle d’apprentissage automatique.
- **Point de terminaison**: *points de terminaison* sont l’objet d’apprentissage Machine Azure utilisée pour prendre des fonctionnalités comme entrée, appliquer un modèle d’apprentissage machine spécifiée et renvoyer des résultats évaluées.
- **ServiceWeb score**: un *score serviceWeb* est un ensemble de points de terminaison comme indiqué ci-dessus.

Chaque point de terminaison dispose d’API pour l’exécution du lot et l’exécution synchrone. Flux Analytique utilise l’exécution synchrone. Le service spécifique s’appelle un [Service de demande/réponse](../machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs) dans AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Apprentissage ressources nécessaires pour les tâches de flux de données Analytique de l’ordinateur

Aux fins de flux Analytique de traitement, un point de terminaison demande/réponse, un [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md#get-an-azure-machine-learning-authorization-key)et une définition swagger sont toutes les nécessaires pour réussir une exécution. Flux Analytique a un point de terminaison supplémentaire qui crée l’url de point de terminaison swagger, recherche l’interface et qui renvoie une définition UDF par défaut à l’utilisateur.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurer un flux Analytique et apprentissage UDF via l’API REST de l’ordinateur

En utilisant des API REST, vous pouvez configurer votre projet pour appeler des fonctions de langage Machine Azure. La procédure est la suivante :

1. Créer une tâche de flux de données Analytique
2. Définir une entrée
3. Définir une sortie
4. Créer une fonction définie par l’utilisateur (UDF)
5. Écrire une transformation flux Analytique qui appelle l’UDF
6. Démarrer le travail

## <a name="creating-a-udf-with-basic-properties"></a>Création d’un fichier UDF avec des propriétés de base

Par exemple, le code suivant crée une fonction scalaire nommée *newudf* qui est lié à un point de terminaison d’apprentissage automatique Azure. Notez que *point de terminaison* (service URI) vous pouvez trouver sur la page de l’aide de l’API pour le service choisi et *apiKey* sont accessibles dans la page principale Services.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corps de la requête exemple :  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Appelez le point de terminaison RetrieveDefaultDefinition pour par défaut UDF

Une fois la structure UDF créé la définition complète de la UDF est nécessaire. Le point de terminaison RetreiveDefaultDefinition vous permet d’accéder la définition par défaut pour une fonction scalaire qui est liée à un point de terminaison d’apprentissage automatique Azure. La charge utile ci-dessous, vous devez obtenir la définition de UDF par défaut pour une fonction scalaire qui est liée à un point de terminaison d’apprentissage automatique Azure. Il n’indique pas le point de terminaison réel comme il ont déjà été fournie au cours de la demande de place. Flux Analytique appelle le point de terminaison fournie dans la demande si elle est fourni de manière explicite. Sinon, elle utilise l’option référencé à l’origine. Ici prend la UDF une seule chaîne paramètre (une phrase) et retourne une seule représentation de type chaîne qui indique l’étiquette « opinion » pour cette phrase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corps de la requête exemple :  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un exemple de sortie de ce serait regardez quelque chose comme ci-dessous.  

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="patch-udf-with-the-response"></a>Correctif UDF avec la réponse 

À présent le fichier UDF doit être appliqué avec la réponse précédente, comme illustré ci-dessous.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corps de la requête (sortie de RetrieveDefaultDefinition) :

````
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Mise en œuvre de transformation de flux Analytique pour appeler le fichier UDF

Maintenant interroger le fichier UDF (ici nommé scoreTweet) pour tous les événements d’entrée et écrire une réponse pour cet événement dans le résultat.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
