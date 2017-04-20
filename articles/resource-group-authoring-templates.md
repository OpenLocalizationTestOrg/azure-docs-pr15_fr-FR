<properties
   pageTitle="Propos Azure Gestionnaire de ressources | Microsoft Azure"
   description="Créer des modèles Azure le Gestionnaire de ressources à l’aide de la syntaxe déclarative JSON pour déployer des applications à Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Création de modèles de gestionnaire de ressources Azure

Cette rubrique décrit la structure d’un modèle d’Azure le Gestionnaire de ressources. Il présente les différentes sections d’un modèle et les propriétés qui sont disponibles dans ces sections. Le modèle est constitué de JSON et expressions que vous pouvez utiliser pour créer des valeurs pour votre déploiement. 

Pour afficher le modèle pour les ressources que vous avez déjà déployé, voir [Exporter un modèle Azure le Gestionnaire de ressources à partir de ressources existantes](resource-manager-export-template.md). Pour des instructions sur la création d’un modèle, voir [Présentation du modèle Gestionnaire de ressources](resource-manager-template-walkthrough.md). Pour obtenir des recommandations sur la création de modèles, voir [meilleures pratiques pour la création de modèles Azure le Gestionnaire de ressources](resource-manager-template-best-practices.md).

Un bonne éditeur JSON peut simplifier la tâche de création de modèles. Pour plus d’informations sur l’utilisation de Visual Studio avec vos modèles, voir [Création et déploiement de groupes de ressources Azure via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Pour plus d’informations sur l’utilisation de Code VS, voir [utilisation des modèles du Gestionnaire de ressources Azure dans Code Visual Studio](resource-manager-vs-code.md).

Limiter la taille de votre modèle à 1 Mo et chaque fichier paramètre à 64 Ko. La limite de 1 Mo s’applique à l’état final du modèle après que qu’il a été développé avec les valeurs des variables et des paramètres et les définitions de ressource itératif. 

## <a name="template-format"></a>Format de modèle

Dans sa structure la plus simple, un modèle contient les éléments suivants :

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| $schema        |   Oui    | Emplacement du fichier de schéma JSON qui décrit la version de la langue du modèle. Utiliser l’URL affichée dans l’exemple précédent.
| contentVersion |   Oui    | Version du modèle (par exemple, 1.0.0.0). Vous pouvez fournir une valeur pour cet élément. Lorsque vous déployez des ressources à l’aide du modèle, cette valeur peut être utilisée pour vous assurer que le modèle approprié est utilisé.
| paramètres     |   N°     | Valeurs qui sont fournies lors de l’exécution de déploiement pour personnaliser le déploiement de ressources.
| variables      |   N°     | Valeurs qui sont utilisées en tant que fragments JSON dans le modèle pour simplifier les expressions de langage de modèle.
| ressources      |   Oui    | Types de ressources sont déployés ou mis à jour dans un groupe de ressources.
| sorties        |   N°     | Valeurs renvoyées après le déploiement.

Nous allons examiner les sections du modèle de façon plus détaillée plus loin dans cette rubrique. Pour l’instant, nous allons examiner quelques de la syntaxe qui composent le modèle.

## <a name="expressions-and-functions"></a>Expressions et des fonctions

La syntaxe de la base du modèle est JSON. Toutefois, expressions et des fonctions étendent le JSON n’est disponible dans le modèle. Avec des expressions, vous créez des valeurs qui ne sont pas des valeurs littérales stricts. Expressions placées entre crochets [et] et sont évaluées lorsque le modèle est déployé. Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON et toujours retourner une autre valeur JSON. Si vous devez utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux entre crochets [[.

En règle générale, vous utilisez expressions avec des fonctions pour effectuer des opérations de configuration du déploiement. Comme dans JavaScript, les appels de fonction sont mis en forme en tant que **functionName(arg1,arg2,arg3)**. Vous référencez propriétés en utilisant les opérateurs point et [index].

L’exemple suivant montre comment utiliser plusieurs fonctions lors de la création des valeurs :
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Pour obtenir la liste complète des fonctions de modèle, voir [fonctions de modèle Azure le Gestionnaire de ressources](resource-group-template-functions.md). 


## <a name="parameters"></a>Paramètres

Dans la section Paramètres du modèle, vous spécifiez les valeurs que vous pouvez entrer lorsque vous déployez les ressources. Ces valeurs de paramètre permettent de personnaliser le déploiement en fournissant des valeurs qui sont conçues pour un environnement particulier (par exemple, le développement, de test et production). Vous n’êtes pas obligé de fournir des paramètres dans votre modèle, mais sans paramètres votre modèle est toujours déployer les mêmes ressources avec les mêmes noms, les emplacements et les propriétés.

Vous pouvez utiliser ces valeurs de paramètre dans le modèle pour définir les valeurs pour les ressources déployées. Uniquement les paramètres qui sont déclarés dans la section paramètres peuvent être utilisés dans d’autres sections du modèle.

Vous définissez des paramètres avec la structure suivante :

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| parameterName  |   Oui    | Nom du paramètre. Doit être un identificateur JavaScript valide.
| type           |   Oui    | Type de la valeur de paramètre. Consultez la liste ci-dessous contient les types d’autorisés.
| valeur par défaut   |   N°     | Valeur par défaut pour le paramètre, si aucune valeur n’est fourni pour le paramètre.
| allowedValues  |   N°     | Tableau de valeurs autorisées pour vous assurer que la valeur correcte est fournie pour ce paramètre.
| minValue       |   N°     | La valeur minimale pour les paramètres de type int, cette valeur est incluse.
| maxValue       |   N°     | La valeur maximale de paramètres de type int, cette valeur est incluse.
| minLength      |   N°     | La longueur minimale de chaîne secureString et paramètres de type tableau, cette valeur est incluse.
| maxLength      |   N°     | La longueur maximale de chaîne secureString et paramètres de type tableau, cette valeur est incluse.
| Description    |   N°     | Description du paramètre, qui est présenté aux utilisateurs du modèle via l’interface du portail modèle personnalisé.

Les valeurs et les types autorisés sont :

- **chaîne**
- **secureString**
- **ent**
- **bool**
- **objet** 
- **secureObject**
- **tableau**

Pour spécifier un paramètre comme facultatif, fournissent une valeur par défaut (peut être une chaîne vide). 

Si vous spécifiez un nom du paramètre qui correspond à l’un des paramètres de la commande pour déployer le modèle, vous êtes invité à fournir une valeur pour un paramètre avec le suffixe **modèle**. Par exemple, si vous incluez un paramètre nommé **ResourceGroupName** dans votre modèle qui est le même que le paramètre **ResourceGroupName** dans [Nouveau AzureRmResourceGroupDeployment] [ deployment2cmdlet] applet de commande, vous êtes invité à fournir une valeur pour **ResourceGroupNameFromTemplate**. En règle générale, vous devez éviter cette confusion par nommer ne pas les paramètres du même nom en tant que paramètres utilisés pour les opérations de déploiement.

>[AZURE.NOTE] Tous les mots de passe, les clés et autres secrets doivent utiliser le type de **secureString** . Paramètres de modèle avec le type de secureString ne peuvent pas être lus après le déploiement de la ressource. 

L’exemple suivant montre comment définir les paramètres :

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Pour savoir comment entrer les valeurs de paramètre au cours du déploiement, voir [déployer une application avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variables

Dans la section variables, construire valeurs qui peuvent être utilisées dans l’ensemble de votre modèle. En règle générale, les variables sont basées sur les valeurs fournies à partir des paramètres. Vous n’avez pas besoin de définir des variables, mais ils simplifient souvent votre modèle en réduisant des expressions complexes.

Vous définissez des variables avec la structure suivante :

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

L’exemple suivant montre comment définir une variable qui est construite à partir de deux valeurs de paramètre :

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

L’exemple suivant montre une variable qui est un type JSON complexe et des variables qui sont construits à partir d’autres variables :

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Ressources

Dans la section ressources, vous définissez les ressources qui sont déployés ou mis à jour. Cette section accessible compliquée, car vous devez comprendre les types de que vous déployez pour fournir les valeurs correctes. 

Vous définissez des ressources avec la structure suivante :

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nom de l’élément             | Obligatoire | Description
| :----------------------: | :------: | :----------
| apiVersion               |   Oui    | Version de l’API REST à utiliser pour créer la ressource.
| type                     |   Oui    | Type de la ressource. Cette valeur est une combinaison de l’espace de noms de fournisseur de ressources et le type de ressource (par exemple, **Microsoft.Storage/storageAccounts**).
| nom                     |   Oui    | Nom de la ressource. Le nom doit respecter les restrictions de composant URI définies dans RFC3986. En outre, des services Azure qui exposent le nom de la ressource à l’extérieur de parties valider le nom pour vous assurer qu’il n’est pas une tentative d’usurpation d’identité un autre. Voir [recherche de nom de la ressource](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| emplacement                 |   Variable.  | Emplacements geo pris en charge de la ressource fournie. Vous pouvez sélectionner un des emplacements disponibles, mais en général il est préférable d’en choisir un qui est éloigné de vos utilisateurs. En règle générale, il est également judicieux de placer les ressources qui interagissent avec les uns des autres dans la même région. La plupart des types de ressources nécessitent un emplacement, mais certains types (par exemple, une attribution de rôle) ne nécessitent pas un emplacement.
| balises                     |   N°     | Balises qui sont associées à la ressource.
| commentaires                 |   N°     | Vos notes pour la documentation de ressources dans votre modèle
| dependsOn                |   N°     | Ressources qui dépend de la ressource est définie. Les dépendances entre les ressources sont évaluées et déployer des ressources dans leur ordre dépendante. Lorsque les ressources ne sont pas dépendants de l’autre, ils sont déployés en parallèle. La valeur peut être une liste séparée par des virgules d’une ressource de noms ou les identificateurs uniques de la ressource.
| propriétés               |   N°     | Paramètres de configuration spécifiques à la ressource. Les valeurs pour les propriétés sont identiques en tant que les valeurs que vous fournissez dans le corps de la demande pour l’opération de l’API REST (méthode place) créer la ressource. Pour accéder à la documentation relative au schéma ressource ou API REST, voir [Gestionnaire de ressources fournisseurs, régions, versions API et schémas](resource-manager-supported-services.md).
| copie                     |   N°     | Si plusieurs instances est nécessaire, le nombre de ressources à créer. Pour plus d’informations, voir [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md). |
| ressources                |   N°     | Ressources enfants qui dépendent de la ressource est définie. Vous pouvez fournir uniquement les types de ressources qui sont autorisées par le schéma de la ressource parent. Le nom complet du type de ressource enfant inclut le type de ressource parent, tel que **Microsoft.Web/sites/extensions**. Dépendance par rapport à la ressource parent n’est pas concerné ; Vous devez définir explicitement cette dépendance. 

Savoir quelles valeurs spécifier pour **apiVersion**, **type**et **l’emplacement** n’est pas évidente. Peut être effectué en, vous pouvez déterminer ces valeurs via PowerShell Azure ou Azure infrastructure du langage commun.

Pour obtenir tous les fournisseurs de ressources avec **PowerShell**, utilisez :

    Get-AzureRmResourceProvider -ListAvailable

Dans la liste renvoyée, recherchez les fournisseurs de ressources que qui vous intéresse. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, stockage), utilisez :

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Pour obtenir les versions de l’API pour un type de ressource (ces comptes stockage), utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Pour obtenir tous les fournisseurs de ressources avec **Azure infrastructure du langage commun**, utilisez :

    azure provider list

Dans la liste renvoyée, recherchez les fournisseurs de ressources que qui vous intéresse. Pour obtenir les types de ressources pour un fournisseur de ressources (par exemple, stockage), utilisez :

    azure provider show Microsoft.Storage

Pour obtenir les emplacements pris en charge et les versions de l’API, utilisez :

    azure provider show Microsoft.Storage --details --json

Pour en savoir plus sur les fournisseurs de ressources, voir [Gestionnaire de ressources fournisseurs, régions, versions API et schémas](resource-manager-supported-services.md).

La section ressources contient un tableau des ressources à déployer. Au sein de chaque ressource, vous pouvez également définir un tableau des ressources enfants. Par conséquent, votre section ressources peut avoir une structure, telles que :

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


L’exemple suivant montre une ressource **Microsoft.Web/serverfarms** et une ressource **Microsoft.Web/sites** avec **Extensions** ressource enfant. Notez que le site est marqué comme dépendant de la batterie de serveurs dans la mesure où la batterie de serveurs doit exister avant de déployer le site. Notez également que la ressource **Extensions** est enfant du site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Sorties

Dans la section des sorties, vous spécifiez des valeurs qui sont retournés à partir de déploiement. Par exemple, vous pouvez retourner l’URI utilisé pour accéder à une ressource déployée.

L’exemple suivant illustre la structure d’une définition de sortie :

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nom de l’élément   | Obligatoire | Description
| :------------: | :------: | :----------
| nomsortie     |   Oui    | Nom de la valeur de sortie. Doit être un identificateur JavaScript valide.
| type           |   Oui    | Type de la valeur de sortie. Valeurs de sortie prend en charge les mêmes types en tant que paramètres d’entrée du modèle.
| valeur          |   Oui    | Expression de langue de modèle qui est évaluée et retournée sous forme de valeur de sortie.


L’exemple suivant montre une valeur qui est renvoyée dans la section des sorties.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Pour plus d’informations sur l’utilisation de sortie, voir [partage état dans le Gestionnaire de ressources Azure modèles](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour afficher les modèles complètes pour de nombreux types de solutions, voir les [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/).
- Pour plus d’informations sur les fonctions, vous pouvez utiliser à partir d’un modèle, voir [Fonctions de modèle Azure Gestionnaire de ressources](resource-group-template-functions.md).
- Pour combiner plusieurs modèles lors du déploiement, voir [l’aide de modèles liées avec le Gestionnaire de ressources Azure](resource-group-linked-templates.md).
- Vous devrez peut-être utiliser les ressources qui existent au sein d’un groupe de ressources différent. Ce scénario est courant lorsque vous travaillez avec des comptes de stockage ou des réseaux virtuels qui sont partagées entre plusieurs groupes de ressources. Pour plus d’informations, voir la [fonction resourceId](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
