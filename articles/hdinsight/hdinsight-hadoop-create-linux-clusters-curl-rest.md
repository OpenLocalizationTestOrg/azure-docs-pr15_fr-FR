<properties
    pageTitle="Créer des clusters Hadoop, HBase ou vague de sur Linux dans HDInsight à l’aide d’ourlée et l’API REST Azure | Microsoft Azure"
    description="Apprenez à créer des clusters basés sur Linux HDInsight à l’aide d’ourlée, modèles Azure le Gestionnaire de ressources et l’API REST Azure. Vous pouvez spécifier le type de cluster (Hadoop, HBase ou vague de), ou utiliser des scripts pour installer des composants personnalisés."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Créer des clusters basés sur Linux dans HDInsight à l’aide d’ourlée et l’API REST Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

L’API REST Azure vous permet d’effectuer des opérations de gestion des services hébergé dans la plateforme Azure, y compris la création de nouvelles ressources telles que les clusters basés sur Linux HDInsight. Dans ce document, vous allez apprendre à créer des modèles Azure le Gestionnaire de ressources pour configurer un cluster HDInsight et le stockage associé, puis utilisez ourlée à déployer le modèle de l’API REST Azure pour créer un nouveau cluster HDInsight.

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le nombre de nœuds de travail (4) par défaut pour un cluster HDInsight. Si vous prévoyez de plus de 32 nœuds de travail, lors de la création de cluster ou en redimensionnant le cluster après sa création, vous devez sélectionner une taille de nœud de tête au moins 8 cœurs et 14 Go de ram.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure infrastructure du langage commun__. L’infrastructure du langage commun Azure est utilisé pour créer un service principal, qui est ensuite utilisé pour générer des jetons d’authentification pour les demandes de l’API REST Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __coin__. Cet utilitaire est disponible via votre système de gestion du package, ou peut être téléchargé à partir de [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Si vous utilisez PowerShell pour exécuter les commandes dans ce document, vous devez d’abord supprimer la `curl` alias qu’il crée par défaut. Cet alias utilise appeler-WebRequest, une applet de commande PowerShell, à la place ourlée lorsque vous utilisez la `curl` commande à partir d’une invite PowerShell, puis renvoie les erreurs pour la plupart des commandes utilisées dans ce document.
    > 
    > Pour supprimer cet alias, utilisez ce qui suit à partir de l’invite PowerShell :
    >
    > `Remove-item alias:curl`
    >
    > Une fois que l’alias a été supprimée, vous devez être en mesure d’utiliser la version d’ourlée que vous avez installés sur votre système.

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Créer un modèle

Modèles de gestion des ressources Azure sont des documents JSON qui décrivent un __groupe de ressources__ et toutes les ressources qu’il contient (par exemple, HDInsight.) Ce modèle basé approche vous permet de définir toutes les ressources dont vous avez besoin pour HDInsight dans un modèle et de gestion des changements au groupe entier par le biais __des déploiements__ appliquer les modifications au groupe.

Modèles sont généralement fournis en deux parties ; le modèle lui-même et un fichier de paramètres qui vous remplissez avec les valeurs spécifiques à votre configuration. Pour exemple, le nom de cluster, nom d’administrateur et mot de passe. Lorsque vous utilisez directement l’API REST, vous devez combiner ces dans un seul fichier. Le format de ce document JSON est la suivante :

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Par exemple, voici une fusion des fichiers modèle et les paramètres de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), qui crée un cluster Linux à l’aide d’un mot de passe pour sécuriser le compte d’utilisateur SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Cet exemple montre comment est utilisé dans les étapes décrites dans ce document. Vous devez remplacer les _valeurs_ d’espace réservé dans la section __paramètres__ à la fin du document avec les valeurs que vous souhaitez utiliser pour votre cluster.

##<a name="login-to-your-azure-subscription"></a>Connexion à votre abonnement Azure

Suivez les étapes présentées dans [se connecter à un abonnement Azure à partir de l’Interface de ligne de Azure (commande Azure)](../xplat-cli-connect.md) et se connecter à votre abonnement à l’aide du `azure login` commande.

##<a name="create-a-service-principal"></a>Créer une entité de service

> [AZURE.NOTE] Ces étapes sont une version abrégée des informations fournies dans la section _service authentifier principal avec un mot de passe - Azure infrastructure du langage commun_ du document [authentifier un principal de service avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Ces étapes créent un nouveau service principal qui peut être utilisé pour authentifier les demandes de l’API REST utilisées pour créer des ressources Azure comme un cluster HDInsight.

1. À partir de l’invite de commandes, session Terminal Server ou shell, utilisez la commande suivante pour répertorier vos abonnements Azure.

        azure account list
        
    Dans la liste, sélectionnez l’abonnement auquel vous voulez utiliser et notez la colonne __Id__ . Ceci est l' __ID de l’abonnement__ et est utilisée dans la plupart des étapes décrites dans ce document.

2. Créer une nouvelle application dans Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Remplacez les valeurs de la `--name`, `--home-page`, et `--identifier-uris` avec vos propres valeurs. Fournir un mot de passe pour la nouvelle entrée Active Directory.
    
    > [AZURE.NOTE] Étant donné que vous créez cette application pour l’authentification via un principal de service, le `--home-page` et `--identifier-uris` valeurs n’êtes pas obligé de faire référence à une page web réel hébergée sur internet. ils doivent simplement être URI unique.
    
    Parmi les données renvoyées, enregistrez la valeur de __l’identificateur__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Création d’un service principal à l’aide de la valeur de __l’identificateur__ retourné précédemment.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Parmi les données renvoyées, enregistrez la valeur de __l’Id de l’objet__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Affecter le rôle de __propriétaire__ au service principal à l’aide de la valeur de __l’ID de l’objet__ retourné précédemment. Vous devez également utiliser l' __ID de l’abonnement__ que vous avez obtenu précédemment.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Une fois cette commande terminée, le service principal comporte maintenant un accès propriétaire à l’ID de l’abonnement spécifié.

##<a name="get-an-authentication-token"></a>Obtenir un jeton d’authentification

1. Utilisez ce qui suit pour rechercher l' __ID de client__ de votre abonnement.

        azure account show -s <subscription ID>
        
    Parmi les données renvoyées, rechercher l' __ID de client__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Générer un nouveau jeton à l’aide de l’API REST Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Remplacez les valeurs obtenues ou utilisées précédemment __TenantID__ __identificateur__et __mot de passe__ .

    Si cette demande a réussi, vous recevrez une réponse 200 série et le corps de réponse contiendra un document JSON.

    Le document JSON renvoyé par cette demande contiendra un élément nommé __access_token__; la valeur de cet élément est le jeton d’accès que vous devez utiliser pour l’authentification les requêtes utilisées dans les sections suivantes de ce document.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Créer un groupe de ressources

Utilisez ce qui suit pour créer un nouveau groupe de ressources. Vous devez créer tout d’abord le groupe avant de pouvoir créer les ressources telles que le cluster HDInsight. 

* Remplacez __SubscriptionID__ par l’ID de l’abonnement reçu lors de la création de l’entité de service.
* Remplacez __AccessToken__ par le jeton d’accès reçu à l’étape précédente.
* Remplacez __DataCenterLocation__ par le centre de données que vous souhaitez créer le groupe de ressources et les ressources, dans. Par exemple, « Sud centrale américaines ». 
* Remplacez __ResourceGroupName__ par le nom que vous souhaitez utiliser pour ce groupe :

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Si cette demande a réussi, vous recevrez une réponse 200 série et le corps de réponse contiendra un document JSON contenant des informations sur le groupe. La `"provisioningState"` élément contient une valeur de `"Succeeded"`.

##<a name="create-a-deployment"></a>Création d’un déploiement

Utiliser les éléments suivants pour déployer la configuration du cluster (valeurs modèle et des paramètres), au groupe de ressources.

* Remplacez __SubscriptionID__ et __AccessToken__ par les valeurs utilisées précédemment. 
* Remplacez __ResourceGroupName__ par le nom du groupe de ressources que vous avez créé dans la section précédente.
* Remplacez __DeploymentName__ par le nom que vous souhaitez utiliser pour ce déploiement.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Si vous avez enregistré le document JSON contenant le modèle et les paramètres dans un fichier, vous pouvez utiliser la suivante au lieu de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Si cette demande a réussi, vous recevrez une réponse 200 série et le corps de réponse contiendra un document JSON contenant des informations sur l’opération de déploiement.

> [AZURE.IMPORTANT] Notez que le déploiement a été envoyé, mais n’est pas terminée pour le moment. Il peut prendre plusieurs minutes, en général environ 15, pour le déploiement terminer.

##<a name="check-the-status-of-a-deployment"></a>Vérifier l’état d’un déploiement

Pour vérifier l’état du déploiement, procédez comme suit :

* Remplacez __SubscriptionID__ et __AccessToken__ par les valeurs utilisées précédemment. 
* Remplacez __ResourceGroupName__ par le nom du groupe de ressources que vous avez créé dans la section précédente.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Cela renvoie des informations un document JSON contenant des informations sur l’opération de déploiement. La `"provisioningState"` élément contient l’état du déploiement ; Si elle contient une valeur de `"Succeeded"`, puis le déploiement a réussi. À ce stade, votre cluster doit être disponible pour les utiliser.

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé un cluster de HDInsight, utilisez ce qui suit pour apprendre à utiliser avec votre cluster. 

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clusters HBase

* [Prise en main HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters vague

* [Développer des topologies Java pour vague d’HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utiliser des composants Python dans vague d’HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déployer et surveiller topologies avec vague d’HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
