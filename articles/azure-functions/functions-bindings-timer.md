<properties
    pageTitle="Azure déclencheur minuteur de fonctions | Microsoft Azure"
    description="Apprenez à utiliser des déclencheurs de minuteur dans Azure fonctions."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctionne, fonctions, traitement des événements, cluster dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure déclencheur minuteur de fonctions

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer des déclencheurs de minuteur dans des fonctions Azure. Minuteur déclenche appel de fonctions basées sur une planification, ponctuelle ou périodique.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON déclencheur du temporisateur

Le fichier *function.json* fournit une expression de planification. Par exemple, la planification suivante s’exécute la fonction minute :

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le déclencheur minuteur gère plusieurs instances horizontale automatiquement : qu’une seule instance d’une fonction particulière minuteur exécutera dans toutes les instances.

## <a name="format-of-schedule-expression"></a>Format d’expression de planning

L’expression de planification est une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui inclut 6 champs : `{second} {minute} {hour} {day} {month} {day of the week}`. 

Notez que la plupart des expressions cron que vous trouvez omettre le champ {deuxième}, si vous copiez parmi ceux que vous aurez pour s’ajuster au champ supplémentaire. 

Voici un autre calendrier exemples d’expressions :

Déclencher une fois toutes les 5 minutes :

```json
"schedule": "0 */5 * * * *"
```

Déclencher une seule fois en haut de chaque heure :

```json
"schedule": "0 0 * * * *",
```

Déclencher une fois toutes les deux heures :

```json
"schedule": "0 0 */2 * * *",
```

Déclencher une fois par heure de 9 h à 17 :

```json
"schedule": "0 0 9-17 * * *",
```

Déclencher à 9:30 quotidiennement :

```json
"schedule": "0 30 9 * * *",
```

Déclencher à 9:30, tous les jours ouvrables :

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Exemple de code du déclencheur c# du temporisateur

Cet exemple de code c# écrit un journal unique chaque déclenchement de la fonction.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
