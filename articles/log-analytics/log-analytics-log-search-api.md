<properties
    pageTitle="Journal Analytique se API REST de recherche | Microsoft Azure"
    description="Ce guide fournit un didacticiel de base qui décrivent comment vous pouvez utiliser la recherche de journal Analytique API REST dans la Suite de gestion des opérations (OMS) ainsi que des exemples qui vous montrent comment utiliser les commandes."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>Recherche des journaux de journal Analytique API REST

Ce guide fournit un didacticiel de base qui décrivent comment vous pouvez utiliser l’API REST de journal Analytique recherche dans la Suite de gestion des opérations (OMS) ainsi que des exemples qui vous montrent comment utiliser les commandes. Parmi les exemples de cet article de référence Insights opérationnelles, qui est le nom de la version précédente du journal Analytique.

## <a name="overview-of-the-log-search-rest-api"></a>Vue d’ensemble de la recherche du journal API REST

L’API REST de journal Analytique recherche est RESTful et sont accessibles via l’API du Gestionnaire de ressources Azure. Dans ce document vous trouverez des exemples qui accèdent à l’API via [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l’appel de l’API du Gestionnaire de ressources Azure. L’utilisation de ARMClient et PowerShell est une des nombreuses options pour accéder à l’API de recherche de journal Analytique. Une autre option consiste à utiliser le module Azure PowerShell pour OperationalInsights qui inclut les applets de commande pour accéder à la recherche. Grâce à ces outils, vous pouvez utiliser l’API du Gestionnaire de ressources Azure RESTful pour passer des appels vers les espaces de travail OMS et exécuter des commandes de recherche qu’ils contiennent. L’API génère des résultats de recherche pour vous au format JSON, ce qui vous permet d’utiliser les résultats de recherche de différentes façons par programme.

Le Gestionnaire de ressources Azure peut être utilisé via une [bibliothèque pour .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) ainsi qu’une [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Passez en revue les pages web liées pour en savoir plus.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Didacticiel API REST de journal Analytique recherche simple

### <a name="to-use-the-arm-client"></a>Pour utiliser le Client processeur

1. Installez [Chocolatey](https://chocolatey.org/), qui est un gestionnaire de Package Source ouvert pour Windows. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur, puis exécutez la commande suivante :

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Installez le ARMClient en exécutant la commande suivante :

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Pour effectuer une recherche simple à l’aide de la ARMClient

1. Connectez-vous à votre compte Microsoft ou OrgID :

    ```
    armclient login
    ```

    Connexion réussie répertorie tous les abonnements liés au compte donné :

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenir les espaces de travail opérations Management Suite :

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Un appel Get réussi affiche tous les espaces de travail liés à l’abonnement :

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Créer votre variable de recherche :

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Effectuer une recherche à l’aide de votre nouvelle variable de recherche :

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Ouvrez une session exemples de référence de l’API REST de recherche Analytique
Les exemples suivants montrent comment vous pouvez utiliser l’API de recherche.

### <a name="search---actionread"></a>Recherche - Action/lu

**Url de l’exemple :**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Demande :**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Le tableau suivant décrit les propriétés disponibles.

|**Propriété**|**Description**|
|---|---|
|Retour au début|Le nombre maximal de résultats à renvoyer.|
|Mettez en surbrillance|Contient des paramètres pre et post, généralement utilisés pour mettre en surbrillance les champs correspondants|
|versions antérieures|Les préfixes la chaîne donnée à vos champs correspondantes.|
|Publier|Ajoute la chaîne donnée à vos champs correspondantes.|
|requête|La requête de recherche utilisée pour recueillir et retourner des résultats.|
|Démarrer|Début de la fenêtre de temps que sont indiqués dans les résultats.|
|fin|La fin de la fenêtre de temps que sont indiqués dans les résultats.|


**Réponse :**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Effectuer une recherche / {ID} - Action/lu

**Demander le contenu d’une recherche enregistrée :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Si la recherche renvoie dont l’état « En attente », puis l’interrogation les résultats de la mise à jour peut être effectuée via cette API. Après 6 min, le résultat de la recherche est supprimé à partir du cache et HTTP disparaît sera renvoyée. Si la demande de recherche initiale renvoyée immédiatement « réussite », il n’est pas ajoutée au cache à l’origine de cette API renvoyer HTTP disparaît si interrogé. Le contenu d’un résultat HTTP 200 se trouvent dans le même format que la demande de recherche initiale uniquement avec les valeurs mises à jour.

### <a name="saved-searches---rest-only"></a>Recherches - enregistrées reste uniquement

**Demander la liste des recherches enregistrées :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Méthodes prises en charge : GET placer supprimer

Prise en charge des méthodes de collecte : obtenir

Le tableau suivant décrit les propriétés disponibles.

|Propriété|Description|
|---|---|
|ID|Identificateur unique.|
|ETag|**Requis pour le correctif**. Mise à jour par serveur sur chaque écriture. Valeur doit être égale à la valeur stockée actuelle ou ' *' pour mettre à jour. 409 retournées pour les valeurs de l’ancien / n’est pas valide.|
|Properties.Query|**Obligatoire**. La requête de recherche.|
|properties.displayName|**Obligatoire**. Le nom d’affichage définis utilisateur de la requête. Si le modèle en tant qu’une ressource Azure, il s’agit d’une balise.|
|Properties.Category|**Obligatoire**. Catégorie de la requête définie par l’utilisateur. Modèle en tant qu’une ressource Azure c’est si une balise.|

>[AZURE.NOTE] L’API de recherche de journal Analytique renvoie actuellement créés par l’utilisateur à l’enregistrement des recherches lorsque des recherches de recherches enregistrées dans un espace de travail. L’API ne retourne pas de recherches enregistrées fournis par solutions pour le moment. Cette fonctionnalité est ajoutée à une date ultérieure.

### <a name="create-saved-searches"></a>Créer des recherches enregistrées

**Demande :**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Supprimer enregistrée recherches

**Demande :**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Recherches de mise à jour enregistré

 **Demande :**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Métadonnées - JSON uniquement

Voici un moyen pour afficher les champs pour tous les types de journal pour les données collectées dans votre espace de travail. Par exemple, si vous voulez que savoir si le type d’événement possède un champ nommé ordinateur, il est un moyen de définitions, puis confirmer.

**Demande de champs :**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Réponse :**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

Le tableau suivant décrit les propriétés disponibles.

|**Propriété**|**Description**|
|---|---|
|nom|Nom du champ.|
|nom complet|Le nom d’affichage du champ.|
|type|Le Type de la valeur du champ.|
|facetable|Combinaison en cours « indexé », ' stockées » et les propriétés de « facette ».|
|affichage|Propriété 'afficher' Current. True si le champ est visible dans la recherche.|
|ownerType|Réduit à uniquement des Types appartenant à l’adresse IP onboarded.|


## <a name="optional-parameters"></a>Paramètres facultatifs
Les informations suivantes décrivent les paramètres facultatifs disponibles.

### <a name="highlighting"></a>Mise en surbrillance

Le paramètre « Mise en surbrillance » est un paramètre facultatif, vous pouvez utiliser pour demander le sous-système recherche incluent un ensemble de marques de données dans sa réponse.

Ces marqueurs indiquent le début et la fin du texte en surbrillance qui répond aux critères fournies dans votre requête de recherche.
Vous pouvez spécifier les marques de début et de fin utilisées par la recherche pour renvoyer à la ligne le terme mis en surbrillance.

**Exemple de requête de recherche**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Résultat de l’exemple :**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Remarquez que le résultat ci-dessus contienne un message d’erreur qui a été le préfixe et ajouté.

## <a name="computer-groups"></a>Groupes d’ordinateurs

Groupes d’ordinateurs sont des recherches enregistrées spéciaux qui renvoient un ensemble d’ordinateurs.  Vous pouvez utiliser un groupe d’ordinateurs dans d’autres requêtes pour limiter les résultats sur les ordinateurs dans le groupe.  Un groupe d’ordinateurs est implémenté comme une recherche enregistrée avec une balise de groupe avec une valeur de l’ordinateur.

Voici un exemple de réponse pour un groupe d’ordinateurs.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Récupération des groupes d’ordinateurs

Utilisez la méthode Get avec l’ID du groupe pour récupérer un groupe d’ordinateurs.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Création ou mise à jour d’un groupe d’ordinateurs

Pour utiliser la méthode place avec un seul enregistré ID de recherche pour créer un nouveau groupe d’ordinateurs. Si vous utilisez un ID de groupe ordinateur existant, qu’il sera modifié. Lorsque vous créez un groupe d’ordinateurs dans la console OMS, l’ID est créé à partir du groupe et un nom.

La requête utilisée pour la définition de groupe doit renvoyer un jeu d’ordinateurs pour le groupe de fonctionner correctement.  Il est recommandé que vous terminez votre requête avec *| Ordinateur distinct* pour vérifier les données correctes sont retournées.

La définition de la recherche enregistrée doit inclure une balise nommée Group avec une valeur d’ordinateur pour la recherche pour être considérés comme un groupe d’ordinateurs.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Suppression de groupes d’ordinateurs

Pour supprimer un groupe d’ordinateurs, utilisez la méthode Delete de l’ID de groupe.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [les recherches de journal](log-analytics-log-searches.md) créer des requêtes à l’aide de champs personnalisés pour les critères.
