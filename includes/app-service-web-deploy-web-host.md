### <a name="app-service-plan"></a>Plan de services d’application

Crée le plan de services pour l’hébergement de l’application web. Vous indiquez le nom du plan via le paramètre **hostingPlanName** . L’emplacement du plan est le même emplacement utilisé pour le groupe de ressources. La taille de la couche et collaborateur tarification sont spécifiés dans les paramètres de **référence (SKU)** et **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

