<properties
   pageTitle="Modèle de gestionnaire de ressources pour le stockage | Microsoft Azure"
   description="Montre le schéma de gestionnaire de ressources pour le déploiement des comptes de stockage via un modèle."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="storage-account-template-schema"></a>Schéma de modèle de compte de stockage

Crée un compte de stockage.

## <a name="schema-format"></a>Format de schéma

Pour créer un compte de stockage, ajoutez le schéma suivant à la section de ressources de votre modèle.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
            "accountType": string
        }
    }

## <a name="values"></a>Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- |
| type | Énumération<br />Obligatoire<br />**Microsoft.Storage/storageAccounts**<br /><br />Le type de ressource à créer. |
| apiVersion | Énumération<br />Obligatoire<br />**2015-06-15** ou **2015-05-01-aperçu**<br /><br />La version de l’API à utiliser pour créer la ressource. | 
| nom | Chaîne<br />Obligatoire<br />Entre 3 et 24 caractères, seuls les nombres et les minuscules.<br /><br />Le nom du compte de stockage à créer. Le nom doit être unique dans l’ensemble d’Azure. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’appellation comme illustré dans l’exemple ci-dessous. |
| emplacement | Chaîne<br />Obligatoire<br />Une zone qui prend en charge les comptes de stockage. Pour déterminer les régions valides, voir [prise en charge des régions](resource-manager-supported-services.md#supported-regions).<br /><br />La région pour le compte de stockage d’hôte. |
| propriétés | Objet<br />Obligatoire<br />[objet de propriétés](#properties)<br /><br />Objet qui spécifie le type de compte de stockage à créer. |

<a id="properties" />
### <a name="properties-object"></a>objet de propriétés

| Nom | Valeur |
| ---- | ---- | 
| accountType | Chaîne<br />Obligatoire<br />**Standard_LRS**, **Standard_ZRS**, **Standard_GRS**, **Standard_RAGRS**ou **Premium_LRS**<br /><br />Le type de compte de stockage. Les valeurs autorisées correspondent aux Standard localement redondantes, Zone Standard redondantes, Geo-redondants Standard, accès en lecture Standard Geo-redondants et Premium localement redondants. Pour plus d’informations sur ces types de comptes, voir [la réplication du stockage Azure](./storage/storage-redundancy.md ). |

    
## <a name="examples"></a>Exemples

L’exemple suivant déploie un compte de stockage Standard localement redondants avec un nom unique basé sur l’id de groupe de ressources.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
            {
                    "accountType": "Standard_LRS"
            }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Il existe de nombreux modèles de démarrage rapide qui incluent un compte de stockage. Les modèles suivants illustrent quelques scénarios courants :

- [Créer un compte de stockage Standard](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Déploiement simple d’une machine virtuelle Windows](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Déploiement simple d’un Linux VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Créer un profil CDN, un point de terminaison CDN avec un compte de stockage comme origine](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Créer une batterie de serveurs SharePoint Availabilty haute avec 9 machines virtuelles à l’aide de l’Extension DSC Powershell](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Déploiement simple de 5 nœud secure Service TISSU Cluster avec WAD activé](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Créer une Machine virtuelle à partir d’une Image Windows avec 4 disques de données vide](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur le stockage, voir [Introduction à Microsoft Azure stockage](./storage/storage-introduction.md).
- Par exemple les modèles qui utilisent un nouveau compte de stockage avec une Machine virtuelle, voir [déployer une machine virtuelle Linux simple](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) ou [déployer une machine virtuelle Windows simple](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).
