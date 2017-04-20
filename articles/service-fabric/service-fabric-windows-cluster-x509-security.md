<properties
   pageTitle="Se connecter à un cluster privé sécurisé | Microsoft Azure"
   description="Cet article décrit comment sécuriser les communications au sein de l’autonome ou cluster privé ainsi qu’entre les clients et le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de certificats X.509

Cet article décrit comment faire pour sécuriser la communication entre les différents nœuds de votre cluster Windows autonome, ainsi que la manière dont s’authentifier clients qui se connectent à ce cluster, à l’aide de X.509 des certificats. Cela garantit que seuls les utilisateurs autorisés peuvent accéder au cluster, les applications déployées et tâches de gestion.  Sécurité de certificat doit être activée sur le cluster lorsque le cluster est créé.  

Pour plus d’informations sur la sécurité de cluster comme nœud-à-nœud sécurité client-à-nœud sécurité et contrôle d’accès basé sur un rôle, voir [scénarios de sécurité Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Les certificats aurez-vous besoin ?

Pour commencer à [Télécharger le package de cluster autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) à un des nœuds dans votre cluster. Dans le package téléchargé, vous trouverez un fichier **ClusterConfig.X509.MultiMachine.json** . Ouvrez le fichier et passez en revue la section **sécurité** sous la section **Propriétés** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

Cette section décrit les certificats dont vous avez besoin pour sécuriser votre cluster Windows autonome. Pour activer la sécurité basée sur certificat auquel les valeurs de **ClusterCredentialType** et **ServerCredentialType** *X509*.

>[AZURE.NOTE] Une [empreinte numérique](https://en.wikipedia.org/wiki/Public_key_fingerprint) est l’identité principale d’un certificat. Lisez [comment récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx) pour déterminer l’empreinte des certificats que vous créez.

Le tableau suivant répertorie les certificats dont vous aurez besoin dans votre programme d’installation cluster :

|**Paramètre de CertificateInformation**|**Description**|
|-----------------------|--------------------------|
|ClusterCertificate|Ce certificat est nécessaire pour sécuriser la communication entre les nœuds sur un cluster. Vous pouvez utiliser deux certificats différents, un serveur principal et secondaire pour la mise à niveau. Définir l’empreinte numérique du certificat primaire dans la section **empreinte numérique** et celle de l’image secondaire dans les variables **ThumbprintSecondary** .|
|Certificats|Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Pour faciliter la tâche, vous pouvez choisir d’utiliser le même certificat pour *ClusterCertificate* et *certificats*. Vous pouvez utiliser deux certificats serveur différent, un serveur principal et secondaire pour la mise à niveau. Définir l’empreinte numérique du certificat primaire dans la section **empreinte numérique** et celle de l’image secondaire dans les variables **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Il s’agit d’un ensemble de certificats que vous voulez installer sur les clients authentifiés. Vous pouvez avoir un certain nombre de certificats client différents installés sur les ordinateurs que vous voulez autoriser l’accès au cluster. Définir l’empreinte numérique de chaque certificat dans la variable **CertificateThumbprint** . Si vous définissez **IsAdmin** *true*, puis le client avec ce certificat est installé possibles administrateur activités de gestion sur le cluster. Si l' **IsAdmin** est *faux*, le client avec ce certificat ne pouvez effectuer les actions autorisées pour les droits d’accès utilisateur, en général en lecture seule. Pour plus d’informations sur les rôles lire [accès contrôle basé sur un rôle](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Définir le nom commun du certificat client première pour la **CertificateCommonName**. Le **CertificateIssuerThumbprint** est l’empreinte numérique pour l’émetteur de ce certificat. Lire [des certificats](https://msdn.microsoft.com/library/ms731899.aspx) pour en savoir plus sur les noms courantes et l’émetteur.|
|HttpApplicationGatewayCertificate|Il s’agit d’un certificat facultatif qui peut être spécifié si vous voulez protéger votre passerelle d’Application Http. Vérifiez que reverseProxyEndpointPort est définie dans nodeTypes si vous utilisez ce certificat.|

Voici la configuration du cluster exemple où les certificats Cluster, serveur et Client ont été fournis.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Acquérir X.509 certificats
Pour sécuriser les communications au sein du cluster, vous devez d’abord obtenir des certificats X.509 pour vos nœuds de cluster. En outre, pour limiter la connexion à ce groupe pour les ordinateurs/utilisateurs autorisés, vous devez obtenir et d’installer des certificats pour les ordinateurs client.

Pour les clusters qui exécutent les charges de travail, vous devez utiliser une [Autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority) connecté certificat X.509 utilisé pour sécuriser le cluster. Pour plus d’informations sur l’obtention de ces certificats, accédez à [Comment : obtenir un certificat](http://msdn.microsoft.com/library/aa702761.aspx).

Pour les clusters destiné à des fins de test, vous pouvez choisir d’utiliser un certificat auto-signé.

## <a name="optional-create-a-self-signed-certificate"></a>Facultatif : Créez un certificat auto-signé
Pour créer un certificat auto-signé pouvant être sécurisées correctement consiste à utiliser le script *CertSetup.ps1* dans le dossier de Service TISSU SDK dans le répertoire *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Modifiez ce fichier, utilisez cette option pour créer un certificat avec un nom approprié.

Maintenant, exportez le certificat vers un fichier PFX avec un mot de passe protégée. Vous devez tout d’abord obtenir l’empreinte numérique du certificat. Exécutez l’application certmgr.exe. Accédez au dossier **Local\personnel** et recherchez le certificat que vous venez de créer. Double-cliquez sur le certificat pour l’ouvrir, sélectionnez l’onglet *Détails* et naviguez jusqu'à *l’empreinte numérique du* champ. Copiez la valeur empreinte numérique dans la ligne de commande PowerShell ci-dessous, supprimer les espaces.  Définissez la valeur *$pswd* sur un mot de passe sécurisé pertinence protéger et exécuter la session PowerShell :

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Pour afficher les détails d’un certificat installé sur l’ordinateur, vous pouvez exécuter la commande PowerShell suivante :

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Par ailleurs, si vous avez un abonnement Azure, suivez la section [Ajouter des certificats à l’archivage sécurisé clé](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installer les certificats
Une fois que vous avez ou les certificats, vous pouvez les installer sur les nœuds du cluster. Les nœuds doivent être la plus récente de Windows PowerShell 3.x est installé dessus. Vous devrez répéter ces étapes pour chaque nœud, pour les certificats Cluster et serveur et les certificats secondaires.

1. Copier les fichiers .pfx vers le nœud.
2. Ouvrez une fenêtre PowerShell en tant qu’administrateur et entrez les commandes suivantes. Remplacez le *$pswd* par le mot de passe que vous avez utilisé pour créer ce certificat. Remplacez le *$PfxFilePath* par le chemin d’accès complet de la .pfx copiés sur ce nœud.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Ensuite, vous devez définir le contrôle d’accès sur ce certificat afin que le processus de tissu de Service, qui s’exécute sous le compte de Service de réseau, puissent les utiliser en exécutant le script suivant. Fournir l’empreinte numérique du certificat et de « SERVICE réseau » pour le compte de service. Vous pouvez vérifier que les utilisateurs sur le certificat sont corrects à l’aide de l’outil certmgr.exe et examinez les clés privées gérer sur le certificat.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Connectez-ServiceFabricCluster - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName mon
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
Se connecter ServiceFabricCluster Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
