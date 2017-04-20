<properties
    pageTitle="Déployer une application web à l’aide de MSDeploy avec certificat hostname et ssl"
    description="Utiliser un modèle Azure le Gestionnaire de ressources pour déployer une application web à l’aide de MSDeploy et en configurant hostname personnalisé et un certificat SSL"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Déployer une application web avec MSDeploy, nom d’hôte personnalisé et un certificat SSL

Ce guide via la création d’un déploiement de bout en bout pour une application Web Azure, exploiter les MSDeploy ainsi que l’ajout un nom d’hôte personnalisé et un certificat SSL au modèle processeur.

Pour plus d’informations sur la création de modèles, voir [Création de modèles de gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Créer l’exemple d’Application

Vous déployez une application web ASP.NET. La première étape consiste à créer une application web simple (ou vous pouvez choisir d’utiliser une existante, auquel cas, vous pouvez ignorer cette étape).

Ouvrir Visual Studio 2015 et sélectionnez Fichier > Nouveau projet. Dans la boîte de dialogue qui s’affiche, sélectionnez Web > Application Web ASP.NET. Sous modèles choisissez Web et choisissez le modèle MVC. Sélectionnez _Modifier le type d’authentification_ à _Aucune authentification_. Il s’agit simplement pour simplifier l’exemple d’application.

À ce stade, vous aurez accès une application de web ASP.Net base prête à utiliser dans le cadre de votre processus de déploiement.

###<a name="create-msdeploy-package"></a>Créer le package MSDeploy

Étape suivante consiste à créer le package pour déployer l’application web sur Azure. Pour ce faire, enregistrez votre projet, puis exécutez les éléments suivants à partir de la ligne de commande :

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Cela créera un package zip sous le dossier PackageLocation. L’application est désormais prête à être déployé, dont vous pouvez à présent générer un modèle de gestionnaire de ressources Azure à le faire.

###<a name="create-arm-template"></a>Créer le modèle de processeur
Tout d’abord, nous allons commencer avec un modèle de processeur simple qui permet de créer une application web et un plan d’hébergement (Notez que les paramètres et les variables ne sont pas affichées pour raisons de concision).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Ensuite, vous devez modifier la ressource de l’application web pour prendre une ressource MSDeploy imbriquée. Cela vous permettra à référence le package créé précédemment et indiquez le Gestionnaire de ressources Azure utiliser MSDeploy pour déployer le package sur le WebApp Azure. La figure suivante montre la ressource Microsoft.Web/sites avec la ressource MSDeploy imbriquée :

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Désormais, vous remarquerez que la ressource MSDeploy prend une propriété **packageUri** qui est définie comme suit :

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Cette **packageUri** prend l’uri de compte de stockage qui pointe vers le compte de stockage dans lequel vous allez télécharger votre zip package pour. Le Gestionnaire de ressources Azure exploiter les [Partagés accès Signatures](../storage/storage-dotnet-shared-access-signature-part-1.md) pour développé le package localement à partir du compte de stockage lorsque vous déployez le modèle. Ce processus sera automatisé via un PowerShell script qui télécharge le package, puis appelez l’API de gestion Azure pour créer les touches requises et transfèrent ces informations dans le modèle en tant que paramètres (*_artifactsLocation* et *_artifactsLocationSasToken*). Vous devez définir des paramètres pour le dossier et nom de fichier du package est téléchargé dans sous le conteneur de stockage.

Vous devez ensuite ajouter dans une autre ressource imbriquée pour configurer les liaisons hostname pour tirer parti d’un domaine personnalisé. Vous devez tout d’abord vous assurer que vous êtes propriétaire du nom d’hôte et configurez-la à être vérifié par Azure que vous êtes propriétaire, voir [configurer un nom de domaine personnalisé dans le Service d’application Azure](web-sites-custom-domain-name.md). Une fois que cela est fait, vous pouvez ajouter les éléments suivants à votre modèle sous la section ressource Microsoft.Web/sites :

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Enfin, vous devez ajouter une autre ressource au niveau supérieure, Microsoft.Web/certificates. Cette ressource contiendra votre certificat SSL et se trouvent au même niveau que votre application web et offre d’hébergement.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Vous devez disposer d’un certificat SSL valide afin de configurer cette ressource. Une fois que vous avez ce certificat valide vous devez extraire les octets pfx sous forme de chaîne en base 64. Une option permettant d’extraire cela consiste à utiliser la commande PowerShell suivante :

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Vous pourriez ensuite le passer en tant que paramètre à votre modèle de déploiement processeur.

À ce stade le modèle processeur est prêt.

###<a name="deploy-template"></a>Modèle de déploiement

Les étapes finales sont à assembler tous les éléments dans un déploiement complète de bout en bout. Pour faciliter le déploiement que vous pouvez tirer parti du script PowerShell **Déployer AzureResourceGroup.ps1** qui est ajouté lorsque vous créez un projet de groupe de ressources Azure dans Visual Studio pour vous aider à télécharger de n’importe quel objets requis dans le modèle. Il nécessite que vous avez créé un compte de stockage que vous souhaitez utiliser à l’avance. Dans cet exemple, j’ai créé un compte de stockage partagée pour la package.zip à télécharger. Le script exploiter les AzCopy pour télécharger le package sur le compte de stockage. Vous passez votre emplacement de dossier d’objet et le script sont-elles automatiquement tous les fichiers dans le répertoire au conteneur de stockage nommé. Après avoir appelé déployer AzureResourceGroup.ps1 vous devez mettre à jour les liaisons SSL pour faire correspondre le nom d’hôte personnalisé avec votre certificat SSL.

La session PowerShell suivante montre le déploiement complet appelant la déployer-AzureResourceGroup.ps1 :

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

À ce stade votre application doit être déployée et vous devriez pouvoir pour y accéder via https://www.yourcustomdomain.com
