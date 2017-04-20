<properties
   pageTitle="Déployer des machines virtuelles de carte réseau à plusieurs à l’aide d’un modèle dans le Gestionnaire de ressources | Microsoft Azure"
   description="Découvrez comment déployer des machines virtuelles de carte réseau à plusieurs à l’aide d’un modèle dans le Gestionnaire de ressources"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Déployer des machines virtuelles de carte réseau à plusieurs à l’aide d’un modèle

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Dans la mesure où à ce moment que vous ne peut pas contenir des machines virtuelles avec une seule carte réseau et des ordinateurs virtuels avec plusieurs cartes réseau dans le même groupe de ressources, vous allez implémenter les serveurs principaux dans un groupe de ressources et tous les autres composants dans un autre groupe de sécurité. Les étapes ci-dessous utilisent un groupe de ressources nommé *IaaSStory* pour le groupe de ressources principal et *Serveur principal IaaSStory* pour les serveurs principaux.

## <a name="prerequisites"></a>Conditions préalables

Avant de déployer les serveurs principaux, vous devez déployer le groupe de ressources principale à toutes les ressources nécessaires pour ce scénario. Pour déployer ces ressources, suivez les étapes ci-dessous.

1. Accédez à [la page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Dans la page de modèle, à droite du **groupe de ressources Parent**, cliquez sur **déployer vers Azure**.
3. Si nécessaire, modifiez les valeurs de paramètre, puis suivez les étapes décrites dans le portail Azure Aperçu pour déployer le groupe de ressources.

> [AZURE.IMPORTANT] Assurez-vous que les noms des comptes stockage sont uniques. Vous ne pouvez pas portent des noms de compte de stockage en double dans Azure.

## <a name="understand-the-deployment-template"></a>Comprendre le modèle de déploiement

Avant de déployer le modèle fourni avec cette documentation, vérifiez que vous savez ce qu’il fait. Les étapes suivantes fournissent un aperçu du modèle en question.

1. Accédez à [la page de modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Cliquez sur **azuredeploy.json** pour ouvrir le fichier de modèle.
3. Notez le paramètre *osType* présentée ci-après. Ce paramètre permet de sélectionner quels image machine virtuelle à utiliser pour le serveur de base de données, ainsi que de système d’exploitation plusieurs paramètres associés.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Faites défiler jusqu'à la liste des variables et vérifier la définition pour les variables **dbVMSetting** , présentées ci-après. Il reçoit l’un des éléments du tableau contenues dans la variable **dbVMSettings** . Si vous êtes habitué à la terminologie de développement logiciel, vous pouvez afficher la variable **dbVMSettings** comme une table de hachage ou un ANSI.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Supposons que vous décidez de déployer Windows machines virtuelles SQL en cours d’exécution dans la partie principale. La valeur de **osType** sera alors *Windows*et la variable **dbVMSetting** contient l’élément ci-dessous, qui représente la première valeur de la variable **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Notez que la **vmSize** contient la valeur *Standard_DS3*. Seules les tailles de certains machine virtuelle autoriser l’utilisation de plusieurs cartes réseau. Vous pouvez vérifier les tailles machine virtuelle sont à plusieurs carte réseau activée en visitant une [vue d’ensemble de la carte réseau à plusieurs](virtual-networks-multiple-nics.md).
7. Faites défiler jusqu'à **ressources** et notez le premier élément. Décrire un compte de stockage. Ce compte de stockage permet de tenir à jour les disques de données utilisées par chaque ordinateur virtuel de base de données. Dans ce scénario, chaque base de données machine virtuelle comporte un disque du système d’exploitation stocké en stockage normal et deux disques de données stockées dans le stockage SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Faites défiler jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour l’accès de base de données dans chaque base de données machine virtuelle. Notez l’utilisation de la fonction de **copie** dans cette ressource. Le modèle permet de déployer des machines virtuelles autant que vous le souhaitez, en fonction du paramètre **dbCount** . Par conséquent, vous devez créer la même quantité de cartes réseau pour l’accès de base de données, une pour chaque machine virtuelle.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Faites défiler jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente la carte réseau utilisée pour la gestion de chaque machine virtuelle de la base de données. Là encore, vous devez une de ces cartes réseau pour chaque base de données machine virtuelle. Notez l’élément **networkSecurityGroup** , liaison un NSG qui autorise l’accès à RDP/SSH pour cette carte réseau uniquement.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Faites défiler jusqu'à la ressource suivante, comme indiqué ci-dessous. Cette ressource représente une disponibilité définie pour être partagés par tous les ordinateurs virtuels de base de données. Ainsi, vous garantissez qu’il sera toujours une machine virtuelle dans le jeu en cours d’exécution lors de la maintenance.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Faites défiler jusqu'à la ressource suivante. Cette ressource représente la base de données machines virtuelles, comme illustré dans la première premières lignes présentées ci-après. Notez l’utilisation de la fonction de **copie** à nouveau, garantissant que plusieurs ordinateurs virtuels sont créés sur le paramètre **dbCount** . Notez également la collection **dependsOn** . Il répertorie deux cartes réseau ne soit nécessaire d’être créés avant la machine virtuelle est déployée, ainsi que l’ensemble de la disponibilité et du compte de stockage.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Faites défiler vers le bas dans la ressource machine virtuelle à l’élément **networkProfile** , comme indiqué ci-dessous. Notez qu’il existe deux cartes en cours de référence pour chaque machine virtuelle. Lorsque vous créez plusieurs cartes réseau pour une machine virtuelle, vous devez définir la propriété **primary** de l’une des cartes réseau *true*et le reste sur *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployez le modèle de processeur en déployant à l’aide de clic

> [AZURE.IMPORTANT] Vérifiez que vous suivez les étapes [préalables](#Pre-requisites) avant de suivre les instructions ci-dessous.

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle à l’aide de cliquez sur le déploiement, suivez [ce lien](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à droite du **groupe de ressources de serveur principal (voir la documentation)** , cliquez sur **déployer sur Azure**, remplacez les valeurs de paramètre par défaut si nécessaire et suivez les instructions dans le portail.

L’illustration suivante affiche le contenu du nouveau groupe de ressources, après le déploiement.

![Groupe de ressources serveur principal](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Déployez le modèle à l’aide de PowerShell

Pour déployer le modèle que vous avez téléchargée à l’aide de PowerShell, suivez les étapes ci-dessous.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Exécuter le **`New-AzureRmResourceGroup`** applet de commande pour créer un groupe de ressources à l’aide du modèle.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Résultat attendu :

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployez le modèle à l’aide de l’infrastructure du langage commun Azure

Pour déployer le modèle à l’aide de l’infrastructure du langage commun Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure infrastructure du langage commun, voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) et suivez les instructions jusqu'à l’endroit où vous sélectionnez votre compte Azure et abonnement.
2. Exécuter le **`azure config mode`** commande pour passer en mode directeur des ressources, comme illustré ci-dessous.

        azure config mode arm

    Voici le résultat attendu de la commande ci-dessus :

        info:    New mode is arm

3. Ouvrir le [fichier de paramètres](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Dans cet exemple, nous avons enregistré le fichier de paramètres à *parameters.json*.

4. Exécuter le **`azure group deployment create`** applet de commande pour déployer la nouvelle VNet à l’aide du modèle et paramètre fichiers que vous avez téléchargé et de modifier les propriétés. La liste visible après la sortie explique les paramètres utilisés.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Résultat attendu :

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
