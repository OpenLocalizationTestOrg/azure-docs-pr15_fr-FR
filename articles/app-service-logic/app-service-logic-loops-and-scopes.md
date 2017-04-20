<properties
   pageTitle="Applications logique boucles, les étendues et Debatching | Microsoft Azure"
   description="Retour en boucle a application logique, la portée et debatching concepts"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Applications logique boucles, les étendues et Debatching
  
>[AZURE.NOTE] Cette version de l’article s’applique au schéma logique applications 2016-04-01-aperçu et versions ultérieures.  Concepts sont similaires pour les schémas antérieurs, mais les étendues sont uniquement disponibles pour cette schéma et versions ultérieures.
  
## <a name="foreach-loop-and-arrays"></a>Boucle ForEach et les matrices
  
Logique applications vous permet de boucle sur un jeu de données et effectuer une action pour chaque élément.  Cela est possible via la `foreach` action.  Dans le concepteur, vous pouvez spécifier pour ajouter un pour chaque boucle.  Après avoir sélectionné le tableau que vous souhaitez parcourir, vous pouvez commencer à ajouter des actions.  Vous êtes actuellement limité à une seule action par boucle foreach, mais cette limitation sera levée dans les prochaines semaines.  Une fois dans la boucle, vous pouvez commencer à spécifier que doit avoir lieu à chaque valeur du tableau.

Si vous utilisez le mode code, vous pouvez spécifier une boucle for each comme ci-dessous.  Il s’agit d’un exemple d’une boucle for each qui envoie un message électronique pour chaque adresse de messagerie qui contient « microsoft.com » :

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` action peut parcourir sur tableaux jusqu'à 5 000 lignes.  Chaque itération peut exécuter en parallèle, il peut être nécessaire d’ajouter des messages à une file d’attente si vous avez besoin de contrôle de flux.
  
## <a name="until-loop"></a>Jusqu'à boucle
  
  Vous pouvez effectuer une action ou une série d’actions jusqu'à ce qu’une condition est remplie.  Scénario le plus courant pour ce appelle un point de terminaison jusqu'à ce que vous obteniez la réponse que vous recherchez.  Dans le concepteur, vous pouvez spécifier pour ajouter un jusqu'à boucle.  Après avoir ajouté des actions à l’intérieur de la boucle, vous pouvez définir la condition de sortie, ainsi que les échanges limites.  Il y a un délai d’une minute entre les cycles de boucle.
  
  Si vous utilisez le mode code, vous pouvez spécifier une jusqu'à ce que boucle comme ci-dessous.  Il s’agit d’un exemple de l’appel à un point de terminaison HTTP jusqu'à ce que le corps de réponse a la valeur « Terminés ».  Il s’achève lorsque soit 
  
  * Réponse HTTP a l’état « Terminé »
  * Il a essayé pour une heure
  * Il est lu en boucle 100 fois
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn et Debatching

Parfois un déclencheur peut s’afficher un tableau des éléments que vous voulez debatch et démarrer un flux de travail par élément.  Ceci peut être accompli la `spliton` commande.  Par défaut, si votre swagger déclencheur spécifie une charge utile qui est un tableau, un `spliton` sera ajouté et lancer un par élément.  SplitOn peut uniquement être ajoutée à un déclencheur.  Cela peut être manuellement configuré ou substitution en mode de code définition.  Actuellement SplitOn pouvez debatch matrices jusqu'à 5 000 éléments.  Vous ne peut pas contenir un `spliton` et également implémenter le modèle de réponse synchrone.  Les flux de travail appelé qui comporte une `response` action en plus de `spliton` exécutera de façon asynchrone et envoyer une immédiate `202 Accepted` réponse.  

SplitOn peut être spécifié dans le mode code comme l’exemple suivant.  Cette reçoit un tableau des éléments et debatches sur chaque ligne.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Étendues

Il est possible d’une série d’actions collaborer à l’aide d’une étendue de groupe.  Ceci est particulièrement utile pour l’implémentation de la gestion des exceptions.  Dans le concepteur, vous pouvez ajouter une nouvelle étendue et commencer à ajouter des actions à l’intérieur.  Vous pouvez définir des étendues en mode code comme suit :


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
