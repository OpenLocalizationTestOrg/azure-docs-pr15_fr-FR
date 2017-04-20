<properties
    pageTitle="Créer un concentrateur IoT en utilisant un modèle de processeur et c# | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser le Gestionnaire de ressources Azure modèles pour créer un concentrateur IoT avec un programme c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Créer un concentrateur IoT à l’aide d’un programme c# avec un modèle pour le Gestionnaire de ressources Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le Gestionnaire de ressources Azure pour créer et gérer des hubs Azure IoT par programme. Ce didacticiel montre comment utiliser un modèle Azure le Gestionnaire de ressources pour créer un concentrateur IoT à partir d’un programme c#.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md).  Cet article décrit l’utilisation du modèle de déploiement d’Azure le Gestionnaire de ressources.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.
- Un [compte de stockage Azure] [ lnk-storage-account] où vous pouvez stocker vos fichiers de modèle Azure le Gestionnaire de ressources.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Visual c# Windows à l’aide du modèle de projet **Application Console** . Nommez le projet **CreateIoTHub**.

2. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, puis sur **Gérer les Packages NuGet**.

3. Dans le Gestionnaire de Package Nuget, vérifiez **incluent la version préliminaire**et recherchez **Microsoft.Azure.Management.ResourceManager**. Cliquez sur **l’installation**, dans **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **accepter** pour accepter les licences.

4. Dans le Gestionnaire de Package Nuget, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Cliquez sur **l’installation**, dans **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **accepter** pour accepter la licence.

5. Dans Program.cs, remplacez les instructions **à l’aide** d’existant avec les éléments suivants :

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. Dans Program.cs, ajoutez les variables statiques suivantes remplacer les valeurs d’espace réservé. Vous avez noté **ApplicationId**, **SubscriptionId**, **TenantId**et **mot de passe** précédemment dans ce didacticiel. **Nom de compte de votre stockage Azure** est le nom du compte de stockage Azure où vous stockez vos fichiers de modèle Azure le Gestionnaire de ressources. **Nom du groupe ressource** est le nom du groupe de ressources que vous utilisez lorsque vous créez le IoT Hub, il peut être un groupe de ressources existant ou un nouvel identifiant. **Nom du déploiement** est un nom pour le déploiement, par exemple **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Envoyer un modèle Azure le Gestionnaire de ressources pour créer un concentrateur IoT

Utiliser un fichier de modèle et de paramètre JSON pour créer un concentrateur IoT dans votre groupe de ressources. Vous pouvez également utiliser un modèle de gestionnaire de ressources Azure pour apporter des modifications à un concentrateur IoT existant.

1. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis cliquez sur **Nouvel élément**. Ajouter un fichier JSON appelé **template.json** à votre projet.

2. Remplacez le contenu de **template.json** par la définition de la ressource suivante pour ajouter un concentrateur IoT standard à la région **Extrême-Orient US** (pour la liste actuelle des régions qui prennent en charge IoT concentrateur voir [État Azure][lnk-status]) :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, cliquez sur **Ajouter**, puis cliquez sur **Nouvel élément**. Ajouter un fichier JSON appelé **parameters.json** à votre projet.

4. Remplacez le contenu de **parameters.json** avec les informations de paramètre suivantes qui définit un nom pour le nouveau concentrateur IoT telle que **{vos initiales} mynewiothub**. Notez que le nom de concentrateur IoT doit être unique afin qu’il doit inclure votre nom ou vos initiales) :

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. Dans l' **Explorateur de serveurs**, connectez-vous à votre abonnement Azure et dans votre compte de stockage Azure créer un conteneur appelé **modèles**. Dans le volet de **Propriétés** , définissez les autorisations **Public accès en lecture** pour le conteneur de **modèles** à **Blob**.

6. Dans l' **Explorateur de serveurs**, avec le bouton droit sur le conteneur de **modèles** , puis sur **Mode Blob conteneur**. Cliquez sur le bouton **Télécharger Blob** , sélectionnez deux fichiers, **parameters.json** et **templates.json**, puis cliquez sur **Ouvrir** pour télécharger les fichiers JSON dans le conteneur **modèles** . Les URL des objets BLOB contenant les données JSON sont :

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Ajoutez la méthode suivante à Program.cs :
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Ajoutez le code suivant à la méthode **CreateIoTHub** pour envoyer les fichiers de modèle et de paramètre au Gestionnaire de ressources Azure :

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Ajouter le code suivant à la méthode **CreateIoTHub** qui affiche l’état et des touches pour le nouveau concentrateur IoT :

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Terminer et exécutez l’application

Vous pouvez à présent terminer l’application en appelant la méthode **CreateIoTHub** avant de générer et de l’exécuter.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Cliquez sur **créer** puis **Générer la Solution**. Corrigez les erreurs.

3. Cliquez sur **Déboguer** , puis sur **Démarrer le débogage** pour exécuter l’application. Il peut prendre plusieurs minutes pour le déploiement à exécuter.

4. Vous pouvez vérifier que votre application ajouté le nouveau concentrateur IoT en visitant le [portail Azure] [ lnk-azure-portal] et affichage de votre liste de ressources, ou à l’aide de l’applet de commande PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Cet exemple d’application ajoute un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Vous pouvez supprimer le hub IoT via le [portail Azure] [ lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Supprimer AzureRmResource** lorsque vous avez terminé.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez déployé un concentrateur IoT à l’aide d’un modèle de gestionnaire de ressources Azure avec un programme c#, vous souhaiterez peut-être approfondir :

- En savoir plus sur les fonctionnalités du [fournisseur de ressources IoT concentrateur API REST][lnk-rest-api].
- Lisez la [vue d’ensemble du Gestionnaire de ressources Azure] [ lnk-azure-rm-overview] pour en savoir plus sur les fonctionnalités du Gestionnaire de ressources Azure.

Pour plus d’informations sur le développement pour IoT concentrateur, voir les rubriques suivantes :

- [Introduction au SDK C][lnk-c-sdk]
- [Concentrateur IoT SDK][lnk-sdks]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
