<properties
    pageTitle="Application Service API applications métadonnées de génération de découverte et le code de l’API | Microsoft Azure"
    description="Découvrez comment API applications dans le Service d’application Azure utiliser Swagger métadonnées pour faciliter la génération de découverte et le code de l’API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Application Service API applications métadonnées de génération de découverte et le code de l’API 

Prise en charge pour les métadonnées de l’API [2.0 Swagger](http://swagger.io/) intégré à Lanceur d’applications API de Service. Vous n’êtes pas obligé d’utiliser Swagger, mais si vous l’utilisez, vous pouvez tirer parti des fonctionnalités de l’API applications qui facilitent la découverte et la consommation.   

## <a name="swagger-endpoint"></a>Point de terminaison swagger

Vous pouvez spécifier un point de terminaison qui fournit des métadonnées Swagger 2.0 JSON pour une application API dans une propriété de l’application API. Le point de terminaison peut être par rapport à l’URL de base de l’application API ou une URL absolue. URL absolues pouvant pointent vers l’extérieur de l’application API. 

De nombreux clients en aval (par exemple, génération de code de Visual Studio et flux « Ajouter API » PowerApps), l’URL doit être accessible au public (non protégé par utilisateur ou l’authentification du service). Cela signifie que si vous utilisez l’authentification du Service d’application et que vous souhaitez exposer la définition de l’API au sein de votre application elle-même, vous devez utiliser l’option d’authentification qui autorise le trafic anonyme atteindre votre API. Pour plus d’informations, voir [authentification et autorisation pour les applications de l’API de Service application](app-service-api-authentication.md).

### <a name="portal-blade"></a>Carte portail

Dans le [portail Azure](https://portal.azure.com/) l’URL du point de terminaison peut être vu et modifiée dans la carte de **Définition de l’API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propriété Gestionnaire de ressources Azure

Vous pouvez également configurer l’URL de la définition d’API pour une application API à l’aide de [Explorateur des ressources](https://resources.azure.com/) ou des [modèles de Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md) dans les outils de ligne de commande tels que [PowerShell Azure](../powershell-install-configure.md) et [Azure infrastructure du langage commun](../xplat-cli-install.md). 

Dans l' **Explorateur de ressources**, accédez à **abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Web > sites > {votre site} > Configuration > web**, et vous verrez le `apiDefinition` propriété :

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Pour obtenir un exemple d’un modèle de gestionnaire de ressources Azure qui définit la `apiDefinition` propriété, ouvrez le [fichier azuredeploy.json dans l’application d’exemple de liste de tâches](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Recherchez la section du modèle qui ressemble à l’exemple JSON ci-dessus.

### <a name="default-value"></a>Valeur par défaut

Lorsque vous utilisez Visual Studio pour créer une application API, le point de terminaison de la définition de l’API est automatiquement défini sur l’URL de base de l’application API plue `/swagger/docs/v1`. Il s’agit de l’URL par défaut qui utilise le package NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) pour traiter les métadonnées Swagger générée dynamiquement pour un projet d’API Web ASP.NET. 

## <a name="code-generation"></a>Génération de code

Un des avantages d’intégration Swagger dans applications Azure API est génération de code automatique. Les classes client générée facilement l’écrire du code qui appelle une application API.

Vous pouvez générer le code de client pour une application API à l’aide de Visual Studio ou à partir de la ligne de commande. Pour plus d’informations sur la façon de générer des classes client dans Visual Studio pour un projet d’API Web ASP.NET, consultez [prise en main des applications de l’API et ASP.NET](app-service-api-dotnet-get-started.md#codegen). Pour plus d’informations sur la façon d’y parvenir à partir de la ligne de commande pour toutes les langues prises en charge, consultez le fichier Lisez-moi du référentiel [Azure/autorest](https://github.com/azure/autorest) sur GitHub.com.
 
## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel qui vous guide dans la création, le déploiement et par d’autres programmes une application API, voir [prise en main des applications de l’API dans le Service d’application Azure](app-service-api-dotnet-get-started.md).

Si vous utilisez la gestion de l’API Azure avec l’API applications, vous pouvez utiliser Swagger métadonnées pour importer votre API dans Gestion de l’API. Pour plus d’informations, voir [comment importer la définition d’une API avec opérations de gestion des API Azure](../api-management/api-management-howto-import-api.md). 
