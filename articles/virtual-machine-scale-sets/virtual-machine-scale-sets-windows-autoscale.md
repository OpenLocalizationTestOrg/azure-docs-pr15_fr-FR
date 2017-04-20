<properties
    pageTitle="Échelle Windows Machine virtuelle échelle définit | Microsoft Azure"
    description="Configurer l’autoscaling pour échelle de Machine virtuelle Windows définie à l’aide de PowerShell Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Ajuster automatiquement machines dans un jeu d’échelle machine virtuelle

Machine virtuelle échelle jeux facilitent la déployer et gérer des machines virtuelles identiques comme un ensemble. Jeux d’échelle fournissent un calque cluster très scalable et personnalisables pour les applications de hyperscale, et ils prennent en charge les images de plateforme Windows, des images de plateforme Linux, des images personnalisées et des extensions. Pour plus d’informations sur les ensembles d’échelle, consultez [Jeux d’échelle Machine virtuelle](virtual-machine-scale-sets-overview.md).

Ce didacticiel montre comment créer un ensemble d’échelle de machines virtuelles Windows et ajuster automatiquement les ordinateurs dans l’ensemble. Vous créez l’échelle de définir et configurer la mise à l’échelle en créant un modèle Azure le Gestionnaire de ressources et de déploiement à l’aide de PowerShell Azure. Pour plus d’informations sur les modèles, voir [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md). Pour en savoir plus sur l’échelle automatique des jeux d’échelle, voir [mise à l’échelle automatique et jeux d’échelle Machine virtuelle](virtual-machine-scale-sets-autoscale-overview.md).

Dans cet article, vous déployez les ressources et les extensions suivantes :

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Pour plus d’informations sur les ressources du Gestionnaire de ressources, voir [Azure calculer, réseau et des fournisseurs de stockage sous le Gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

## <a name="step-1-install-azure-powershell"></a>Étape 1 : Installer PowerShell Azure

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à Azure.

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Étape 2 : Créer un groupe de ressources et un compte de stockage

1. **Créer un groupe de ressources** – toutes les ressources doivent être déployés sur un groupe de ressources. [Nouveau AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) permet de créer un groupe de ressources nommé **vmsstestrg1**.

2. **Créer un compte de stockage** – ce compte de stockage est l’endroit où le modèle est stocké. [Nouveau AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) permet de créer un compte de stockage nommé **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Étape 3 : Créer le modèle
Un modèle de gestionnaire de ressources Azure rend possible pour déployer et gérer les ressources Azure entre eux à l’aide d’une description JSON des ressources et des paramètres de déploiement associé.

1. Dans votre éditeur préféré, créez le fichier C:\VMSSTemplate.json et ajoutez la structure initiale JSON pour prendre en charge le modèle.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Paramètres ne sont pas toujours nécessaires, mais elles permettent de valeurs d’entrée lorsque le modèle est déployé. Ajoutez ces paramètres sous l’élément parent paramètres que vous avez ajouté au modèle.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Un nom pour la machine virtuelle séparée qui est utilisé pour accéder aux ordinateurs sur l’échelle de la valeur.
    - Le nom du compte de stockage où se trouve le modèle.
    - Le nombre de machines virtuelles créer initialement dans l’ensemble d’échelle.
    - Le nom et le mot de passe du compte administrateur sur les ordinateurs virtuels.
    - Un préfixe de nom pour les ressources qui sont créés pour prendre en charge le jeu d’échelle.

3. Variables peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètre. Ajoutez ces variables sous l’élément parent variables que vous avez ajouté au modèle.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - Noms DNS qui sont utilisés par les interfaces réseau.
    - Les noms d’adresses IP et les préfixes pour le réseau virtuel et sous-réseaux.
    - Les noms et les identificateurs du réseau virtuel, chargez équilibrage et interfaces réseau.
    - Définir les noms des comptes pour les comptes associés avec les ordinateurs de l’échelle de stockage.
    - Paramètres pour l’extension de Diagnostics est installé sur les ordinateurs virtuels. Pour plus d’informations sur l’extension Diagnostics, voir [créer une machine virtuelle Windows avec la surveillance et les diagnostics de l’aide d’Azure Gestionnaire de ressources du modèle](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Ajoutez la ressource de compte de stockage sous l’élément parent de ressources que vous avez ajouté au modèle. Ce modèle utilise une boucle pour créer les cinq comptes de stockage recommandée où sont stockées les disques du système d’exploitation et les données de diagnostic. Cet ensemble de comptes peut prendre en charge jusqu'à 100 machines virtuelles dans un jeu d’échelle, ce qui correspond au maximum actuel. Chaque compte de stockage est nommé avec un indicateur de lettre a été défini dans les variables combinés avec le préfixe que vous entrez dans les paramètres pour le modèle.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Ajoutez la ressource réseau virtuel. Pour plus d’informations, voir [Fournisseur de ressources réseau](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Ajoutez les ressources adresse IP publics qui sont utilisés par l’équilibrage de charge et l’interface réseau.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Ajoutez la ressource d’équilibrage de charge qui est utilisée par le jeu d’échelle. Pour plus d’informations, consultez [Prise en charge du Gestionnaire de ressources Azure pour équilibrage de charge](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Ajoutez la ressource d’interface réseau utilisé par la machine virtuelle distincte. Étant donné que machines dans un jeu d’échelle ne sont pas accessibles via une adresse IP publique, un ordinateur virtuel distinct est créé dans le même réseau virtuel pour l’accès à distance les ordinateurs.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Ajoutez la machine virtuelle distincte dans le même réseau que le jeu d’échelle.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
              ]
            }
          }
        },

10. Ajoutez que l’échelle machine virtuelle définition de la ressource et spécifiez l’extension de diagnostics est installée sur tous les ordinateurs virtuels dans l’ensemble d’échelle. La plupart des paramètres pour cette ressource sont semblables à la ressource machine virtuelle. Les principales différences sont l’élément de capacité qui spécifie le nombre de machines virtuelles dans l’ensemble de l’échelle et upgradePolicy indiquant comment les mises à jour sont apportées aux machines virtuelles. Le jeu d’échelle n’a pas été créé jusqu'à ce que tous les comptes de stockage sont créés comme indiqué par l’élément dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Ajoutez la ressource autoscaleSettings qui définit comment l’ensemble d’échelle s’ajuste en fonction de l’utilisation du processeur sur les ordinateurs de l’ensemble d’échelle.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Pour ce didacticiel, ces valeurs sont importants :

    - **metricName** - cette valeur est différente de celle du compteur de performance que nous avons définie dans la variable wadperfcounter. À l’aide de cette variable, l’extension Diagnostics collecte la **processeur (_Total)\% temps processeur** compteur.
    - **metricResourceUri** - cette valeur est l’identificateur de ressources de l’ensemble d’échelle machine virtuelle.
    - **timeGrain** – cette valeur est la précision des mesures qui sont collectées. Dans ce modèle, il est défini sur une minute.
    - **statistique** – cette valeur détermine la façon dont les mesures sont combinées afin de s’adapter à l’action de mise à l’échelle automatique. Les valeurs possibles sont : moyenne, Min, Max. Dans ce modèle, la moyenne de l’UC total des machines virtuelles est collectée.
    - **durée** – cette valeur est l’intervalle de temps dans lequel les données de l’instance sont collectées. Il doit être comprise entre 5 minutes et 12 heures.
    - **timeAggregation** – cette valeur détermine la façon dont les données sont collectées doivent être combinées au fil du temps. La valeur par défaut est moyenne. Les valeurs possibles sont : moyenne, Minimum, Maximum, dernier, Total, de nombre.
    - l' **opérateur** – cette valeur est l’opérateur qui est utilisé pour comparer les données métriques et le seuil. Les valeurs possibles sont : est égal à, NotEquals, supérieur, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **seuil** – cette valeur est la valeur déclenchant l’action d’échelle. Dans ce modèle, machines sont ajoutés à l’échelle de la valeur lorsque la moyenne de l’UC entre les ordinateurs dans l’ensemble est supérieure à 50 %.
    - **direction** – cette valeur détermine l’action qui est exécutée lorsque la valeur de seuil est atteint. Les valeurs possibles sont augmenter ou diminuer. Dans ce modèle, le nombre de machines virtuelles dans l’ensemble d’échelle est augmenté si le seuil est supérieure à 50 % dans la fenêtre de temps défini.
    - **type** – cette valeur est le type d’action qui doit avoir lieu et doit être définie sur ChangeCount.
    - **valeur** – cette valeur est le nombre de machines virtuelles qui sont ajoutés ou supprimés à partir du jeu d’échelle. Cette valeur doit être 1 ou supérieure. La valeur par défaut est 1. Dans ce modèle, le nombre d’ordinateurs sur l’échelle de jeu augmente par 1 lorsque le seuil est atteint.
    - **ralentissement** – cette valeur est la quantité de délai d’attente depuis la dernière action mise à l’échelle avant la prochaine action se produit. Cette valeur doit être comprise entre une minute et une semaine.

12. Enregistrez le fichier de modèle.    

## <a name="step-4-upload-the-template-to-storage"></a>Étape 4 : Télécharger le modèle de stockage

Le modèle peut être téléchargé dans la mesure où vous connaissez le nom et la clé primaire du compte de stockage que vous avez créé à l’étape 1.

1.  Dans la fenêtre Microsoft Azure PowerShell, définissez une variable qui indique le nom du compte de stockage que vous avez créé à l’étape 1.

            $storageAccountName = "vmstestsa"

2.  Définir une variable qui spécifie la clé primaire du compte de stockage.

            $storageAccountKey = "<primary-account-key>"

    Vous pouvez obtenir cette touche en cliquant sur l’icône de clé lors de l’affichage de la ressource de compte de stockage dans le portail Azure.

3.  Créez l’objet de contexte de compte de stockage utilisé pour valider les opérations avec le compte de stockage.

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  Créez le conteneur pour stocker le modèle.

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  Téléchargez le fichier de modèle sur le nouveau conteneur.

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>Étape 5 : Déployer le modèle

À présent que vous avez créé le modèle, vous pouvez commencer à déployer les ressources. Utilisez cette commande pour démarrer le processus :

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Lorsque vous appuyez sur entrer, vous êtes invité à fournir des valeurs pour les variables que vous avez affecté. Fournir les valeurs suivantes :

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

Il doit prendre environ 15 minutes pour toutes les ressources à déployer avec succès.

>[AZURE.NOTE] Vous pouvez également utiliser la fonctionnalité permettant du portail pour déployer les ressources. Utilisez ce lien : « https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>»

## <a name="step-6-monitor-resources"></a>Étape 6 : Contrôler les ressources

Vous pouvez obtenir des informations sur les jeux d’échelle machine virtuelle à l’aide de ces méthodes :

 - Le portail Azure - actuellement accessible limitée des informations à l’aide du portail.
 - L' [Explorateur de ressources Azure](https://resources.azure.com/) - cet outil est la meilleure pour l’exploration de l’état actuel de votre jeu d’échelle. Suivez ce chemin d’accès et vous devriez voir la vue d’instance de l’ensemble d’échelle que vous avez créé :

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell - Utilisez cette commande pour obtenir des informations :

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Se connecter à la machine virtuelle distincte comme vous le feriez pour toute autre machine et vous pouvez ensuite accéder à distance les machines virtuelles dans l’échelle défini pour contrôler les processus individuels.

>[AZURE.NOTE] Une API REST complète pour obtenir des informations sur les ensembles d’échelle se trouvent dans [Machine virtuelle échelle définit](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Étape 7 : Supprimer les ressources

Étant donné que vous êtes chargé des ressources utilisées dans Azure, il est toujours judicieux de supprimer les ressources qui ne sont plus utiles. Vous n’avez pas besoin de supprimer séparément chaque ressource à partir d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources et toutes ses ressources sont automatiquement supprimés.

    Remove-AzureRmResourceGroup -Name vmsstestrg1

Si vous voulez conserver votre groupe de ressources, vous pouvez supprimer l’échelle uniquement à la définition.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>Étapes suivantes

- Gérer le jeu d’échelle que vous venez de créer en utilisant les informations dans la [Gestion des machines virtuelles dans un jeu d’échelle de Machine virtuelle](virtual-machine-scale-sets-windows-manage.md).
- En savoir plus sur la mise à l’échelle verticale en passant en revue [échelle verticale avec des jeux d’échelle Machine virtuelle](virtual-machine-scale-sets-vertical-scale-reprovision.md)
- Rechercher des exemples d’Azure moniteur fonctionnalités d’analyse dans [Azure moniteur PowerShell rapide démarrer exemples](../monitoring-and-diagnostics/insights-powershell-samples.md)
- En savoir plus sur les fonctionnalités de notification [d’utiliser les actions d’échelle pour envoyer des messages électroniques et webhook notifications d’alerte dans le moniteur Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Découvrez comment [utiliser audit enregistre pour envoyer des messages électroniques et webhook notifications alertes moniteur d’Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
