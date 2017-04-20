<properties
   pageTitle="Dépendances dans le Gestionnaire de ressources modèles | Microsoft Azure"
   description="Décrit comment définir une ressource comme dépendant d’une autre ressource au cours du déploiement afin de déployer des ressources dans l’ordre correct."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Définition des dépendances dans le Gestionnaire de ressources Azure modèles

Pour une ressource donnée, il est possible d’autres ressources qui doivent exister avant le déploiement de la ressource. Par exemple, un serveur SQL server doit exister avant d’essayer de déploiement d’une base de données SQL. Pour définir cette relation, le marquage d’une ressource comme dépendant de l’autre ressource. En règle générale, vous définissez une dépendance avec l’élément **dependsOn** , mais vous pouvez également le définir via la fonction de **référence** . 

Gestionnaire de ressources évalue les dépendances entre les ressources et les déploie dans leur ordre dépendante. Lorsque les ressources ne sont pas dépendants de l’autre, le Gestionnaire de ressources les déploie en parallèle.

## <a name="dependson"></a>dependsOn

Au sein de votre modèle, l’élément dependsOn vous permet de définir une ressource en tant que dépendant sur une ou plusieurs ressources. Sa valeur peut être une liste de noms de ressources séparées par des virgules. 

L’exemple suivant montre un ensemble d’échelle machine virtuelle dont dépend un équilibrage de charge réseau virtuel et une boucle qui crée plusieurs comptes de stockage. Ces autres ressources ne sont pas affichés dans l’exemple suivant, mais ils doivent exister ailleurs dans le modèle.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Pour définir une relation entre une ressource et les ressources qui sont créés dans une boucle de copie, définissez l’élément dependsOn au nom de la boucle. Pour obtenir un exemple, voir [créer plusieurs instances de ressources dans le Gestionnaire de ressources Azure](resource-group-create-multiple.md).

Pendant que vous pouvez être incliné à utiliser dependsOn pour mapper les relations entre vos ressources, il est important de comprendre pourquoi que vous faites, car il peut affecter les performances de votre déploiement. Par exemple, pour documenter la manière dont les ressources sont reliés, dependsOn n’est pas la bonne approche. Vous ne pouvez pas interroger les ressources qui ont été définis dans l’élément dependsOn après le déploiement. À l’aide de dependsOn, vous avoir un impact sur le temps de déploiement, car le Gestionnaire de ressources ne déploie pas en parallèles deux ressources présentant une relation. Relations entre les ressources de document, utilisez à la place la [liaison de ressources](resource-group-link-resources.md).

## <a name="child-resources"></a>Ressources enfants

La propriété ressources permet de spécifier des ressources enfants liés à la ressource qui est définie. Ressources enfants ne peuvent être défini cinq niveaux. Il est important de noter qu’une dépendance implicite n’a pas été créée entre une ressource enfant et la ressource parent. Si vous avez besoin de la ressource enfant à déployer après la ressource parent, vous devez indiquer explicitement cette dépendance avec la propriété dependsOn. 

Chaque ressource parent accepte uniquement certains types de ressources comme des ressources de l’enfant. Les types de ressource acceptés sont spécifiés dans le [schéma de modèle](https://github.com/Azure/azure-resource-manager-schemas) de la ressource parent. Le nom du type de ressource enfant inclut le nom du type de ressource parent, tels que **Microsoft.Web/sites/config** et **Microsoft.Web/sites/extensions** sont les deux ressources enfant de la **Microsoft.Web/sites**.

L’exemple suivant montre un SQL server et la base de données SQL. Notez qu’une dépendance explicite est définie entre la base de données SQL et SQL server, même si la base de données est l’enfant du serveur.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>fonction de référence

La [fonction de référence](resource-group-template-functions.md#reference) permet d’une expression à sa valeur dérivent d’autres nom JSON et paires valeur ou ressources d’exécution. Expressions de référence déclarent implicitement qu’une ressource dépend d’un autre. 

    reference('resourceName').propertyPath

Vous pouvez utiliser cet élément ou l’élément dependsOn pour spécifier les dépendances, mais vous n’avez pas besoin d’utiliser les deux pour la même ressource dépendante. Si possible, utilisez une référence implicite d’éviter d’ajouter par inadvertance une dépendance inutile.

Pour plus d’informations, voir [fonction de référence](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles Azure le Gestionnaire de ressources, voir [modèles de création](resource-group-authoring-templates.md). 
- Pour obtenir la liste des fonctions disponibles dans un modèle, voir [fonctions de modèle](resource-group-template-functions.md).

