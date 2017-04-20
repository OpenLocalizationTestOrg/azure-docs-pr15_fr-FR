<properties
   pageTitle="Applications logique comme points de terminaison pouvant être appelé par"
   description="Comment créer et configurer des points de terminaison déclencheur et les utiliser dans une application logique dans le Service d’application Azure"
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


# <a name="logic-apps-as-callable-endpoints"></a>Applications logique comme points de terminaison pouvant être appelé par

Logique d’applications en mode natif peut exposer un point de terminaison HTTP synchrone comme un déclencheur.  Vous pouvez également utiliser le modèle de points de terminaison pouvant être appelé par pour appeler logique applications sous forme d’un flux de travail imbriquée via l’action « flux de travail » dans une application logique.

Il existe 3 types de déclencheurs doit peuvent recevoir des demandes :

* Demande
* ApiConnectionWebhook
* HttpWebhook

Pour le reste de l’article, nous allons utiliser **demande** que dans l’exemple, mais tous les principes s’appliquent identique à 2 autres types de déclencheurs.

## <a name="adding-a-trigger-to-your-definition"></a>Ajout d’un déclencheur pour la définition de votre
La première étape consiste à ajouter un déclencheur pour la définition de votre application de logique doit peut recevoir des demandes entrantes.  Vous pouvez rechercher dans le concepteur pour « Demande HTTP » ajouter la carte de déclencheur. Vous pouvez définir un corps de demande JSON schéma et le concepteur génère jetons pour vous aider à analyser et passer des données à partir du déclencheur manuel via le flux de travail.  J’ai vous recommandons d’utiliser un outil comme [jsonschema.net](http://jsonschema.net) pour générer un schéma JSON à partir de la charge utile de corps d’un exemple.

![Demande de déclencheur carte][2]

Après avoir enregistré la définition de votre application logique, une URL de rappel sera générée similaire à celui-ci :
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Cette URL contient une clé associations de sécurité dans les paramètres de requête utilisés pour l’authentification.

Vous pouvez également obtenir ce point de terminaison dans le portail Azure :

![][1]

Ou, en appelant :

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Sécurité de l’URL du déclencheur

URL de rappel application logique sont générées en toute sécurité à l’aide d’une Signature accès partagé.  La signature est transmise comme paramètre de requête et doit être validée avant l’application logique se déclenche.  Il est généré via un ensemble unique d’une clé secrète par application logique, le nom du déclencheur et l’opération en cours.  Sauf si un utilisateur a accès à la clé d’application logique secrète, ils ne pourront pas générer une signature valide.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Appel de point de terminaison du déclencheur application logique

Une fois que vous avez créé le point de terminaison pour votre déclencheur, vous pouvez le déclencher via un `POST` l’URL complète. Vous pouvez inclure des en-têtes supplémentaires et tout son contenu dans le corps.

Si le type de contenu est `application/json` puis vous pourrez à partir des propriétés de référence à l’intérieur de la demande. Dans le cas contraire, il sera traité comme une seule unité binaire qui peut être passée à une autre API mais ne peuvent pas être référencée dans le flux de travail sans convertir le contenu.  Par exemple, si vous passez `application/xml` contenu que vous pouvez aussi utiliser `@xpath()` pour effectuer une extraction xpath, ou `@json()` pour convertir des XML en JSON.  Plus d’informations sur l’utilisation de contenu types [trouverez ici](app-service-logic-content-type.md)

En outre, vous pouvez spécifier un schéma JSON dans la définition. Ainsi, le concepteur générer des jetons que vous pouvez ensuite passer en étapes.  Par exemple ce qui suit apporte une `title` et `name` jeton disponible dans le concepteur :

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Référencer le contenu de la requête entrante

La `@triggerOutputs()` fonction affiche le contenu de la requête entrante. Par exemple, il se présente comme :

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Vous pouvez utiliser la `@triggerBody()` raccourci pour accéder à la `body` propriété spécifiquement. 

## <a name="responding-to-the-request"></a>Répondre à la demande

Pour des requêtes qui démarrent une application logique, vous souhaiterez répondre avec du contenu à l’appelant. Il existe un nouveau type d’action appelé **réponse** qui peut être utilisé pour créer le code d’état, corps et les en-têtes pour votre réponse. Notez que si aucune forme **réponse** n’est présente, le point de terminaison logique application sera *immédiatement* répondre avec **202 accepté**.

![Action de réponse HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Réponses de disposer des éléments suivants :

| Propriété | Description |
| -------- | ----------- |
| statusCode | Code d’état HTTP pour répondre à la demande entrante. Il peut être tout code d’état valide qui commence par 2xx, 4xx ou 5xx. codes d’état 3xx ne sont pas autorisés. | 
| corps | Un objet corps qui peut être une chaîne, un objet JSON ou le contenu même binaire référencé à partir d’une étape précédente. | 
| en-têtes | Vous pouvez définir le nombre d’en-têtes à inclure dans la réponse | 

Toutes les étapes décrites dans l’application logique requis pour la réponse doivent se terminer dans *60 secondes* pour la demande d’origine recevoir la réponse **, à moins que le flux de travail est appelée comme une application logique imbriquées**. Si aucune action de réponse n’est atteint au sein de 60 secondes alors la requête entrante sera délai d’expiration et recevoir une réponse **408 délai sur le Client** HTTP.  Pour les applications logique imbriqués, le parent logique application continuent à attendre une réponse jusqu'à la fin, quel que soit le nombre d’heures nécessaire.

## <a name="advanced-endpoint-configuration"></a>Configuration de point de terminaison avancées

Applications logique prise en charge pour le point de terminaison accès direct ont intégrée et toujours utiliser le `POST` méthode pour démarrer une exécution de l’application logique. L’application **HTTP récepteur** API précédemment également pris en charge modifiant les segments URL et la méthode HTTP. Vous pouvez même configurer une sécurité supplémentaire ou un domaine personnalisé en l’ajoutant à l’hôte d’application API (l’application Web hébergeant l’application API). 

Cette fonctionnalité est disponible via la **gestion de l’API**:
* [Modifier la méthode de la demande](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modifier les segments URL de la demande](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurer votre API gestion des domaines dans l’onglet **configurer** dans le portail Azure classique
* Configurer la stratégie pour vérifier l’authentification de base (**lien nécessaire**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Résumé de la migration de 2014-12-01-aperçu

|  2014-12-01-aperçu | 2016-06-01 |
|---------------------|--------------------|
| Cliquez sur application **Écoute HTTP** API | Cliquez sur **déclencheur manuel** (aucune application API obligatoire) |
| Récepteur HTTP définition «*envoie automatiquement réponse*» | Des options incluent une action de **réponse** ou non dans la définition de flux de travail |
| Configurer l’authentification de base ou OAuth | via la gestion de l’API |
| Configurer la méthode HTTP | via la gestion de l’API |
| Configurer le chemin d’accès relatif | via la gestion de l’API |
| Référencer le corps entrant via`@triggerOutputs().body.Content` | Référence via`@triggerOutputs().body` |
| Action de **réponse HTTP envoyer** l’écoute HTTP | Cliquez sur **répondre à la demande HTTP** (aucune application API obligatoire)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
