<properties 
    pageTitle="Nouvelle version du schéma 2016-06-01 | Microsoft Azure" 
    description="Apprenez à écrire la définition de JSON pour la dernière version d’applications logique" 
    authors="jeffhollan" 
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
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nouvelle version du schéma 2016-06-01

Les nouvelles schéma et API version pour les applications logique comporte plusieurs améliorations qui améliorer la fiabilité et facilité d’utilisation des applications logique. Il existe 3 principales différences :

1. Ajout d’étendues, qui sont des actions qui contiennent un ensemble d’actions.
1. Conditions et des boucles sont des actions
1. L’exécution de commande plus clair via `runAfter` propriété (qui remplace `dependsOn`)

Pour plus d’informations sur la mise à niveau vos applications logique à partir du schéma 2015-08-01-aperçu au schéma 2016-06-01, [consultez la section mise à niveau ci-dessous.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. les étendues de

Un des modifications dans ce schéma est l’ajout d’étendues et la possibilité d’imbriquer des actions dans d’autres.  Ceci est utile lorsque regroupant un ensemble d’actions, ou lorsque vous avez besoin d’imbriquer des actions dans les autres (par exemple une condition peut contenir une autre condition).  Plus d’informations sur la syntaxe de la portée peuvent être trouvé [ici](app-service-logic-loops-and-scopes.md), mais un exemple simple étendue vous trouverez ci-dessous :


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. conditions et boucles des modifications

Dans les versions précédentes du schéma, conditions et des boucles ont été paramètres associés à une seule action.  Cette limitation a été levée dans ce schéma et maintenant conditions et des boucles apparaissant en tant que type d’action.  Vous trouverez plus d’informations [dans cet article](app-service-logic-loops-and-scopes.md), et un exemple simple d’une action condition est indiqué ci-dessous :

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. propriété RunAfter

La nouvelle `runAfter` propriété remplace `dependsOn` afin de permettre une plus grande précision de la commande Exécuter.  `dependsOn`a été synonyme de « l’action exécuté et a réussi, » toutefois autant de fois que vous avez besoin exécuter une action si l’action précédente a réussi, a échoué ou ignoré.  `runAfter`permet de cette flexibilité.  Il est un objet qui indique tous les noms d’action qu'il s’exécutera après et définit un tableau d’état qui sont acceptables pour déclencher à partir de.  Par exemple, si vous voulez exécuter après étape A a réussi et B a été a réussi ou a échoué, ce qui suit doit être construit `runAfter` propriété :

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Mise à niveau vers 2016-06-01 schéma

Mise à niveau vers le nouveau schéma 2016-06-01 prend uniquement quelques étapes sont nécessaires.  Vous trouverez plus d’informations sur les modifications à partir du schéma [dans cet article](app-service-logic-schema-2016-04-01.md).  Le processus de mise à niveau inclut exécutant le script de mise à niveau, enregistrer en tant que nouvelle application logique et le remplacement potentiellement ancienne application logique si nécessaire.

1. Ouvrez votre application logique en cours.
1. Cliquez sur le bouton de **Mise à jour de schéma** dans la barre d’outils
   
    ![][1]
   
    La définition de la mise à niveau est renvoyée.  Vous pouvez copier et coller dans une définition de ressource si vous avez besoin, mais nous **vous recommandons vivement de** vous utilisez le bouton **Enregistrer sous** afin de garantir connexion toutes les références sont valides dans l’application logique mis à niveau.
1. Cliquez sur le bouton **Enregistrer sous** dans la barre d’outils de la cuillère mise à niveau.
1. Renseignez le nom et une logique de l’état application et cliquez sur **créer** pour déployer votre application mise à niveau logique.
1. Vérifiez que votre application logique mis à niveau fonctionne comme prévu.

    >[AZURE.NOTE] Si vous utilisez un déclencheur manuel ou demande, l’URL de rappel changeront dans votre nouvelle application logique.  Utilisez la nouvelle URL pour vérifier qu’il fonctionne de bout en bout, et vous pouvez cloner sur votre application logique existant pour conserver les URL précédentes.

1. *Facultatif* Utilisez le bouton **Dupliquer** dans la barre d’outils (adjacente à l’icône de **Mise à jour de schéma** dans l’image ci-dessus) pour remplacer votre application logique précédente par la nouvelle version du schéma.  Cela est nécessaire uniquement si vous souhaitez conserver le même ID de ressource ou demander déclencheur URL de votre application logique.

### <a name="upgrade-tool-notes"></a>Notes de l’outil de mise à niveau

#### <a name="condition-mapping"></a>Mappage de condition

L’outil fera de son mieux pour regrouper les actions branche true et false ensemble dans l’étendue de la définition de la mise à niveau.  Spécifiquement le modèle du Concepteur de `@equals(actions('a').status, 'Skipped')` doit figurer comme un `else` action.  Cependant si l’outil détecte motifs il ne reconnaît pas qu'il créera potentiellement conditions distinctes pour le vrai et la branche false.  Actions peuvent être remappées publier la mise à niveau si nécessaire.

#### <a name="foreach-with-condition"></a>ForEach condition
  
Le modèle précédent d’une boucle foreach avec une condition par élément peut être répliqué dans le nouveau schéma avec l’action de filtrage.  Il doit avoir lieu automatiquement sur la mise à niveau.  La condition devienne une action de filtrage avant la boucle foreach (pour retourner uniquement un tableau des éléments qui correspondent à la condition), et cette matrice est passée dans l’action foreach.  Vous pouvez afficher un exemple de cette [dans cet article](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Balises de ressources

Les balises de ressources sont supprimées de mise à niveau et vous devrez les définir à nouveau pour le flux de travail mis à niveau.

## <a name="other-changes"></a>Autres modifications

### <a name="manual-trigger-renamed-to-request-trigger"></a>Déclenchement manuel renommé en demande déclencheur

Le type `manual` a été déconseillée et renommé `request` avec le type de `http`.  Il s’agit plus cohérente avec le type de motif que déclencheur sert à créer.

### <a name="new-filter-action"></a>Nouvelle action « filtre »

Si vous travaillez avec un grand tableau et qu’il soit nécessaire pour filtrer vers le bas jusqu'à un ensemble réduit d’éléments, vous pouvez utiliser le nouveau type de « filtre ».  Il accepte un tableau et une condition et est évaluer la condition pour chaque élément et retourner un tableau des éléments qui répondent à la condition.

### <a name="foreach-and-until-action-restrictions"></a>ForEach et jusqu'à ce que les restrictions d’action

Foreach et jusqu'à boucle sont limités à une seule opération.

### <a name="trackedproperties-on-actions"></a>TrackedProperties sur les Actions

Actions disposez désormais d’une propriété supplémentaire (frères à `runAfter` et `type`) appelé `trackedProperties`.  Il s’agit d’un objet qui spécifie certaines actions entrées ou les sorties à inclure dans la télémétrie Azure Diagnostic est émis dans le cadre d’un flux de travail.  Par exemple :

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Utiliser la définition de flux de travail de l’application logique](app-service-logic-author-definitions.md)
- [Créer un modèle de déploiement de l’application logique](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
