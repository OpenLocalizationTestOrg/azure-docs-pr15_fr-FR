<properties
    pageTitle="Créer un concentrateur IoT à l’aide de l’API REST | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser l’API REST pour créer un concentrateur IoT."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Didacticiel : Créer un concentrateur IoT à l’aide d’un programme c# et l’API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser le [fournisseur de ressources IoT concentrateur API REST] [ lnk-rest-api] pour créer et gérer des hubs Azure IoT par programme. Ce didacticiel montre comment utiliser le fournisseur de ressources IoT concentrateur API REST pour créer un concentrateur IoT à partir d’un programme c#.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md).  Cet article décrit l’utilisation du modèle de déploiement d’Azure le Gestionnaire de ressources.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

- Microsoft Visual Studio 2015.
- Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] ou version ultérieure.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Préparer votre projet Visual Studio

1. Dans Visual Studio, créez un projet Visual c# Windows à l’aide du modèle de projet **Application Console** . Nommez le projet **CreateIoTHubREST**.

2. Dans l’Explorateur de solutions, avec le bouton droit sur votre projet, puis sur **Gérer les Packages NuGet**.

3. Dans le Gestionnaire de Package Nuget, vérifiez **incluent la version préliminaire**et recherchez **Microsoft.Azure.Management.ResourceManager**. Cliquez sur **l’installation**, dans **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **accepter** pour accepter les licences.

4. Dans le Gestionnaire de Package Nuget, recherchez **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Cliquez sur **l’installation**, dans **Réviser les modifications** , cliquez sur **OK**, puis cliquez sur **accepter** pour accepter la licence.

6. Dans Program.cs, remplacez les instructions **à l’aide** d’existant avec les éléments suivants :

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. Dans Program.cs, ajoutez les variables statiques suivantes remplacer les valeurs d’espace réservé. Vous avez noté **ApplicationId**, **SubscriptionId**, **TenantId**et **mot de passe** précédemment dans ce didacticiel. **Nom du groupe ressource** est le nom du groupe de ressources que vous utilisez lorsque vous créez le hub IoT, il peut être un groupe de ressources existant ou un nouvel identifiant. **Nom IoT Hub** est le nom du concentrateur IoT créer, par exemple **MyIoTHub** (ce nom doit être globalement unique, afin qu’il doit inclure votre nom ou vos initiales). **Nom du déploiement** est un nom pour le déploiement, par exemple **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Utiliser l’API REST pour créer un concentrateur IoT

Utiliser l' [API REST de concentrateur IoT] [ lnk-rest-api] pour créer un concentrateur IoT dans votre groupe de ressources. Vous pouvez également utiliser l’API REST pour apporter des modifications à un concentrateur IoT existant.

1. Ajoutez la méthode suivante à Program.cs :
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Ajoutez le code suivant à la méthode **CreateIoTHub** pour créer un objet **HttpClient** avec le jeton d’authentification dans les en-têtes :

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Ajoutez le code suivant à la méthode **CreateIoTHub** pour décrire le hub IoT pour créer et générer une représentation JSON (pour la liste actuelle des emplacements qui prennent en charge IoT concentrateur voir [État Azure][lnk-status]) :

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Ajoutez le code suivant à la méthode **CreateIoTHub** pour soumettre la demande reste à Azure et vérification de la réponse à obtenir l’URL que vous pouvez utiliser pour surveiller l’état de la tâche de déploiement :

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Ajoutez le code suivant à la fin de la méthode **CreateIoTHub** pour utiliser l’adresse **asyncStatusUri** récupérée à l’étape précédente pour patienter pour le déploiement à effectuer :

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Ajoutez le code suivant à la fin de la méthode **CreateIoTHub** pour récupérer les clés du concentrateur IoT vous avez créé et les imprimez à la console :

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Terminer et exécutez l’application

Vous pouvez à présent terminer l’application en appelant la méthode **CreateIoTHub** avant de générer et de l’exécuter.

1. Ajoutez le code suivant à la fin de la méthode **Main** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Cliquez sur **créer** puis **Générer la Solution**. Corrigez les erreurs.

3. Cliquez sur **Déboguer** , puis sur **Démarrer le débogage** pour exécuter l’application. Il peut prendre plusieurs minutes pour le déploiement à exécuter.

4. Vous pouvez vérifier que votre application ajouté le nouveau concentrateur IoT en visitant le [portail Azure] [ lnk-azure-portal] et affichage de votre liste de ressources, ou à l’aide de l’applet de commande PowerShell **Get-AzureRmResource** .

> [AZURE.NOTE] Cet exemple d’application ajoute un concentrateur IoT Standard S1 pour lesquels vous êtes facturé. Lorsque vous avez terminé, vous pouvez supprimer le hub IoT via le [portail Azure] [ lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Supprimer AzureRmResource** lorsque vous avez terminé.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez déployé un concentrateur IoT à l’aide de l’API REST, vous souhaiterez peut-être approfondir :

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
