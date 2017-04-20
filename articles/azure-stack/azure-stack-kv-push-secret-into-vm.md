<properties
    pageTitle="Déployer une machine virtuelle avec un certificat à l’aide de l’archivage sécurisé Azure pile clé | Microsoft Azure"
    description="Découvrez comment déployer une machine virtuelle et injecte un certificat de l’archivage sécurisé Azure pile clé"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Créer des ordinateurs virtuels et inclure les certificats récupérées à partir de l’archivage sécurisé clé

Dans la pile d’Azure, machines virtuelles sont déployés par le biais du Gestionnaire de ressources Azure, et vous pouvez maintenant stocker des certificats dans Azure pile clé l’archivage sécurisé. Puis Azure pile (fournisseur Microsoft.Compute ressource pour être précis) pousse les dans vos ordinateurs virtuels lorsque les ordinateurs virtuels sont déployés. Certificats peuvent être utilisés dans de nombreux scénarios, y compris SSL, du chiffrement et authentification par certificat.

À l’aide de cette méthode, vous pouvez protéger le certificat. Vous pouvez désormais pas dans l’image de la machine virtuelle, ou dans des fichiers de configuration de l’application ou un autre emplacement potentiellement dangereux. En définissant la stratégie d’accès appropriée pour l’archivage sécurisé clé, vous pouvez également contrôler qui accède à votre certificat. Un autre avantage est que vous pouvez gérer tous vos certificats au sein d’Azure pile clé l’archivage sécurisé.

Voici une rapide vue d’ensemble du processus :

-   Vous avez besoin d’un certificat au format .pfx.

-   Créer un archivage sécurisé clé (à l’aide d’un modèle ou l’exemple de script suivant).

-   Vérifiez que vous avez activé le commutateur EnabledForDeployment.

-   Téléchargez le certificat comme un code secret.

## <a name="deploying-vms"></a>Machines virtuelles déploiement

Cet exemple de script crée un archivage sécurisé clé, puis stocke un certificat stocké dans le fichier .pfx dans un répertoire local, à l’archivage sécurisé clé comme un code secret.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

La première partie du script lit le fichier .pfx, puis stocke en tant qu’un JSON objet avec l’en base 64 fichier contenu codé. L’objet JSON est également en base 64 codé.

Ensuite, il crée un nouveau groupe de ressources, puis crée un archivage sécurisé clé. Notez le dernier paramètre à la commande New-AzureKeyVault, «-EnabledForDeployment », qui autorise l’accès aux Azure (en particulier au fournisseur de ressources Microsoft.Compute) à lire vault secrets à partir de la clé pour les déploiements.

La dernière commande stocke simplement l’objet JSON en base 64 codé dans l’archivage sécurisé clé sous forme d’un code secret.

Voici un exemple de sortie du script précédent :

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Nous êtes maintenant prêts à déployer un modèle de machine virtuelle. Notez l’URI du code secret à partir du résultat (comme en surbrillance dans la sortie précédente en vert).

Vous avez besoin d’un modèle situé ici. Les paramètres d’un intérêt particulier (outre les paramètres de mémoire virtuelle habituels) sont le nom de l’archivage sécurisé, le groupe de ressources de l’archivage sécurisé et l’URI secrète. Bien entendu, vous pouvez également télécharger à partir de GitHub et modifier selon vos besoins.

Lorsque cette machine virtuelle est déployé, Azure injecte le certificat dans la machine virtuelle.
Sous Windows, les certificats dans le fichier .pfx sont ajoutés avec la clé privée non exportable. Le certificat est ajouté à l’emplacement de certificat LocalMachine, avec le magasin de certificats fournies par l’utilisateur. Sous Linux, le fichier de certificat est placé dans le répertoire /var/lib/waagent, avec le nom du fichier &lt;UppercaseThumbprint&gt;.crt pour la X509 fichier de certificat, et &lt;UppercaseThumbprint&gt;.prv pour la clé privée.
Ces deux fichiers sont .pem mis en forme.

Généralement, l’application recherche le certificat à l’aide de l’empreinte numérique et n’a pas besoin de modification.

## <a name="retiring-certificates"></a>Certificats retraite


Dans la section précédente, que nous vous montrer comment distribuer un nouveau certificat sur vos ordinateurs virtuels existants. Mais votre ancien certificat toujours présent dans la machine virtuelle et ne peut pas être supprimé. Pour une sécurité accrue, vous pouvez modifier l’attribut pour l’ancien secret sur « Désactivé », afin que même si un ancien modèle essaie de créer une machine virtuelle avec cette ancienne version de certificat, il sera. Voici la façon dont vous définissez une version confiant spécifique à désactiver :

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusion


Avec cette nouvelle méthode, le certificat peut être conservé distinct de l’image de la machine virtuelle ou la charge de l’application. Afin que nous avons supprimé un point d’exposition.

Le certificat peut également être renouvelé et téléchargé dans l’archivage sécurisé clé sans avoir à recréer l’image de la machine virtuelle ou le package de déploiement d’application. L’application doit toujours être fourni avec le nouvel URI pour cette nouvelle version de certificat via.

En séparant le certificat de la machine virtuelle ou la charge de l’application, nous avons maintenant réduit le nombre de personnes ayant accès direct au certificat.

En outre, vous avez à présent un seul endroit dans l’archivage sécurisé clé pour gérer tous vos certificats, y compris toutes les versions déployées au fil du temps.

## <a name="next-steps"></a>Étapes suivantes

[Déployer une machine virtuelle avec un mot de passe de l’archivage sécurisé clé](azure-stack-kv-deploy-vm-with-secret.md)

[Autoriser une application à accéder à l’archivage sécurisé clé](azure-stack-kv-sample-app.md)