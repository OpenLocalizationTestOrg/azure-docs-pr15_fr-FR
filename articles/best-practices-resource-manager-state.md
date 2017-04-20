<properties
    pageTitle="Gestion de l’état dans le Gestionnaire de ressources modèles | Microsoft Azure"
    description="Affiche recommandé approches pour des objets complexes pour partager des données sur l’état avec le Gestionnaire de ressources Azure modèles et les modèles liées"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Partage d’état dans le Gestionnaire de ressources Azure modèles

Cette rubrique indique les meilleures pratiques de gestion et de partage d’état au sein des modèles. Les paramètres et les variables présentées dans cette rubrique sont des exemples de type des objets que vous pouvez définir pour mieux organiser vos besoins en matière de déploiement. À partir de ces exemples, vous pouvez implémenter vos propres objets avec des valeurs de propriété représentatives pour votre environnement.

Cette rubrique fait partie d’un plus grand livre blanc. Pour lire l’intégralité du, téléchargez [Remarques sur le monde classe ressource Gestionnaire modèles et pratiques éprouvées](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Fournir des paramètres de configuration standard

Au lieu de proposer un modèle qui fournit une flexibilité totale et un nombre illimité de variantes, un modèle commun consiste à fournir une sélection de configurations connues. En effet, les utilisateurs peuvent sélectionner les tailles de t-shirt standard tels que sandbox, petite, moyenne et grande. Offres de produits, tels que Communauté edition ou enterprise edition sont d’autres exemples de tailles de t-shirt. Dans les autres cas, il peut être configurations spécifique à la charge d’une technologie – tel que carte réduire ou aucune sql.

Avec les objets complexes, vous pouvez créer des variables qui contiennent des collections de données, parfois appelées « conteneurs de propriétés » et utiliser ces données pour piloter la déclaration de ressources dans votre modèle. Cette approche fournit des configurations connues bonnes de différentes tailles sont préconfigurés pour les clients. Sans configurations connues, les utilisateurs du modèle doivent déterminer cluster de redimensionnement tout seul ?, prendre en compte les contraintes de ressources de plateforme et effectuer des opérations mathématiques pour identifier la partition résultante des comptes de stockage et autres ressources (en raison des contraintes de taille et des ressources cluster). En plus de rendre une meilleure expérience pour le client, quelques configurations connues sont plus faciles à prendre en charge et peuvent vous aider à fournir un niveau plus élevé de densité.

L’exemple suivant montre comment définir des variables qui contiennent des objets complexes pour représenter les ensembles de données. Les collections de définissent les valeurs qui sont utilisées pour la taille de la machine virtuelle, paramètres réseau, les paramètres de système d’exploitation et disponibilité.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Notez que la variable **tshirtSize** concatène la taille de t-shirt que vous fournie via un paramètre (**petite**, **moyenne**, **grande**) pour le texte **tshirtSize**. Vous utilisez cette variable pour récupérer la variable objet complexes associé pour la taille spécifiée t-shirt.

Vous pouvez ensuite référencer ces variables plus loin dans le modèle. La possibilité de référence à des variables nommé et leurs propriétés simplifie la syntaxe de modèle et facilite la comprendre contexte. L’exemple suivant définit une ressource à déployer en utilisant les objets décrite précédemment pour définir les valeurs. Par exemple, la taille de la mémoire virtuelle est définie en récupérant la valeur pour `variables('tshirtSize').vmSize` tandis que la valeur de la taille du disque est extraite `variables('tshirtSize').diskSize`. En outre, l’URI d’un modèle lié est définie avec la valeur de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Passer l’état à un modèle

Vous partagez l’état dans un modèle grâce à des paramètres que vous fournissez directement au cours du déploiement.

Le tableau suivant répertorie les paramètres couramment utilisés dans les modèles.

Nom | Valeur | Description
---- | ----- | -----------
emplacement    | Chaîne à partir d’une liste restreinte des régions Azure   | L’emplacement dans lequel les ressources sont déployés.
storageAccountNamePrefix    | Chaîne    | Nom DNS unique pour le compte de stockage où sont trouvent les disques de l’ordinateur
nom_domaine  | Chaîne    | Nom de domaine de la jumpbox accessible au public machine virtuelle au format : **{nom_domaine}. {} emplacement}.cloudapp.com** par exemple : **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Chaîne    | Nom d’utilisateur pour les ordinateurs virtuels
adminPassword   | Chaîne    | Mot de passe pour les ordinateurs virtuels
tshirtSize  | Chaîne d’une liste de contrainte proposé tailles t-shirt   | La taille d’unité échelle nommée mise en service. Par exemple, « Petite », « moyenne », « Grande »
virtualNetworkName  | Chaîne    | Nom du réseau virtuel que le consommateur souhaite à utiliser.
enableJumpbox   | Chaîne à partir d’une liste restreinte (activé/désactivé) | Paramètre qui identifie s’il faut activer un jumpbox pour l’environnement. Valeurs : « activée », « désactivé »

Le paramètre **tshirtSize** utilisé dans la section précédente se définit comme :

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Passer l’état aux modèles liées

Lors de la connexion aux modèles liées, vous utilisez une combinaison de statique et généré variables.

### <a name="static-variables"></a>Variables statiques

Variables statiques sont souvent utilisés pour fournir des valeurs de base, telles que les URL, qui sont utilisés dans un modèle.

Dans l’extrait de modèle suivant, `templateBaseUrl` indique l’emplacement racine pour le modèle dans GitHub. La ligne suivante crée une nouvelle variable `sharedTemplateUrl` qui concatène l’URL de base avec le nom du modèle de ressources partagées connu. Sous ligne, une variable objet complexe permet de stocker une taille t-shirt, où l’URL de base est concaténée à l’emplacement du modèle configuration connu et stockée dans le `vmTemplate` propriété.

L’avantage de cette approche est que si l’emplacement du modèle est modifiée, vous devez uniquement modifier la variable statique au même endroit, qui passe dans tous les modèles liées.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variables générées

En plus des variables statiques, plusieurs variables sont générées dynamiquement. Cette section présente certaines des types communs de variables générées.

#### <a name="tshirtsize"></a>tshirtSize

Vous êtes habitué à cette variable générée à partir de l’exemple ci-dessus.

#### <a name="networksettings"></a>networkSettings

Dans un capacité, une fonctionnalité ou un modèle de solution dans l’étendue de bout en bout, les modèles liées créent habituellement ressources qui existent sur un réseau. Une approche simple consiste à utiliser un objet complexe pour stocker des paramètres du réseau et passer au modèles liés.

Exemple de communication des paramètres du réseau puisse être vu en dessous.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Ressources créées dans les modèles liées sont souvent placés dans un jeu de disponibilité. Dans l’exemple suivant, le nom du jeu disponibilité est spécifié et également le domaine d’erreur et le domaine de mise à jour compter pour utiliser.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Si vous avez besoin de plusieurs jeux de disponibilité (par exemple, une pour les nœuds maîtres) et une autre pour les nœuds de données, vous pouvez utiliser un nom comme un préfixe, spécifier plusieurs jeux de disponibilité, ou suivez le modèle ci-dessus pour la création d’une variable pour une taille de t-shirt spécifique.

#### <a name="storagesettings"></a>storageSettings

Détails sur le stockage sont souvent partagées avec les modèles liées. Dans l’exemple ci-dessous, un objet *storageSettings* fournit des détails sur les noms de compte et le conteneur de stockage.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Avec les modèles liées, vous devrez peut-être passer des paramètres système d’exploitation à différents types de nœuds parmi les différents types de configuration connu différente. Un objet complexe est un moyen facile pour stocker et partager des informations de système d’exploitation et facilite également prendre en charge plusieurs choix de système d’exploitation pour le déploiement.

L’exemple suivant montre un objet pour *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Une variable générée, *machineSettings* est un objet complexe qui contient un mélange des variables de base pour la création d’un ordinateur virtuel. Les variables d’incluent le nom d’utilisateur administrateur et mot de passe, un préfixe pour les noms de machine virtuelle et une référence d’image système d’exploitation.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Notez *osImageReference* récupère les valeurs de la variable *osSettings* définie dans le modèle principal. Cela signifie que vous pouvez facilement modifier le système d’exploitation pour une machine virtuelle — entièrement ou en fonction de la préférence d’un consommateur de modèle.

#### <a name="vmscripts"></a>vmScripts

L’objet *vmScripts* contient des informations sur les scripts pour télécharger et exécuter sur une instance de machine virtuelle, y compris à l’extérieur et à l’intérieur des références. À l’extérieur de références incluent l’infrastructure. Intérieur références incluent les logiciels installés installé et configuration.

La propriété *scriptsToDownload* vous permet de répertorier les scripts de téléchargement de la machine virtuelle. Cet objet contienne également des références à des arguments de ligne de commande pour les différents types d’actions. Ces actions incluent l’exécution de l’installation par défaut pour chaque nœud, une installation qui s’exécute une fois tous les nœuds sont déployées et les scripts supplémentaires qui peuvent être spécifiques à un modèle donné.

Cet exemple est à partir d’un modèle utilisé pour déployer MongoDB, qui nécessite un arbitre fournir une disponibilité élevée. *arbiterNodeInstallCommand* a été ajouté à *vmScripts* pour installer le serveur arbitre.

La section variables est l’endroit où vous trouvez les variables qui définissent le texte spécifique pour exécuter le script avec les valeurs correctes.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Retourner l’état à partir d’un modèle

Non seulement vous passer des données dans un modèle, vous pouvez également partager des données dans le modèle d’appel. Dans la section **sort** d’un modèle lié, vous pouvez fournir des paires clé/valeur qui peuvent être utilisés par le modèle source.

L’exemple suivant montre comment passer de l’adresse IP privée générée dans un modèle lié.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dans le modèle principal, vous pouvez utiliser ces données respectant la syntaxe suivante :

    "[reference('master-node').outputs.masterip.value]"

Vous pouvez utiliser cette expression dans la section des sorties ou la section de ressources du modèle principal. Vous ne pouvez pas utiliser l’expression dans la section variables, car elle dépend de l’état d’exécution. Pour renvoyer cette valeur à partir du modèle principal, utilisez :

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Pour obtenir un exemple de l’utilisation de la section des sorties d’un modèle lié à retourner disques de données pour une machine virtuelle, voir [Création de plusieurs disques de données pour une Machine virtuelle](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Définir les paramètres d’authentification pour la machine virtuelle

Vous pouvez utiliser le même modèle présentée précédemment pour les paramètres de configuration pour spécifier les paramètres d’authentification pour une machine virtuelle. Vous créez un paramètre pour passer dans le type d’authentification.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Ajouter des variables pour les différents types d’authentification et une variable pour stocker le type est utilisée pour ce déploiement en fonction de la valeur du paramètre.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Lorsque vous définissez la machine virtuelle, vous définissez **osProfile** à la variable que vous avez créé.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les sections du modèle, voir [Création de modèles de gestionnaire de ressources Azure](resource-group-authoring-templates.md)
- Pour afficher les fonctions qui sont disponibles dans un modèle, voir [Fonctions de modèle de gestionnaire de ressources Azure](resource-group-template-functions.md)

