<properties
   pageTitle="À l’aide de vous le souhaitez état Configuration avec des jeux d’échelle Machine virtuelle | Microsoft Azure"
   description="À l’aide de la Machine virtuelle échelle définit avec l’Extension DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>À l’aide de la Machine virtuelle échelle définit avec l’Extension DSC Azure

[Machine virtuelle échelle jeux (VMSS)](virtual-machine-scale-sets-overview.md) peut être utilisé avec le Gestionnaire d’extension [Azure souhaité état Configuration (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) . VMSS fournit un moyen de déployer et gérer un grand nombre de machines virtuelles et peut évoluer ELASTIQUEMENT et arrière en réponse à charger. DSC est utilisé pour configurer les ordinateurs virtuels dès qu’elles sont en ligne afin qu’ils exécutent le logiciel de production.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Différences entre le déploiement machine virtuelle et VMSS

La structure de modèle sous-jacente pour VMSS est légèrement différente à partir d’un ordinateur virtuel unique. Plus précisément, un seul ordinateur virtuel déploie extensions sous le nœud « desmachines virtuelles ». Il existe une entrée de type « extensions » où DSC est ajouté au modèle

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Un nœud VMSS possède une section « Propriétés » par le « VirtualMachineProfile », « extensionProfile » attribut. DSC est ajouté sous « extensions »

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Comportement des VMSS

Le comportement de VMSS est identique à celui d’un seul ordinateur virtuel. Lorsqu’un nouvel ordinateur virtuel est créé, il est configuré automatiquement avec l’extension DSC. Si une version plus récente de la WMF est requis par l’extension, la machine virtuelle redémarre avant bientôt en ligne. Une fois qu’il est en ligne, il télécharge DSC configuration .zip et mettre en service sur l’ordinateur virtuel. Vous trouverez plus d’informations dans [La vue d’ensemble de l’Extension de DSC Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Étapes suivantes ##
Examinez le [modèle Azure le Gestionnaire de ressources pour l’extension DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Découvrez comment l' [extension DSC gère en toute sécurité les informations d’identification](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Pour plus d’informations sur le Gestionnaire d’extension DSC Azure, voir [Introduction au gestionnaire d’extension Configuration de l’état Azure vous le souhaitez](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


