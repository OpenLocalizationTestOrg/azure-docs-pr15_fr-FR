Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle inclut une section intitulée paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui sera toujours la même. Chaque valeur de paramètre permet de définir les ressources qui sont déployés dans le modèle. 

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs d’un utilisateur peut fournir pendant le déploiement. Utiliser le champ de **valeur par défaut** pour assigner une valeur au paramètre, si aucune valeur n’est fourni pendant le déploiement.

Nous décrira chaque paramètre dans le modèle.

### <a name="sitename"></a>nom du site

Le nom de l’application web que vous souhaitez créer.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

Le nom de l’offre de Service d’application à utiliser pour l’hébergement de l’application web.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>référence (SKU)

Niveau de tarification pour le plan d’hébergement.

    "sku": {
      "type": "string",
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
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Le modèle définit les valeurs autorisées pour ce paramètre et affecte une valeur par défaut (S1) si aucune valeur n’est spécifié.

### <a name="workersize"></a>workerSize

La taille d’instance de l’offre d’hébergement (petite, moyenne ou grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
Le modèle définit les valeurs autorisées pour ce paramètre (0, 1 ou 2) et affecte une valeur par défaut (0) si aucune valeur n’est spécifié. Les valeurs correspondent aux petites, moyennes et grandes.
