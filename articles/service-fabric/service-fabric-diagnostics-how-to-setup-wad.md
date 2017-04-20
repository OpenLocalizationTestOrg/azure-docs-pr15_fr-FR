<properties
   pageTitle="Collecte des journaux à l’aide des Diagnostics Azure | Microsoft Azure"
   description="Cet article décrit comment configurer Azure Diagnostics pour collecter les journaux d’un cluster de tissu de Service en cours d’exécution dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Collecte des journaux à l’aide des Diagnostics Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Lorsque vous exécutez un cluster tissu de Service Azure, il est recommandé de collecter les journaux de tous les nœuds dans un emplacement central. Présentant les journaux dans un emplacement central vous permet d’analyser et résoudre les problèmes dans votre cluster ou problèmes dans les applications et services qui s’exécutent dans ce cluster.

Une façon de télécharger et de collecte des journaux consiste à utiliser l’extension Diagnostics Azure, qui télécharge les journaux au stockage Azure. Les journaux ne sont pas utiles directement dans l’espace de stockage. Mais vous pouvez utiliser un processus externe pour lire les événements à partir du stockage et placez-les dans un produit comme [Journal Analytique](../log-analytics/log-analytics-service-fabric.md), [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md)ou une autre solution journal d’analyse.

## <a name="prerequisites"></a>Conditions préalables
Vous allez utiliser ces outils pour effectuer les opérations dans ce document :

* [Diagnostics de Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (liés aux Services Cloud Azure mais contient des exemples et des informations utiles)
* [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell Azure](../powershell-install-configure.md)
* [Azure client Gestionnaire de ressources](https://github.com/projectkudu/ARMClient)
* [Modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Sources du journal que vous pouvez collecter
- **Les journaux tissu de service**: émis à partir de la plate-forme aux canaux de suivi d’événements pour Windows (ETW) et source d’événement standards. Les journaux peuvent s’agir de plusieurs types :
  - Les événements opérationnels : les journaux pour les opérations qui effectue la plateforme tissu de Service. Création d’applications et services, modifications de l’état nœud et informations de mise à niveau des exemples.
  - [Intervenants fiables programmation d’événements d’un modèle](service-fabric-reliable-actors-diagnostics.md)
  - [Services fiables programmation d’événements d’un modèle](service-fabric-reliable-services-diagnostics.md)
- **Événements d’application**: événements émis à partir du code de votre service et écrites à l’aide de la classe d’assistance source d’événement fournie dans les modèles de Visual Studio. Pour plus d’informations sur la façon d’écrire les journaux à partir de votre application, voir [moniteur et diagnostiquer les services dans une configuration de développement ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Déploiement de l’extension Diagnostics
La première étape de collecte des journaux consiste à déployer l’extension Diagnostics sur chacun des ordinateurs virtuels dans le cluster tissu de Service. L’extension Diagnostics collecte des journaux sur chaque ordinateur virtuel et les téléchargements pour le compte de stockage que vous spécifiez. Les étapes varient selon un peu selon que vous utilisez le portail Azure ou le Gestionnaire de ressources Azure. Les étapes également varient en fonction du déploiement fait partie de la création d’un cluster ou qu’ils s’applique à un cluster qui existe déjà. Examinons les étapes pour chaque scénario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster via le portail
Pour déployer l’extension Diagnostics sur les ordinateurs virtuels du cluster dans le cadre de la création d’un cluster, vous utilisez le panneau de paramètres Diagnostics indiqué dans l’image suivante. Pour activer la collecte des événements intervenants fiable ou des Services fiables, assurez-vous que les Diagnostics est définie **sur** (le paramètre par défaut). Après avoir créé le cluster, vous ne pouvez pas modifier ces paramètres à l’aide du portail.

![Paramètres des Diagnostics Azure dans le portail pour la création de cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Le support de *nécessite* l’équipe Azure prise en charge les journaux pour aider à résoudre des demandes de support que vous créez. Ces fichiers journaux est collectées en temps réel et est stockés dans un des comptes de stockage créés dans le groupe de ressources. Les paramètres des Diagnostics configurent des événements au niveau de l’application. Ces événements incluent les événements [Intervenants fiable](service-fabric-reliable-actors-diagnostics.md) , les événements de [Services fiables](service-fabric-reliable-services-diagnostics.md) et certains événements tissu de Service au niveau du système stocké dans le stockage Azure.

Produits, tels que [Recherche élastique](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou votre propre processus accessible les événements à partir du compte de stockage. Ne propose actuellement aucun moyen pour filtrer ou nettoyez les événements qui sont envoyés à la table. Si vous ne la mise en œuvre d’un processus pour supprimer des événements à partir de la table, la table continuent à agrandir.

Lorsque vous créez un cluster à l’aide du portail, nous vous recommandons vivement télécharger le modèle *avant de cliquer sur * *OK*** pour créer le cluster. Pour plus d’informations, reportez-vous à [configurer un cluster tissu de Service à l’aide d’un modèle Azure le Gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md). Vous devez le modèle pour apporter des modifications plus tard, car vous ne pouvez pas apporter des modifications à l’aide du portail.

Vous pouvez exporter des modèles à partir du portail en procédant comme suit. Toutefois, ces modèles peuvent être plus difficiles à utiliser, car ils peuvent contenir des valeurs null lesquels il manquent des informations requises.

1. Ouvrez votre groupe de ressources.
2. Cliquez sur **paramètres** pour afficher le panneau Paramètres.
3. Sélectionnez **les déploiements** pour afficher le panneau de l’historique de déploiement.
4. Sélectionnez un déploiement pour afficher les détails du déploiement.
5. Sélectionnez **Exporter le modèle** pour afficher le panneau de modèle.
6. Cliquez sur **Enregistrer dans un fichier** à exporter un fichier .zip qui contient le modèle, de paramètre et fichiers PowerShell.

Une fois que vous exportez les fichiers, vous devez procéder à une modification. Modifier le fichier parameters.json et supprimez l’élément **adminPassword** . Cela provoquera une invite pour le mot de passe lorsque le script de déploiement est exécuté. Lorsque vous exécutez le script de déploiement, vous devrez peut-être corriger les valeurs de paramètre null.

Pour utiliser le modèle téléchargé à mettre à jour une configuration :

1. Extraire le contenu dans un dossier sur votre ordinateur local.
2. Modifier le contenu pour refléter la nouvelle configuration.
3. Démarrer PowerShell et accédez au dossier où vous avez extrait le contenu.
4. Exécutez **deploy.ps1** et renseigner l’ID de l’abonnement, le nom de groupe de ressources (utilisez le même nom pour mettre à jour la configuration) et un nom unique déploiement.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster à l’aide du Gestionnaire de ressources Azure
Pour créer un cluster à l’aide du Gestionnaire de ressources, vous devez ajouter la configuration de Diagnostics JSON au modèle de gestionnaire de ressources cluster complète avant de créer le cluster. Nous fournissons un exemple de modèle de gestionnaire de ressources machine virtuelle cinq cluster configuration Diagnostics ajoutée dans le cadre de nos exemples de modèles Gestionnaire de ressources. Vous pouvez l’afficher à cet emplacement dans la galerie d’exemples Azure : [cluster à cinq nœuds avec l’exemple de modèle Diagnostics le Gestionnaire de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Pour afficher le paramètre Diagnostics dans le modèle de gestionnaire de ressources, ouvrez le fichier azuredeploy.json et effectuez une recherche **IaaSDiagnostics**. Pour créer un cluster à l’aide de ce modèle, sélectionnez le bouton **déployer vers Azure** disponible sur le lien précédent.

Par ailleurs, vous pouvez télécharger l’exemple de gestionnaire de ressources, y apporter des modifications et créer un cluster avec le modèle modifié à l’aide de la `New-AzureRmResourceGroupDeployment` commande dans une fenêtre PowerShell Azure. Voir le code suivant pour les paramètres que vous passez à la commande. Pour plus d’informations sur le déploiement d’un groupe de ressources à l’aide de PowerShell, voir l’article [déployer un groupe de ressources avec le modèle Azure le Gestionnaire de ressources](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Déploiement de l’extension Diagnostics à un cluster existant
Si vous avez un cluster existant qui n’a pas Diagnostics déployé, ou si vous souhaitez modifier une configuration existante, vous pouvez ajouter ou mettre à jour. Modifier le modèle de gestionnaire de ressources qui est utilisé pour créer le cluster existant ou télécharger le modèle à partir du portail comme décrit plus haut. Modifier le fichier template.json en effectuant les tâches suivantes.

Ajouter une nouvelle ressource de stockage pour le modèle en ajoutant à la section des ressources.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Ensuite, ajoutez à la section Paramètres juste après les définitions de compte de stockage, entre `supportLogStorageAccountName` et `vmNodeType0Name`. Remplacez le texte d’espace réservé *Insérer ici le nom de compte de stockage* par le nom du compte de stockage.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Ensuite, mettre à jour la `VirtualMachineProfile` section du fichier template.json en ajoutant le code suivant dans le tableau extensions. Veillez à ajouter une virgule au début ou à la fin, en fonction de l’endroit où il est inséré.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Une fois que vous modifiez le fichier template.json comme décrit, republiez le modèle de gestionnaire de ressources. Si le modèle a été exporté, l’exécution du fichier deploy.ps1 republie le modèle. Une fois que vous déployez, vérifiez que **ProvisioningState** est **a réussi**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Mettre à jour des Diagnostics pour collecter et télécharger les journaux de nouveaux canaux source d’événement
Pour mettre à jour des Diagnostics pour collecter les journaux de nouveaux canaux source d’événement qui représentent une nouvelle application que vous êtes prêt à déployer, exécutez les mêmes étapes comme dans la [section précédente](#deploywadarm) pour le programme d’installation des Diagnostics pour un cluster existant.

Mettre à jour la `EtwEventSourceProviderConfiguration` section dans le fichier template.json pour ajouter des entrées pour les nouvelles chaînes de source d’événement avant d’appliquer la configuration de mise à jour à l’aide de la `New-AzureRmResourceGroupDeployment` commande PowerShell. Le nom de la source d’événements est défini dans le cadre de votre code dans le fichier ServiceEventSource.cs généré par Visual Studio.

Par exemple, si votre source de l’événement s’appelle mon-source d’événement, ajoutez le code suivant pour placer les événements à partir de mon-source d’événement dans une table nommée MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Pour recueillir des compteurs de performance ou les journaux d’événements, modifiez le modèle de gestionnaire de ressources en utilisant les exemples de [créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide d’un modèle Azure le Gestionnaire de ressources](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Ensuite, republiez le modèle de gestionnaire de ressources.

## <a name="next-steps"></a>Étapes suivantes
Pour mieux comprendre plus en détail les événements que vous devez rechercher lors de la résolution des problèmes, consultez les événements de diagnostic émis pour [Intervenants fiable](service-fabric-reliable-actors-diagnostics.md) et [Services fiables](service-fabric-reliable-services-diagnostics.md).


## <a name="related-articles"></a>Articles connexes
* [Découvrez comment collecter des compteurs de performance ou les journaux à l’aide de l’extension Diagnostics](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Solution de tissu de service dans le journal Analytique](../log-analytics/log-analytics-service-fabric.md)
