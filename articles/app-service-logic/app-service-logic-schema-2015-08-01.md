<properties 
    pageTitle="Nouveau schéma version 2015-08-01-preview" 
    description="Apprenez à écrire la définition de JSON pour la dernière version d’applications logique" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Nouveau schéma version 2015-08-01-preview

Les nouvelles schéma et API version pour les applications logique comporte plusieurs améliorations qui améliorer la fiabilité et facilité d’utilisation des applications logique. Il existe 4 principales différences :

1. Le type d’action **APIApp** a été mis à jour pour un nouveau type d’action **APIConnection** .
2. **Répétez** a été renommé **Foreach**.
3. L’application de l’API **HTTP récepteur** n’est plus nécessaire.
4. Appel de flux de travail enfant utilise un nouveau schéma.

## <a name="1-moving-to-api-connections"></a>1. déplacement des connexions de l’API

La plus importante modification est que vous n’avez plus besoin déployer des applications de l’API dans votre abonnement Azure à utiliser l’API. Il existe 2 utilisations API :
* API managée
* Votre personnalisé Web API

Chacun d'entre eux est géré différemment, car leur gestion et les modèles d’hébergement sont différents. L’un des avantages de ce modèle sont que vous êtes n’est plus limité aux ressources déployées dans votre groupe de ressources. 

### <a name="managed-apis"></a>API managé

Il existe un certain nombre de API qui est gérés par Microsoft pour votre compte, tels que Office 365, Salesforce, Twitter, etc. FTP... Certaines de ces API managée peuvent être utilisé comme-est, telles que Bing traduire, tandis que d’autres requièrent configuration. Cette configuration est appelée une *connexion*.

Par exemple, lorsque vous utilisez Office 365, vous devez créer une connexion qui contient votre jeton de connexion à Office 365. Ce jeton est stocké en toute sécurité et actualisé afin que votre application logique puissiez appeler toujours l’API 365 Office. Par ailleurs, si vous voulez vous connecter à votre serveur SQL ou FTP, vous devez créer une connexion qui comporte la chaîne de connexion. 

À l’intérieur de la définition de ces actions sont appelées `APIConnection`. Voici un exemple d’une connexion qui appelle Office 365 pour envoyer un message électronique :

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

La partie des entrées unique pour les connexions de l’API est la `host` objet. Cet onglet contient deux parties : `api` et `connection`.

La `api` contient le runtime URL de l’emplacement que API managée est hébergé. Vous pouvez voir tous API managées disponibles pour vous en appelant `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Lorsque vous utilisez une API, il peut ou peut-être pas les **paramètres de connexion** définis. Le cas contraire, aucune **connexion** est requise. Si c’est le cas, vous devez créer une connexion. Lorsque vous créez cette connexion il comprend du nom que vous choisissez, et ensuite vous référencez que dans les `connection` objet à l’intérieur de la `host` objet. Pour créer une connexion dans un groupe de ressources, appelez :

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Avec le corps suivant :


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Déploiement d’API gérées dans un modèle de gestionnaire de ressources Azure

Vous pouvez créer une application complète dans un modèle de processeur tant qu’il ne nécessite pas de connexion interactive. Si elle requiert une connexion, vous pouvez définir tout avec le modèle de processeur, mais disposez toujours visitez le portail pour autoriser les connexions. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Vous pouvez voir dans cet exemple que les connexions sont simplement normales ressources qui résident dans votre groupe de ressources. Ils faire référence à la managedAPIs disponibles dans votre abonnement.

### <a name="your-custom-web-apis"></a>Votre API Web personnalisé

Si vous utilisez votre propre API (en particulier, pas gérés par Microsoft sont ceux qui), vous devez utiliser l’action **HTTP** intégrée les appeler. Pour disposer d’une expérience idéale, vous devez exposer un point de terminaison swagger correspondant à votre API. Vous activez le Concepteur d’application logique pour le rendu des entrées et des sorties correspondant à votre API. Sans un swagger, le concepteur uniquement seront en mesure d’afficher les entrées et sorties en tant qu’objets JSON opaques.

Voici un exemple montrant les nouveaux `metadata.apiDefinitionUrl` propriété :
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Si vous hébergez vos API Web **Application** service puis il automatiquement apparaît dans la liste des actions disponibles dans le concepteur. Dans le cas contraire, vous devrez coller directement dans l’URL. Le point de terminaison swagger doit être non authentifié pour être utilisable dans le Concepteur d’applications logique (même si vous pouvez sécuriser l’API lui-même avec toutes les méthodes sont prises en charge dans le Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>À l’aide de vos applications API déjà déployées avec 2015-08-01-preview

Si vous avez déjà déployé une application API, vous pouvez l’appel via l’action **HTTP** .

Par exemple, si vous utilisez Dropbox pour répertorier les fichiers, vous devrez comme suit dans la définition de votre version **2014-12-01-aperçu** schéma :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Vous pouvez construire l’action HTTP équivalente comme ci-dessous (la section Paramètres du reste de la définition d’application logique inchangé) :

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Étude de ces propriétés un par un :

| Propriété action |  Description |
| --------------- | -----------  |
| `type` | `Http`Au lieu de`APIapp` |
| `metadata.apiDefinitionUrl` | Si vous voulez utiliser cette action dans le Concepteur d’applications logique, vous souhaiterez inclure le point de terminaison de métadonnées. Ceci est construite à partir de :`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Ceci est construite à partir de :`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Toujours`POST` |
| `inputs.body` | Identiques aux paramètres de l’application api | 
| `inputs.authentication` | Identique à l’authentification de l’application api |

Cette approche doit utiliser pour toutes les actions d’application API. Toutefois, gardez à l’esprit que ces applications API précédentes ne sont plus pris en charge, et vous devez déplacer vers une des deux autres options ci-dessus (une API managée ou qui héberge votre API Web personnalisé).

## <a name="2-repeat-renamed-to-foreach"></a>2. répéter renommé Foreach

Pour la version précédente de schéma nous avons reçu un grand nombre de commentaires des clients qui **Répétez** n’était pas clair et n’a pas été correctement capturer que c’est vraiment une boucle for each. Par conséquent, nous avons renommé ensuite **Foreach**. Par exemple :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Feriez désormais être écrits en :

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

La fonction précédemment `@repeatItem()` a été utilisé pour référencer l’élément actif itéré. Cela a été simplifié pour juste `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Référencer des sorties de Foreach
Pour simplifier davantage, sorties d’actions **Foreach** ne seront pas encapsulés dans un objet appelé **repeatItems**. Cela signifie que les résultats de la répétition ci-dessus ont été :

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

À présent, il sera :

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Lorsque vous référencez ces sorties, pour accéder au corps de l’action vous devez faire :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Vous pouvez désormais effectuer à la place :

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Avec ces modifications, les fonctions `@repeatItem()`, `@repeatBody()` et `@repeatOutputs()` sont supprimés.

## <a name="3-native-http-listener"></a>3. native récepteur HTTP 
Les fonctionnalités HTTP récepteur sont maintenant intégrées, afin que vous n’avez plus besoin déployer une application API du récepteur HTTP. En savoir plus sur [les détails complets comment rendre votre point de terminaison logique application pouvant être appelé par ici](app-service-logic-http-endpoint.md). 

Avec ces modifications, la fonction `@accessKeys()` est supprimée et a été remplacé par le `@listCallbackURL()` fonction aux fins d’obtenir le point de terminaison (le cas échéant). En outre, vous maintenant devez définir au moins un déclencheur dans votre application logique maintenant. Si vous voulez `/run` le flux de travail, vous devez avoir l’un d’un `manual`, `apiConnectionWebhook` ou `httpWebhook` déclencheurs. 

## <a name="4-calling-child-workflows"></a>4. flux de travail enfant vous appelez

Précédemment, appel de flux de travail enfant requis accédant à ce flux de travail et obtenir le jeton d’accès puis le collage que dans la définition de l’application logique que vous souhaitez appeler que les enfants. Avec la nouvelle version de schéma, le moteur d’applications logique génère automatiquement une associations de sécurité lors de l’exécution du flux de travail enfant, ce qui signifie que vous n’avez pas à coller aucun secret dans la définition.  Voici un exemple :

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Un deuxième d’amélioration du produit est que nous sera être donnant le flux de travail enfant un accès total à la demande entrante. Cela signifie que vous pouvez passer des paramètres dans la section *requêtes* et dans l’objet *en-têtes* et que vous pouvez définir entièrement la totalité du corps.

Enfin, il existe des modifications requises pour le flux de travail enfant. Alors qu’avant que vous pouvez simplement appeler un flux de travail enfant directement ; à présent, vous devez définir un point de terminaison déclencheur dans le flux de travail pour le parent à appeler. En règle générale, cela signifie que vous devez ajouter un déclencheur de type **manuel** , puis utilisez que dans la définition parente. Notez que la `host` propriété spécifiquement a un `triggerName`, car vous devez toujours spécifier le déclencheur vous appelez.

## <a name="other-changes"></a>Autres modifications

### <a name="new-queries-property"></a>Nouvelle propriété de requêtes
Tous les types d’actions prennent désormais en charge une nouvelle entrée appelée **requêtes**. Il peut s’agir d’un objet structuré plutôt que vous ayez à assembler manuellement la chaîne.

### <a name="parse-function-renamed"></a>fonction Parse() renommée
Comme nous ajouterons plus rapidement les types de contenu supplémentaires, la `parse()` fonction a été renommée `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Bientôt disponible : API de l’intégration d’entreprise
À ce stade, nous ne pas encore avoir géré versions des API intégration Enterprise disponible (par exemple, AS2). Il seront bientôt comme décrit dans [feuille de route](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Pendant ce temps, vous pouvez utiliser votre APIs BizTalk déployé existantes via l’action HTTP, comme indiqué ci-dessus en » à l’aide de vos applications API déjà déployées. »
