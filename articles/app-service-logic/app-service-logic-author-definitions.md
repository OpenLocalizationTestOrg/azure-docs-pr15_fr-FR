<properties 
    pageTitle="Créer des définitions de l’application logique | Microsoft Azure" 
    description="Apprenez à écrire la définition de JSON pour les applications de logique" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Définitions de logique application auteur
Cette rubrique montre comment utiliser les définitions de [Azure logique d’applications](app-service-logic-what-are-logic-apps.md) , qui est un langage JSON déclaratif simple. Si vous n’avez pas déjà fait, consultez tout d’abord [comment créer une nouvelle application logique](app-service-logic-create-a-logic-app.md) . Vous pouvez également lire le [matériel de référence complète du langage definition sur MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Plusieurs étapes qui se répètent sur une liste

Vous pouvez tirer parti du [type foreach](app-service-logic-loops-and-scopes.md) pour répéter les éléments sur un tableau de jusqu'à 10 Ko et effectuer une action pour chacune d’elles.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Une étape de gestion des erreurs si un problème survient

Vous voulez fréquemment être en mesure d’écrire une *étape de correction* — une certaine logique qui s’exécute, si **et seulement si**, un ou plusieurs de vos appels a échoué. Dans cet exemple, nous allons l’obtention de données à partir d’emplacements variés, mais cas d’échec de l’appel, je souhaite publier quelque part un message afin que je peux effectuer le suivi de cet échec plus loin :  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Vous pouvez apporter utilisation de la `runAfter` propriété pour spécifier le `postToErrorMessageQueue` doit s’exécuter uniquement après `readData` est **Échec**.  Il peut s’agir également une liste des valeurs possibles, donc `runAfter` peut être `["Succeeded", "Failed"]`.

Enfin, étant donné que vous avez maintenant géré l’erreur, nous marquer n’est plus exécuter comme **a échoué**. Comme vous pouvez le voir, cette exécution est **réussi** même si une étape a échoué, étant donné que j’ai écrit l’étape pour gérer cette erreur.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>Étapes de deux (ou plus) qui s’exécutent en parallèle

Pour que l’exécution du plusieurs actions en parallèle, la `runAfter` propriété doit être équivalente en cours d’exécution. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Comme vous pouvez le voir dans l’exemple ci-dessus, les deux `branch1` et `branch2` sont définis pour s’exécuter après `readData`. Par conséquent, les deux de ces branches seront exécuté en parallèle :

![Parallèle](./media/app-service-logic-author-definitions/parallel.png)

Vous pouvez constater que l’horodatage pour les deux branches est identique. 

## <a name="join-two-parallel-branches"></a>Joindre deux branches parallèles.

Vous pouvez joindre deux actions qui ont été définies pour exécuter en parallèle en ajoutant des éléments à la `runAfter` propriété similaire à ci-dessus.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Parallèle](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Mappage d’éléments dans une liste vers une autre configuration

Ensuite, supposons que nous voulons afficher le contenu selon une valeur d’une propriété complètement différent. Nous pouvons créer une carte de valeurs pour les destinations en tant que paramètre :  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

Dans ce cas, nous tout d’abord obtenir une liste des articles, et la deuxième étape recherche alors dans une carte, basée sur la catégorie qui a été définie comme un paramètre, l’URL pour obtenir le contenu à partir de. 

Deux éléments prêter attention ici : le [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) fonction est utilisée pour vérifier si la catégorie correspond à une des catégories connus définis. Ensuite, une fois que nous obtenir la catégorie, nous pouvons extraire l’élément de la carte à l’aide de crochets : `parameters[...]`. 

## <a name="working-with-strings"></a>Utilisation de chaînes

Il existe différentes fonctions qui peut être utilisé pour manipuler chaîne. Prenons un exemple dans lequel nous avons une chaîne que nous souhaitons passer à un système, mais nous n’avons pas sûr que le codage de caractère sera correctement géré. Une option consiste à en base 64 coder cette chaîne. Toutefois, afin d’éviter la séquence d’échappement dans une URL, nous allons maintenant pour remplacer des caractères. 

Nous voulons également une sous-chaîne de la l’ordre nom, car les 5 premiers caractères ne sont pas utilisées.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Utilisation de l’intérieur :

1. Obtenir le [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) du nom de l’auteur de la commande, cela renvoie le nombre total de caractères

2. Soustraire 5 (parce que nous souhaiterez une chaîne plus courte)

3. Prendre le [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Nous allons commencer à index `5` le reste de la chaîne de composition à emporter.

4. Convertir cette sous-chaîne à un [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) chaîne

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)toutes les `+` avec des caractères`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)toutes les `/` avec des caractères`_`

## <a name="working-with-date-times"></a>Utilisation des dates

Dates peut être utile, en particulier lorsque vous essayez d’extraire des données à partir d’une source de données qui ne prennent naturellement en charge **déclencheurs**.  Vous pouvez également utiliser des dates pour identifier le prenant combien différentes étapes. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

Dans cet exemple, nous allons extraire le `startTime` de l’étape précédente. Ensuite, nous sommes l’obtention de l’heure actuelle et en soustrayant une seconde :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (vous pouvez aussi utiliser d’autres unités de temps telles que `minutes` ou `hours`). Pour finir, nous pouvons comparer ces deux valeurs. Si la première est inférieure à la seconde, puis cela signifie que plus d’une seconde écoulé depuis l’ordre a été le premier placé. 

Notez également que nous pouvons utiliser formateurs chaîne pour mettre en forme de dates : dans la chaîne de requête utiliser [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) pour obtenir la RFC1123. Tous les date mise en forme [est décrite sur MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Utilisation des paramètres de temps de déploiement pour différents environnements

Il est courant d’avoir un cycle de vie de déploiement de l’endroit où vous disposez d’un environnement de développement, un environnement de test et un environnement de production. Dans tous ces éléments, vous pouvez souhaitez que la même définition, mais utiliser différentes bases de données, par exemple. De même, vous souhaiterez peut-être utiliser la même définition dans plusieurs régions différentes de disponibilité, mais chaque instance de l’application logique à parler à la base de données de cette région. 

Notez que cela est différent de prendre des paramètres différents au *moment de l’exécution*, que vous devez utiliser le `trigger()` fonctionne comme indiqué ci-dessus. 

Vous pouvez commencer par une définition très simple comme celui-ci :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Puis, dans le réel `PUT` demander pour l’application logique, vous pouvez fournir le paramètre `uri`. Notez qu’il n’est plus une valeur par défaut ce paramètre est requis dans la charge utile application logique :

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Dans chaque environnement, vous pouvez ensuite fournir une valeur différente pour la `connection` paramètre. 

Consultez la [documentation de l’API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) pour toutes les options dont vous disposez pour créer et gérer des applications logique. 
