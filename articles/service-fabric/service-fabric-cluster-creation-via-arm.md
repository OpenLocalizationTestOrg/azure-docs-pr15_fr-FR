
<properties
   pageTitle="Créer un cluster de Service TISSU sécurisé à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cet article décrit comment configurer un cluster de Service TISSU sécurisé dans Azure à l’aide de gestionnaire de ressources Azure, l’archivage sécurisé de clé Azure et Azure Active Directory (DAS) pour l’authentification du client."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Créer un cluster de Service tissu dans Azure à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md)
- [Portail Azure](service-fabric-cluster-creation-via-portal.md)

Il s’agit d’un guide étape par étape qui vous guide tout au long de la procédure de configuration d’un cluster Azure Service TISSU sécurisé dans Azure à l’aide du Gestionnaire de ressources Azure. Ce guide vous guide dans les étapes suivantes :

 - Configurer l’archivage sécurisé clé pour la gestion des clés de sécurité cluster et d’application.
 - Créer un cluster sécurisé dans Azure avec le Gestionnaire de ressources Azure.
 - Authentification des utilisateurs avec Azure Active Directory (DAS) pour la gestion de cluster.

Un cluster sécurisé est un cluster qui empêche l’accès non autorisé aux opérations d’administration, qui comprend le déploiement, la mise à niveau et suppression d’applications, services et les données qu’ils contiennent. Un cluster non sécurisé est un que tout le monde peut se connecter à tout moment et effectuer les opérations de gestion. Bien qu’il soit possible de créer un cluster non sécurisé, il est **fortement recommandé de créer un cluster sécurisé**. Un cluster non sécurisé **ne peut pas être sécurisé plus tard** - un nouveau cluster doit être créé.

Les concepts sont identiques pour la création de clusters sécurisés, les groupes de clusters Linux ou groupes de Windows. Pour plus d’informations et d’assistance scripts pour la création de clusters Linux sécurisés, voir [Création sécurisés clusters sur Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Se connecter à Azure
Ce guide utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant l’exécution de commandes Azure.

Connectez-vous à votre compte azure :

```powershell
Login-AzureRmAccount
```

Sélectionnez votre abonnement :

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurer l’archivage sécurisé clé

Cette section décrit la création d’un archivage sécurisé clé pour un cluster de Service tissu dans Azure et les applications de Service tissu. Pour un guide complet sur l’archivage sécurisé clé, reportez-vous à la [clé de l’archivage sécurisé mise en route][key-vault-get-started].

Service TISSU utilise les certificats X.509 pour sécuriser un cluster et fournir des fonctionnalités de sécurité d’application. Azure l’archivage sécurisé clé est utilisée pour gérer les certificats de groupes de Service tissu dans Azure. Lorsqu’un cluster est déployé dans Azure, le fournisseur de ressources Azure responsable de la création de clusters tissu de Service extrait les certificats de clé de l’archivage sécurisé et ils sont installés sur le cluster machines virtuelles.

Le diagramme suivant illustre la relation entre l’archivage sécurisé clé, un cluster tissu de Service et le fournisseur de ressources Azure qui utilise des certificats stockés dans l’archivage sécurisé clé lorsqu’il crée un cluster :

![Installation du certificat][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

La première étape consiste à créer un groupe de ressources spécifiquement pour l’archivage sécurisé clé. Placer l’archivage sécurisé clé dans son propre groupe de ressources est recommandé. Cela vous permet de supprimer les groupes de ressources informatiques et de stockage, y compris le groupe de ressources qui comporte votre cluster Service TISSU sans perdre vos clés et secrets. Le groupe de ressources qui comporte votre l’archivage sécurisé clé doit être placé dans la même région que le cluster qui est à l’aide.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Créer l’archivage sécurisé clé 

Créer un archivage sécurisé clé dans le nouveau groupe de ressources. La clé l’archivage sécurisé **doit être activé pour le déploiement** pour permettre le fournisseur de ressources tissu de Service obtenir des certificats d’elle et installation sur des nœuds de cluster :

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Si vous avez un archivage sécurisé clé existant, vous pouvez l’activer pour le déploiement à l’aide d’Azure infrastructure du langage commun :

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Ajouter des certificats à l’archivage sécurisé clé

Les certificats sont utilisés dans tissu de Service pour fournir une authentification et chiffrement pour sécuriser les différents aspects d’un cluster et ses applications. Pour plus d’informations sur l’utilisation des certificats dans tissu de Service, voir [scénarios de sécurité cluster Service TISSU][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificat de cluster et serveur (obligatoire) 

Ce certificat est nécessaire pour sécuriser un cluster et empêcher l’accès non autorisé. Il fournit une sécurité cluster de deux façons :
 
 - **Authentification cluster :** Authentifie communication nœud à nœud pour la fédération cluster. Seuls les nœuds qui peuvent se révéler son identité avec ce certificat peuvent rejoindre le cluster.
 - **L’authentification du serveur :** Authentifie les extrémités de gestion de cluster à un client de gestion afin que le client management sache qu'il parle au cluster réel. Ce certificat fournit également SSL pour l’API de gestion HTTPS et Service TISSU Explorer via HTTPS.

Pour répondre à ces fins, le certificat doit répondre aux conditions suivantes :

 - Le certificat doit contenir une clé privée.
 - Le certificat doit être créé pour l’échange de clés, exportable vers un fichier d’échange d’informations personnelles (.pfx).
 - Nom de sujet du certificat doit correspondre le domaine utilisé pour accéder au cluster tissu de Service. Cette matchng est nécessaire pour assurer SSL pour les points de terminaison HTTPS gestion et Service TISSU Explorer le cluster. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez vous procurer un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à partir d’une autorité de certification, nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

### <a name="application-certificates-optional"></a>Certificats d’application (facultatifs)

Un nombre quelconque de certificats supplémentaires peut être installé sur un cluster pour des raisons de sécurité application. Avant de créer votre cluster, envisagez les scénarios de sécurité application nécessitant un certificat doit être installé sur les nœuds, telles que :

 - Chiffrement et déchiffrement des valeurs de configuration d’application
 - Chiffrement des données sur les nœuds lors de la réplication 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Mise en forme de certificats à utiliser de fournisseur de ressources Azure

Fichiers de clé privée (.pfx) peuvent être ajoutées et utilisés directement à l’aide de l’archivage sécurisé clé. Toutefois, le fournisseur de ressources Azure nécessite clés stocké dans un format spécial JSON qui inclut la .pfx en tant que base-64 codé chaîne et le mot de passe de clé privée. Pour répondre à ces exigences, clés doivent être placés dans une chaîne JSON et ensuite stockés en tant que *secrets* dans l’archivage sécurisé clé.

Pour simplifier ce processus, un module PowerShell est [disponible sur GitHub][service-fabric-rp-helpers]. Procédez comme suit pour utiliser le module :

  1. Téléchargez tout le contenu de la mis en pension dans un répertoire local. 
  2. Importer le module dans la fenêtre PowerShell :

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
La `Invoke-AddCertToKeyVault` commande dans ce module PowerShell met en forme une clé privée en une chaîne JSON et des téléchargements pour l’archivage sécurisé clé automatiquement. Utilisez-le pour ajouter le certificat cluster et tous les certificats supplémentaires de l’application à l’archivage sécurisé clé. Répétez cette étape pour tous les certificats que vous voulez installer dans votre cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Les chaînes précédentes sont toutes les conditions préalables l’archivage sécurisé clé pour configurer un modèle de gestionnaire de ressources de cluster tissu de Service qui installe les certificats pour l’authentification nœud, sécurité du point de terminaison d’administration et et les fonctionnalités de sécurité supplémentaires de l’application qui utilisent des certificats X.509. À ce stade, vous disposez à présent la configuration suivante dans Azure :

 - Groupe de ressources de l’archivage sécurisé clé
   - Archivage sécurisé clé
     - Certificat d’authentification serveur cluster
     - Certificats d’application

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurer la Azure Active Directory pour l’authentification du client

AAD permet aux organisations (appelées clients) pour gérer l’accès utilisateur aux applications, qui sont divisées entre les applications avec une interface utilisateur de connexion sur le web et applications avec une expérience native client. Dans ce document, nous part du principe que vous avez déjà créé un client. Dans le cas contraire, commencez par lire [comment obtenir un client Azure Active Directory][active-directory-howto-tenant].

Un cluster de Service TISSU propose plusieurs points d’entrée à ses fonctionnalités de gestion, y compris [Service TISSU Explorer] -based[ service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous créez deux applications AAD pour contrôler l’accès au cluster, une application web et une application native.

Pour simplifier certaines étapes permettant de configurer DAS avec un cluster tissu de Service, nous avons créé un ensemble de scripts Windows PowerShell.

>[AZURE.NOTE] Vous devez effectuer ces étapes *avant de* créer le cluster ainsi dans les cas où les scripts attendent de noms de cluster et points de terminaison, ils doivent être les valeurs planifiées, pas celles que vous avez déjà créé.

1. [Télécharger les scripts] [ sf-aad-ps-script-download] sur votre ordinateur.

2. Cliquez sur le fichier zip, choisissez **Propriétés**, puis activez la case à cocher **Débloquer** et appliquer.

3. Extraire le fichier zip.

4. Exécuter `SetupApplications.ps1`, fournissant la TenantId, nomducluster et WebApplicationReplyUrl en tant que paramètres. Par exemple :

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Vous pouvez trouver votre **TenantId** en exécutant la commande PowerShell '' Get-AzureSubscription'' '. **TenantId** pour chaque abonnement s’affiche.

    La **ClusterName** est utilisée pour préfixer les applications AAD créées par le script. Il n’a pas besoin de correspondre au nom réel cluster exactement tel qu’il est uniquement destiné à simplifier le mapper des objets AAD au cluster tissu de Service qui ils sont utilisés avec.

    La **WebApplicationReplyUrl** est le point de terminaison par défaut AAD renvoie à vos utilisateurs à la fin du processus de connexion. Vous devez spécifier le point de terminaison du Service TISSU Explorer pour votre cluster, qui est par défaut :

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Vous êtes invité à vous connecter à un compte doté des privilèges d’administrateur pour le client AAD. Une fois que vous effectuez, le script continue à créer le web et les applications natives pour représenter votre cluster tissu de Service. Si vous examinez les applications du client dans le [portail classique Azure][azure-classic-portal], vous devriez voir deux nouvelles entrées :

    - *Nomducluster*\_Cluster
    - *Nomducluster*\_Client

    Imprime de script le Json requis par le modèle de gestionnaire de ressources Azure lorsque vous créez le cluster dans la section suivante de tenir la session PowerShell fenêtre Ouvrir.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Créer un modèle de gestionnaire de ressources de cluster tissu de Service

Dans cette section, la sortie des commandes PowerShell précédentes sont utilisées dans un modèle de gestionnaire de ressources de cluster tissu de Service.

Gestionnaire de ressources exemple modèles est disponible dans la [Galerie de modèles de démarrage rapide Azure sous GitHub][azure-quickstart-templates]. Ces modèles peuvent être utilisés comme point de départ pour votre modèle de cluster. 

### <a name="create-the-resource-manager-template"></a>Créer le modèle de gestionnaire de ressources

Ce guide utilise le [cluster sécurisée 5-nœud] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] exemple de modèle et les paramètres de modèle. Télécharger `azuredeploy.json` et `azuredeploy.parameters.json` sur votre ordinateur et ouvrez les deux fichiers dans votre éditeur de texte.

### <a name="add-certificates"></a>Ajouter des certificats

Certificats sont ajoutés à un modèle de gestionnaire de ressources cluster en faisant référence à la clé de l’archivage sécurisé qui contient les clés de certificat. Il est recommandé que ces valeurs de l’archivage sécurisé clé sont placées dans un fichier de paramètres de modèle de gestionnaire de ressources pour conserver le Gestionnaire de ressources réutilisable de fichier de modèle et libérer des valeurs spécifiques à un déploiement.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Ajouter tous les certificats à l’osProfile VMSS

Chaque certificat qui doit être installé dans le cluster doit être configuré dans la section osProfile de la ressource VMSS (Microsoft.Compute/virtualMachineScaleSets). Cela indique le fournisseur de ressources pour installer le certificat sur les ordinateurs virtuels. Cela inclut le certificat cluster ainsi que les certificats de sécurité application que vous souhaitez utiliser pour vos applications :

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurer un certificat de cluster tissu de Service

Le certificat d’authentification cluster doit également être configuré dans la ressource de cluster tissu de Service (Microsoft.ServiceFabric/clusters) et l’extension de Service tissu pour VMSS dans la ressource VMSS. Cela permet au fournisseur de ressources tissu de Service configurer pour l’utiliser pour l’authentification cluster et serveur pour les points de terminaison de gestion.

##### <a name="vmss-resource"></a>Ressource VMSS :

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Ressource tissu de service :

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Insérer config AAD

La configuration AAD créée précédemment peut être insérée directement dans votre modèle de gestionnaire de ressources, cependant, il est recommandé d’extraire les valeurs dans les paramètres tout d’abord dans un fichier de paramètres pour conserver le modèle de gestionnaire de ressources réutilisables et gratuit de valeurs spécifiques à un déploiement.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < un « configurer-processeur » ></a>Gestionnaire de ressources de configurer les paramètres de modèle

Enfin, utilisez les valeurs de sortie à partir des commandes de l’archivage sécurisé clé et AAD PowerShell pour remplir le fichier de paramètres :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
À ce stade, vous devez maintenant disposer des éléments suivants :

 - Groupe de ressources de l’archivage sécurisé clé
    - Archivage sécurisé clé
    - Certificat d’authentification serveur cluster
    - Certificat de chiffrement des données
 - Azure client Active Directory 
    - Application AAD pour la gestion basée sur le web et Explorer tissu de Service
    - Application AAD gestion native client
    - Utilisateurs dotés des rôles attribués 
 - Modèle de gestionnaire de ressources cluster tissu de service
    - Certificats configurés via l’archivage sécurisé clé
    - Azure Active Directory configuré 

Le diagramme suivant illustre où l’archivage sécurisé clé et AAD configuration interviennent dans votre modèle de gestionnaire de ressources.

![Carte de dépendance Gestionnaire de ressources][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Créer le cluster

Vous êtes maintenant prêt à créer le cluster à l’aide de [déploiement processeur][resource-group-template-deploy].

#### <a name="test-it"></a>Tester

Utilisez la commande PowerShell suivante pour tester votre modèle de gestionnaire de ressources avec un fichier de paramètres :

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Déployer

Si le test de modèle du Gestionnaire de ressources réussit, utilisez la commande PowerShell suivante pour déployer votre modèle de gestionnaire de ressources avec un fichier de paramètres :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Attribuer des rôles

Une fois que vous avez créé les applications pour représenter votre cluster, vous devez attribuer vos utilisateurs aux rôles pris en charge par tissu de Service : en lecture seule et administrateur. Vous pouvez le faire à l’aide du [portail classique Azure][azure-classic-portal].

1. Accédez à votre client et choisissez les Applications.
2. Sélectionnez l’application web, c'est-à-dire un nom tel que `myTestCluster_Cluster`.
3. Cliquez sur l’onglet utilisateurs.
4. Sélectionnez un utilisateur à affecter et cliquez sur le bouton **affecter** dans la partie inférieure de l’écran.

    ![Assigner des utilisateurs à un bouton de rôles][assign-users-to-roles-button]

5. Sélectionnez le rôle à attribuer à l’utilisateur.

    ![Attribuer des rôles][assign-users-to-roles-dialog]

>[AZURE.NOTE] Pour plus d’informations sur les rôles dans tissu de Service, voir [contrôle d’accès basé sur un rôle pour tissu de Service clients](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Créer des clusters sécurisés sur Linux

Pour simplifier le processus, un script d’assistance a été fourni [ici](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Pour l’utilisation de ce script d’assistance, il est supposé que vous avez déjà installé Azure infrastructure du langage commun, et il est dans votre chemin d’accès. Assurez-vous que le script possède les autorisations d’exécution en exécutant `chmod +x cert_helper.py` après son téléchargement. La première étape consiste à vous connecter à votre compte Azure à l’aide de l’infrastructure du langage commun avec la `azure login` commande. Une fois la connexion à votre compte Azure, utilisez l’Assistant avec votre certificat signé autorité de certification, comme indiqué dans la commande ci-dessous :

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Cette commande renvoie les trois chaînes suivantes en tant que le résultat : 

1. SourceVaultID est l’ID de la nouvelle KeyVault ResourceGroup il créé pour vous. 

2. CertificateUrl pour l’accès au certificat.

3. CertificateThumbprint, qui est utilisé pour l’authentification.


L’exemple suivant montre comment utiliser la commande :

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
L’exécution de la commande précédente vous offre les trois chaînes comme suit :

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nom de sujet du certificat doit correspondre le domaine utilisé pour accéder au cluster tissu de Service. Cela est nécessaire pour fournir SSL pour le cluster points de terminaison HTTPS gestion et Explorer tissu de Service. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez vous procurer un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à partir d’une autorité de certification nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Voici les entrées nécessaires pour créer un cluster de tissu service sécurisé (sans AAD) tels que décrits sur [configurer le Gestionnaire de ressources des paramètres du modèle](#configure-arm). Vous pouvez vous connecter au cluster sécurisé via les instructions de la section [authentification des accès client vers un cluster](service-fabric-connect-to-secure-cluster.md). Clusters preview Linux ne prennent pas en charge authentification AAD. Vous pouvez attribuer des rôles d’administration et le client comme décrit dans la section [attribuer des rôles aux utilisateurs](#assign-roles). Lorsque vous spécifiez des rôles d’administrateur et client pour un cluster preview Linux, vous devez fournir les empreintes de certificats pour l’authentification (contrairement à nom de sujet, dans la mesure où aucune validation de la chaîne ou de révocation de certificats n’est exécutée dans cette version préliminaire).


Si vous souhaitez utiliser un certificat auto-signé pour le test, vous pouvez utiliser le même script pour générer un certificat auto-signé et téléchargez-le sur KeyVault, grâce à l’indicateur `ss` au lieu de fournir le chemin d’accès du certificat et le nom de certificat. Par exemple, reportez-vous à la commande suivante pour créer et télécharger un certificat auto-signé :

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Cette commande renvoie les mêmes chaînes de trois, SourceVault, CertificateUrl et CertificateThumbprint, qui est utilisé pour créer un cluster Linux sécurisé, ainsi que l’emplacement dans lequel le certificat auto-signé a été placé. Vous devez le certificat auto-signé pour vous connecter au cluster.  Vous pouvez vous connecter au cluster sécurisé via les instructions de la section [authentification des accès client vers un cluster](service-fabric-connect-to-secure-cluster.md). Nom de sujet du certificat doit correspondre le domaine utilisé pour accéder au cluster tissu de Service. Cela est nécessaire pour fournir SSL pour le cluster points de terminaison HTTPS gestion et Explorer tissu de Service. Vous ne pouvez pas obtenir un certificat SSL à partir d’une autorité de certification (CA) pour le `.cloudapp.azure.com` domaine. Vous devez vous procurer un nom de domaine personnalisé pour votre cluster. Lorsque vous demandez un certificat à partir d’une autorité de certification nom de sujet du certificat doit correspondre au nom de domaine personnalisé utilisé pour votre cluster.

Les paramètres fournis par le script d’assistance peuvent être renseignés le portail comme décrit dans la section [créer un cluster dans le portail Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous disposez d’un cluster sécurisé avec l’authentification de gestion fournissant Azure Active Directory. Ensuite, [vous connecter à votre cluster](service-fabric-connect-to-secure-cluster.md) et découvrez comment [Gérer les secrets de l’application](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Résoudre les problèmes de la configuration d’Azure Active Directory pour l’authentification du client

Si vous rencontrez un problème lors de la configuration d’Azure Active Directory pour l’authentification du client, passez en revue les suggestings suivants pour les solutions possibles.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Service TISSU Explorer invite permettant de sélectionner certificat

#### <a name="problem"></a>Problème

Une fois la connexion correctement sur la page de connexion AAD dans l’Explorateur de tissu de Service, le navigateur renvoie à la page d’accueil, mais vous invite à une boîte de dialogue permettant de sélectionner un certificat.

![Boîte de dialogue Sélectionner un certificat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Raison n °

L’utilisateur n’a été affecté un rôle dans l’application en cluster AAD. Par conséquent l’authentification AAD échoue sur un cluster de tissu de Service. Service TISSU Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution

Suivez les instructions de configuration DAS et attribuer des rôles d’utilisateur. En outre, « Utilisateur d’affectation obligatoire pour application ACCESS » est recommandée pour être activée en tant que `SetupApplications.ps1` signifie.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Se connecter avec PowerShell échoue avec le message d’erreur : les informations d’identification spécifiées ne sont pas valides

#### <a name="problem"></a>Problème

Quand utiliser PowerShell pour vous connecter à un cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois la connexion correctement sur la page de connexion DAS, connexion échoue avec l’erreur : les informations d’identification spécifiées ne sont pas valides indiqué.

#### <a name="solution"></a>Solution

Identique à celle ci-dessus.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Service TISSU Explorer Échec de la signature en retour : AADSTS50011

#### <a name="problem"></a>Problème

Une fois la connexion dans la page dans l’Explorateur de tissu de Service de connexion AAD, page renvoie se d’échec - AADSTS50011 : l’adresse de réponse &lt;url&gt; ne correspond pas à l’adresse de réponse configuré pour l’application : &lt;guid&gt;. 

![Adresse de réponse SFX ne correspondent pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Raison n °

L’application cluster(web) représentant Service TISSU Explorer tente de s’authentifier AAD, dans le cadre de la demande il propose l’URL de renvoi de redirection. Mais il n’est pas répertorié dans la liste 'URL réponse' AAD l’application.

#### <a name="solution"></a>Solution

Ajoutez l’url de Service TISSU Explorer 'URL réponse' dans l’onglet Configurer d’application cluster(web) ou remplacer l’un des éléments dans la liste. Puis enregistrer.

![Url de réponse d’application Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Puis-je réutiliser le même client AAD multi-clusters ?

#### <a name="answer"></a>Réponse

Oui. Mais n’oubliez pas d’ajouter l’URL du Service TISSU Explorateur à votre application cluster(web) qu'autrement Service TISSU Explorer ne fonctionnent pas.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Pourquoi dois-je certificat de serveur tant que AAD activé ?

#### <a name="answer"></a>Réponse

FabricClient et FabricGateway effectuer l’authentification commun. En cas d’authentification AAD, intégration AAD fournit l’identité du client au serveur et le certificat de serveur est utilisée pour vérifier l’identité du serveur. Pour plus d’informations sur le fonctionne de certificat sur tissu de Service, vérifiez [les certificats X.509 et tissu de Service][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png