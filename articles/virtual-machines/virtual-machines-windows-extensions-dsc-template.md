<properties
   pageTitle="Modèle de gestionnaire de ressources état Configuration vous le souhaitez | Microsoft Azure"
   description="Définition du modèle de gestionnaire de ressources de Configuration de l’état souhaité dans Azure avec des exemples et de dépannage"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS et Configuration de l’état souhaité avec les modèles de gestionnaire de ressources Azure
Cet article décrit le modèle de gestionnaire de ressources pour le [Gestionnaire d’extension de Configuration de l’état souhaité](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Exemple de modèle pour une machine virtuelle Windows

L’extrait suivant est placé dans la section de ressources du modèle.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Exemple de modèle pour Windows VMSS

Un nœud VMSS possède une section « Propriétés » par le « VirtualMachineProfile », « extensionProfile » attribut. DSC est ajouté sous « extensions ». 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
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

## <a name="detailed-settings-information"></a>Informations sur les paramètres détaillées

Le schéma suivant s’applique à la partie paramètres de l’extension DSC Azure dans un modèle de gestionnaire de ressources Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Plus d’informations
| Nom de la propriété | Type | Description |
| --- | --- | --- |
| settings.wmfVersion | chaîne | Spécifie la version de Windows Management Framework doivent être installées sur votre ordinateur virtuel. Définir cette propriété sur « dernière » installe la version la plus à jour de WMF. Les valeurs possibles uniquement en cours de cette propriété sont **'4.0', '5.0', « 5.0PP' et 'plus tard'**. Ces valeurs possibles sont soumis aux mises à jour. La valeur par défaut est « dernière ».|
| Settings.Configuration.URL | chaîne | Indique l’emplacement de l’URL à partir duquel télécharger votre fichier zip de configuration DSC. Si l’URL fournie requiert un jeton associations de sécurité pour l’accès, vous devez définir la propriété protectedSettings.configurationUrlSasToken à la valeur du jeton associations de sécurité. Cette propriété est requise si settings.configuration.script et/ou settings.configuration.function sont définis. |
| Settings.Configuration.script | chaîne | Spécifie le nom du fichier de script qui contient la définition de votre configuration DSC. Ce script doit être placé dans le dossier racine du fichier zip téléchargé à partir de l’URL spécifiée par la propriété configuration.url. Cette propriété est requise si settings.configuration.url et/ou settings.configuration.script sont définis. |
| Settings.Configuration.Function | chaîne | Spécifie le nom de votre configuration DSC. La configuration nommé doit figurer dans le script défini par configuration.script. Cette propriété est requise si settings.configuration.url et/ou settings.configuration.function sont définis. |
| settings.configurationArguments | Collection de sites | Définit les paramètres que vous voulez passer à votre configuration DSC. Cette propriété n’est pas chiffrée. |
| settings.configurationData.url | chaîne | Spécifie l’URL à partir duquel télécharger votre fichier de configuration de données (.pds1) à utiliser comme entrée pour votre configuration DSC. Si l’URL fournie requiert un jeton associations de sécurité pour l’accès, vous devez définir la propriété protectedSettings.configurationDataUrlSasToken à la valeur du jeton associations de sécurité.|
| settings.privacy.dataEnabled | chaîne | Active ou désactive la collecte de télémétrie. Sont les seules valeurs possibles pour cette propriété **'Activer', 'Désactiver', '', ou $null**. Laisser cette propriété vide ou null permet de télémétrie. La valeur par défaut est « ». [Plus d’informations](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Collection de sites | Définit les emplacements secondaires à partir duquel télécharger le WMF. [Plus d’informations](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Collection de sites | Définit les paramètres que vous voulez passer à votre configuration DSC. Cette propriété est chiffrée. |
| protectedSettings.configurationUrlSasToken | chaîne | Spécifie le jeton associations de sécurité pour accéder à l’URL définie par configuration.url. Cette propriété est chiffrée. |
| protectedSettings.configurationDataUrlSasToken | chaîne | Spécifie le jeton associations de sécurité pour accéder à l’URL définie par configurationData.url. Cette propriété est chiffrée. |

## <a name="settings-vs-protectedsettings"></a>Paramètres et ProtectedSettings
Tous les paramètres sont enregistrées dans un fichier texte paramètres sur l’ordinateur virtuel.
Propriétés sous « paramètres » sont des propriétés publiques, car ils sont chiffrés pas dans le fichier texte paramètres.
Propriétés sous « protectedSettings » sont chiffrées avec un certificat et ne sont pas affichées en texte brut dans ce fichier sur l’ordinateur virtuel.

Si la configuration a besoin d’informations d’identification, ils peuvent être inclus dans protectedSettings :

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Exemple

L’exemple suivant est dérivée de la section « Mise en route » de la [page Présentation du Gestionnaire d’Extension DSC](virtual-machines-windows-extensions-dsc-overview.md).
Cet exemple utilise le Gestionnaire de ressources modèles au lieu des applets de commande pour déployer l’extension. Enregistrer la configuration de « IisInstall.ps1 », puis insérez-la dans une. Fichier ZIP et téléchargez le fichier dans une URL accessible. Cet exemple utilise le stockage blob Azure, mais il est possible de télécharger. COMPRESSER des fichiers à partir de n’importe quel endroit arbitraire.

Dans le modèle Azure le Gestionnaire de ressources, le code suivant indique la machine virtuelle pour télécharger le fichier correct et exécuter la fonction PowerShell appropriée :

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Mise à jour à partir de l’ancien Format
Tous les paramètres dans l’ancien format (contenant les propriétés publiques ModulesUrl, ConfigurationFunction, SasToken ou propriétés) automatiquement s’adapter au format actuel et exécuter la même manière qu’avant.

Le schéma suivant est quels le schéma paramètres précédents étaient :

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Voici comment l’ancien format s’adapte à celles-ci au format actuel :

| Nom de la propriété | Équivalent au schéma précédent |
| --- | --- |
| settings.wmfVersion | Paramètres. WMFVersion |
| Settings.Configuration.URL | Paramètres. ModulesUrl |
| Settings.Configuration.script | Première partie de paramètres. ConfigurationFunction (avant '\\\\») |
| Settings.Configuration.Function | Deuxième partie des paramètres. ConfigurationFunction (après '\\\\») |
| settings.configurationArguments | Paramètres. Propriétés |
| settings.configurationData.url | protectedSettings.DataBlobUri (sans jeton d’associations de sécurité) |
| settings.privacy.dataEnabled | Paramètres. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | Paramètres. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | Paramètres. SasToken |
| protectedSettings.configurationDataUrlSasToken | Jeton associations de sécurité à partir de protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Résolution des problèmes - Code d’erreur 1100
Code d’erreur 1100 indique qu’il existe un problème avec l’entrée d’utilisateur à l’extension DSC.
Le texte de ces erreurs est variable et peut changer.
Voici quelques-unes des erreurs que vous pouvez rencontrer et comment les corriger.

### <a name="invalid-values"></a>Valeurs non valides
« Privacy.dataCollection est {0}. Les seules valeurs possibles sont « », « Activer » et 'Désactiver' » « WmfVersion sont {0}. Uniquement les valeurs possibles sont... et « dernière » »

Problème : Une valeur fournie n’est pas autorisée.

Solution : Modifiez la valeur non valide à une valeur valide. Voir le tableau dans la section Détails.

### <a name="invalid-url"></a>URL non valide
« ConfigurationData.url est {0}. Ce n’est pas une URL valide » « DataBlobUri sont {0}. Ce n’est pas une URL valide » « Configuration.url sont {0}. Ce n’est pas une URL valide »

Problème : A fourni QU'URL n’est pas valide.

Solution : Vérifiez toutes vos URL fourni. Vérifiez que toutes les URL résoudre à un emplacement correct pour que l’extension accessible à partir de l’ordinateur distant.

### <a name="invalid-configurationargument-type"></a>Type de ConfigurationArgument non valide
« Entrez les configurationArguments non valides {0} »

Problème : La propriété ConfigurationArguments ne peut pas résoudre à un objet de la table de hachage. 

Solution : Vérifiez votre propriété ConfigurationArguments une table de hachage. Suivez le format prévu dans l’exemple précédent. Attention devis, des virgules et des accolades.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments en double
« Trouvé arguments en double '{0}' dans configurationArguments publics et protégés »

Problème : Les ConfigurationArguments dans les paramètres publics et le ConfigurationArguments dans les paramètres protégées contiennent des propriétés du même nom.

Solution : Supprimez l’une des propriétés en double.

### <a name="missing-properties"></a>Propriétés manquantes
« Configuration.function nécessite que configuration.url ou configuration.module est spécifié »

« Configuration.url nécessite que configuration.script soit spécifié »

« Configuration.script nécessite que configuration.url soit spécifié »

« Configuration.url nécessite que configuration.function soit spécifié »

« ConfigurationUrlSasToken nécessite que configuration.url soit spécifié »

« ConfigurationDataUrlSasToken nécessite que configurationData.url soit spécifié »

Problème : Une propriété définie a besoin d’une autre propriété est manquante.

Solutions : 
- Fournir la propriété manquante.
- Supprimez la propriété nécessitant la propriété manquante.


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur DSC et jeux d’échelle machine virtuelle dans [à l’aide de Machine virtuelle échelle définit avec l’Extension DSC Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Trouver plus d’informations sur la [Gestion des informations d’identification sécurisée de DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Pour plus d’informations sur le Gestionnaire d’extension DSC Azure, voir [Introduction au gestionnaire d’extension Configuration de l’état Azure vous le souhaitez](virtual-machines-windows-extensions-dsc-overview.md). 

Pour plus d’informations sur PowerShell DSC, [visitez le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
