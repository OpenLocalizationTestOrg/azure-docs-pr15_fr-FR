<properties
    pageTitle="Déployer une machine virtuelle à l’aide d’un modèle de gestionnaire de ressources et c# | Microsoft Azure"
    description="Découvrez comment utiliser c# et un modèle de gestionnaire de ressources pour déployer une machine virtuelle Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Déployer une Machine virtuelle Azure à l’aide d’un modèle de gestionnaire de ressources et c#

À l’aide de modèles et des groupes de ressources, vous ne pouvez gérer toutes les ressources gagnante qui prennent en charge de votre application. Cet article vous explique comment utiliser Visual Studio et c# pour configurer l’authentification, créez un modèle et puis déployer des ressources Azure à l’aide du modèle que vous avez créé.

Vous devez tout d’abord pour vous assurer que vous avez terminé ces étapes de configuration :

- Installation de [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Vérifier l’installation de [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obtenir un [jeton d’authentification](../resource-group-authenticate-service-principal.md)
- Créer un groupe de ressources à l’aide de [PowerShell Azure](../resource-group-template-deploy.md), [Azure infrastructure du langage commun](../resource-group-template-deploy-cli.md)ou [portail Azure](../resource-group-template-deploy-portal.md).

Il prend environ 30 minutes pour effectuer ces étapes.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Étape 1 : Créer le projet Visual Studio, le fichier de modèle et le fichier de paramètres

### <a name="create-the-template-file"></a>Créer le fichier de modèle

Un modèle de gestionnaire de ressources Azure rend possible pour déployer et gérer les ressources Azure ensemble. Le modèle est une description JSON des ressources et des paramètres de déploiement associé.

Dans Visual Studio, procédez comme suit :

1. Cliquez sur **fichier** > **Nouveau** > **projet**.

2. Dans les **modèles** > **Visual c#**, sélectionnez **Application Console**, entrez le nom et l’emplacement du projet, puis cliquez sur **OK**.

3. Le nom du projet dans l’Explorateur de menu contextuel, cliquez sur **Ajouter** > **Un nouvel élément**.

4. Cliquez sur le Web, sélectionnez fichier JSON, entrez *VirtualMachineTemplate.json* pour le nom et puis cliquez sur **Ajouter**.

5. Dans les crochets gauche et du fichier VirtualMachineTemplate.json, ajoutez l’élément de schéma requis et l’élément contentVersion requis :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Paramètres](../resource-group-authoring-templates.md#parameters) ne sont pas toujours nécessaires, mais elles permettent de valeurs d’entrée lorsque le modèle est déployé. Ajoutez l’élément parameters et ses éléments enfants après l’élément contentVersion :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variables](../resource-group-authoring-templates.md#variables) peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètre. Ajoutez l’élément variables après la section Paramètres :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Ressources](../resource-group-authoring-templates.md#resources) telles que la machine virtuelle, le réseau virtuel et du compte de stockage sont définies suivant dans le modèle. Ajoutez la section ressources après la section variables :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Enregistrez le fichier de modèle que vous avez créé.

### <a name="create-the-parameters-file"></a>Créer le fichier de paramètres

Pour spécifier des valeurs pour les paramètres de ressource qui ont été définis dans le modèle, vous créez un fichier de paramètres qui contient les valeurs qui sont utilisés lorsque le modèle est déployé. Dans Visual Studio, procédez comme suit :

1. Le nom du projet dans l’Explorateur de menu contextuel, cliquez sur **Ajouter** > **Un nouvel élément**.

2. Cliquez sur le Web, sélectionnez fichier JSON, entrez *Parameters.json* pour le nom et puis cliquez sur **Ajouter**.

3. Ouvrez le fichier parameters.json, puis ajoutez ce contenu JSON :

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Cet article crée une machine virtuelle exécute une version du système d’exploitation Windows Server. Pour plus d’informations sur la sélection d’autres images, voir [Naviguer et sélectionnez machine virtuelle Azure images avec Windows PowerShell et l’infrastructure du langage commun Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Enregistrez le fichier de paramètres que vous avez créé.

## <a name="step-2-install-the-libraries"></a>Étape 2 : Installez les bibliothèques

Les packages NuGet sont le moyen le plus simple pour installer les bibliothèques dont vous avez besoin pour terminer ce didacticiel. Vous avez besoin de la bibliothèque de gestion des ressources Azure et bibliothèque d’authentification Azure Active Directory pour créer les ressources. Pour obtenir ces bibliothèques dans Visual Studio, procédez comme suit :

1. Cliquez sur le nom du projet dans l’Explorateur de solutions, cliquez sur **Gérer les Packages NuGet**, puis cliquez sur Parcourir.

2. Type *Active Directory* dans la zone de recherche, cliquez sur **installer** pour le package de la bibliothèque d’authentification Active Directory et puis suivez les instructions pour installer le package.

4. En haut de la page, sélectionnez **Inclure la version préliminaire**. Type *Microsoft.Azure.Management.ResourceManager* dans la zone de recherche, cliquez sur **installer** pour les bibliothèques de gestion des ressources de Microsoft Azure et puis suivez les instructions pour installer le package.

Vous êtes maintenant prêt à commencer à utiliser les bibliothèques pour créer votre application.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Étape 3 : Créer les informations d’identification sont utilisées pour authentifier les requêtes

L’application Azure Active Directory est créée et la bibliothèque d’authentification est installée. Maintenant que vous mettez en forme les informations d’application dans les informations d’identification utilisées pour authentifier les requêtes au Gestionnaire de ressources Azure.

1. Ouvrez le fichier Program.cs pour le projet que vous avez créé et ajoutez ces instructions using au haut du fichier :

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Ajoutez cette méthode à la classe de programme pour obtenir le jeton nécessaires pour créer les informations d’identification :

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Remplacez {id client} par l’identificateur de Azure Active Directory application, {client-secret} avec la touche d’accès rapide de l’application AD et {id client} avec l’identificateur de client pour votre abonnement. Vous pouvez trouver l’id de client en exécutant Get-AzureRmSubscription. Vous pouvez trouver la touche d’accès rapide à l’aide du portail Azure.

3. Pour créer les informations d’identification, ajoutez ce code à la méthode Main dans le fichier Program.cs :

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Enregistrez le fichier Program.cs.

## <a name="step-4-deploy-the-template"></a>Étape 4 : Déployez le modèle

Dans cette étape, vous utilisez le groupe de ressources que vous avez créé précédemment, mais vous pouvez également créer un groupe de ressources à l’aide de la [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) et les classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) .

1. Ajouter des variables à la méthode Main de la classe de programme pour spécifier les noms des ressources que vous avez créé précédemment, le nom du déploiement et l’identificateur de votre abonnement :

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Remplacez la valeur de nom de groupe par le nom de votre groupe de ressources. Remplacez la valeur de deploymentName par le nom que vous souhaitez utiliser pour le déploiement. Vous pouvez trouver l’identificateur d’abonnement en exécutant Get-AzureRmSubscription.

2. Ajoutez cette méthode à la classe de programme pour déployer les ressources au groupe de ressources à l’aide du modèle que vous avez défini :

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Si vous voulez déployer le modèle à partir d’un compte de stockage, vous pouvez remplacer la propriété du modèle avec la propriété TemplateLink.

3. Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Étape 5 : Supprimer les ressources

Étant donné que vous êtes chargé des ressources utilisées dans Azure, il est toujours judicieux de supprimer les ressources qui ne sont plus utiles. Vous n’avez pas besoin de supprimer séparément chaque ressource à partir d’un groupe de ressources. Supprimer le groupe de ressources et toutes ses ressources sont automatiquement supprimés.

1.  Pour supprimer le groupe de ressources, ajoutez cette méthode à la classe Program :

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Pour appeler la méthode que vous venez d’ajouter, ajoutez ce code à la méthode Main :

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Étape 6 : Exécutez l’application console

1.  Pour exécuter l’application console, cliquez sur **Démarrer** dans Visual Studio, puis connectez-vous à Azure AD utilisant les mêmes informations d’identification que vous utilisez avec votre abonnement.

2.  Une fois que l’état accepté s’affiche, appuyez sur **entrée** .

    Il doit prendre environ 5 minutes pour cette application console pour s’exécuter à partir du début à fin. Avant d’appuyer sur ENTRÉE pour démarrer la suppression de ressources, vous pouvez prendre quelques minutes pour vérifier la création des ressources dans le portail Azure avant de les supprimer.

3. Pour afficher l’état des ressources, parcourir les journaux d’Audit dans le portail Azure :

    ![Parcourir les journaux d’audit dans le portail Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Étapes suivantes

- S’il y a des problèmes avec le déploiement, une étape suivante serait pour examiner les [déploiements de groupe de ressources de résolution des problèmes avec Azure portail](../resource-manager-troubleshoot-deployments-portal.md).
- Découvrez comment gérer la machine virtuelle que vous avez créé en passant [machines virtuelles gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-csharp-manage.md).
