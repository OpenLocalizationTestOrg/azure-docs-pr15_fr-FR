<properties
   pageTitle="Gestion des exceptions logique applications | Microsoft Azure"
   description="Découvrez les modèles d’erreur et gestion des exceptions avec Azure logique d’applications"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Erreur applications logique et gestion des exceptions

Logique applications fournit une vaste gamme d’outils et des modèles pour garantir votre intégrations sont robuste et résistant contre les défaillances.  L’une des difficultés avec n’importe quelle architecture de l’intégration consiste à s’assurer que les temps d’arrêt ou problèmes de systèmes dépendants sont gérées correctement.  Logique applications permet de gérer les erreurs une expérience de première classe, vous donnant les outils que vous avez besoin pour agir sur les exceptions et les erreurs dans vos flux de travail.

## <a name="retry-policies"></a>Réessayer de stratégies

Le type d’exception et gestion des erreurs plus simple est une stratégie de réessayer.  Cette stratégie définit si l’action doit réessayer si demande initiale dépassé ou a échoué (toute demande qui a donné lieu à une 429 ou réponse 5xx).  Par défaut, toutes les actions réessayer 4 heures supplémentaires sur des intervalles de 20 secondes.  Par conséquent, si la première requête reçu une `500 Internal Server Error` réponse, le moteur de flux de travail s’arrête 20 secondes et essayez de la demande à nouveau.  Si après toutes les tentatives la réponse est toujours une exception ou une panne, le flux de travail est continuer et indiquer l’état de l’action en tant que `Failed`.

Vous pouvez configurer des stratégies de réessayer dans les **entrées** d’une action particulière.  Une stratégie de nouvelles tentatives peut être configurée pour essayer d’intervalles jusqu'à 4 heures sur 1 heure.  Plus d’informations sur les propriétés d’entrée peuvent être [trouvé sur MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Si vous voulez que votre action HTTP réessayer 4 heures et attendre 10 minutes entre chaque tentative que c’est la définition suivante :

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Pour plus d’informations sur la syntaxe pris en charge, consultez la [section stratégie de nouvelles tentatives sur MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propriété RunAfter pour intercepter des échecs

Cette action chaque application logique déclare les actions doivent terminer avant que l’action démarre.  Vous pouvez considérer ceci comme l’ordre des étapes dans votre flux de travail.  Ce type de classement est connu sous le nom du `runAfter` propriété dans la définition d’action.  Il s’agit d’un objet qui décrit les actions et statuts action aurait exécuté l’action.  Par défaut, toutes les actions ajoutées par le biais du concepteur sont définies sur `runAfter` l’étape précédente si l’étape précédente a été `Succeeded`.  Toutefois, vous pouvez personnaliser cette valeur pour déclencher des actions lorsque les actions précédentes sont `Failed`, `Skipped`, ou un jeu de valeurs possible.  Si vous souhaitez ajouter un élément à un sujet de Service Bus désigné après une action spécifique `Insert_Row` échoue, vous devez utiliser ce qui suit `runAfter` configuration :

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Notification de la `runAfter` propriété est définie à se déclenche si le `Insert_Row` action est `Failed`.  Pour exécuter l’action si le statut d’action est `Succeeded`, `Failed`, ou `Skipped` la syntaxe est :

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Actions qui s’exécutent après une action précédente a échoué et terminée, seront marquées comme `Succeeded`.  Ce comportement signifie que si vous correctement intercepter tous les échecs dans un flux de travail l’exécution elle-même est marquée comme `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Les étendues et les résultats pour évaluer les actions

Similaire à la façon dont vous pouvez exécuter après actions individuelles, vous pouvez également les actions de groupe entre eux à l’intérieur d’une [portée](app-service-logic-loops-and-scopes.md) - qui agissent comme un regroupement logique d’actions.  Les étendues sont utiles pour organiser vos actions d’application logique et d’effectuer des évaluations agrégation sur l’état de l’étendue.  L’étendue lui-même recevront un statut une fois toutes les actions dans une portée effectuées.  L’état de l’étendue est déterminé avec les mêmes critères comme une exécution--si la dernière action dans une branche d’exécution est `Failed` ou `Aborted` le statut est `Failed`.

Vous pouvez `runAfter` une étendue a été marquée `Failed` pour déclencher des actions spécifiques pour tout problème s’est produite dans l’étendue.  En cours d’exécution après l’échec d’une étendue permet de vous permet de créer une seule action pour intercepter échecs si *toutes* les actions dans le cadre échouent.

### <a name="getting-the-context-of-failures-with-results"></a>Obtenir le contexte d’échecs de résultats

Interception d’échecs à partir de l’étendue est très utile, mais vous pouvez également le contexte de comprendre exactement quelles actions a échoué et des erreurs ou des codes de statut qui ont été retournées.  La `@result()` fonction de flux de travail fournit contexte dans le résultat de toutes les actions dans une étendue.

`@result()`prend un seul paramètre, le nom de l’étendue et renvoie une matrice de tous les résultats d’action à partir de dans cette étendue.  Ces objets action incluent les mêmes attributs que la `@actions()` objet, y compris l’heure de début action, heure de fin action, état de l’action, entrées action, ID de corrélation action et action sorties.  Vous pouvez facilement associer un `@result()` fonctionne avec une `runAfter` pour envoyer le contexte de toutes les actions qui a échoué dans une étendue.

Si vous souhaitez exécuter une action *pour chaque* action d’une portée qui `Failed`, vous pouvez associer `@result()` avec une action de **[Filtre de tableau](../connectors/connectors-native-query.md)** et une boucle **[ForEach](app-service-logic-loops-and-scopes.md)** .  Permet de filtrer le tableau de résultats aux actions qui a échoué.  Vous pouvez prendre le tableau de résultats filtrés et exécuter une action pour chaque échec à l’aide de la boucle **ForEach** .  Voici un exemple ci-dessous, suivi d’une explication détaillée.  Cet exemple montre comment vous envoie une demande HTTP POST avec le corps de réponse de toutes les actions qui a échoué dans le cadre `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Voici une description détaillée des activités :

1. Action de **Filtre de tableau** pour filtrer le `@result('My_Scope')` pour obtenir le résultat de toutes les actions dans`My_Scope`
1. Condition du **Filtre de tableau** est une `@result()` élément dont l’état est égal à `Failed`.  Le tableau de tous les résultats de l’action de filtre `My_Scope` pour seulement un tableau de résultats de l’action qui a échoué.
1. Exécuter une action **pour chacune d’elles** sur sorties **Tableau filtrés** .  Cela effectue un action *pour chaque* échoué de résultat de l’action nous filtrés au-dessus.
    - S’il y a une action unique dans l’étendue qui a échoué, les actions contenues dans les `foreach` ne s’exécute une seule fois.  De nombreuses actions échecs entraînerait une action par erreur.
1. Envoyer une publication HTTP le `foreach` élément de corps de réponse, ou `@item()['outputs']['body']`.  La `@result()` élément forme est identique à la `@actions()` mettre en forme et peut être analysé de la même manière.
1. Également inclus deux en-têtes personnalisés avec le nom de l’action qui a échoué `@item()['name']` et exécutez l’échec de client ID de suivi `@item()['clientTrackingId']`.

Pour référence, Voici un exemple d’un seul `@result()` élément.  Vous pouvez voir la `name`, `body`, et `clientTrackingId` propriétés analysées dans l’exemple ci-dessus.  Il convient de noter qu’en dehors d’une `foreach`, `@result()` renvoie une matrice de ces objets.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Vous pouvez utiliser les expressions ci-dessus pour effectuer différentes exceptions de modèles.  Vous pouvez choisir d’exécuter une seule exception gestion action en dehors de l’étendue acceptant le tableau entier filtré de pannes et supprimer les `foreach`.  Vous pouvez également inclure d’autres propriétés utiles à partir de la `@result()` réponse ci-dessus.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnostics de Windows Azure et de télémétrie

Les modèles ci-dessus sont très utile pour gérer les erreurs et les exceptions à l’intérieur d’une série, mais vous pouvez également identifier et résoudre les erreurs indépendamment de l’exécution elle-même.  [Diagnostics Azure](app-service-logic-monitor-your-logic-apps.md) fournit un moyen simple d’envoyer tous les événements de flux de travail (y compris tous les statuts exécuter et une action) à un compte de stockage Azure ou un concentrateur événement Azure.  Vous pouvez surveiller les journaux et les mesures, ou les publier dans un outil d’analyse que vous préférez, doit être évaluée statuts exécuter.  Une option potentielle consiste à tous les événements via Azure événement concentrateur de flux dans [Flux Analytique](https://azure.microsoft.com/services/stream-analytics/).  Dans le flux de données Analytique vous pouvez écrire des requêtes live supprimer n’importe quel anomalies, des moyennes ou défaillances dans les journaux de diagnostic.  Flux Analytique pouvez facilement sortie vers d’autres sources de données, tels que files d’attente rubriques, SQL, DocumentDB et Power BI.

## <a name="next-steps"></a>Étapes suivantes
- [Voir comment un client créé avec logique applications de gestion d’erreur robuste](app-service-logic-scenario-error-and-exception-handling.md)
- [Rechercher d’autres exemples de logique applications et scénarios](app-service-logic-examples-and-scenarios.md)
- [Apprenez à créer des déploiements automatisés des applications de logique](app-service-logic-create-deploy-template.md)
- [Concevoir et déployer des applications logique de Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9