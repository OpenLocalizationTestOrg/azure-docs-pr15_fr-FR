Avec le Gestionnaire de ressources Azure, vous définissez les paramètres pour les valeurs que vous souhaitez spécifier lorsque le modèle est déployé. Le modèle inclut une section intitulée paramètres qui contient toutes les valeurs de paramètre.
Vous devez définir un paramètre pour les valeurs qui varient basée sur le projet que vous déployez ou sur l’environnement que vous effectuez le déploiement. Ne définissez pas les paramètres pour les valeurs qui sera toujours la même. Chaque valeur de paramètre est utilisé dans le modèle pour définir les ressources qui sont déployer. 

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs d’un utilisateur peut fournir pendant le déploiement. Utiliser le champ de **valeur par défaut** pour assigner une valeur au paramètre, si aucune valeur n’est fourni pendant le déploiement.

Nous décrira chaque paramètre dans le modèle.

### <a name="logicappname"></a>logicAppName

Le nom de l’application logique à créer.

    "logicAppName": {
        "type": "string"
    }