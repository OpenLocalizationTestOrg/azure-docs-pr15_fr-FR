<properties
    pageTitle="Configurer l’archivage sécurisé clé pour les machines virtuelles dans le Gestionnaire de ressources Azure | Microsoft Azure"
    description="Comment configurer l’archivage sécurisé clé pour une utilisation avec une machine virtuelle Azure le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurer l’archivage sécurisé clé pour les machines virtuelles dans le Gestionnaire de ressources Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique

Dans le Gestionnaire de ressources Azure pile, secrets/certificats sont modélisation en tant que ressources sont fournies par le fournisseur de ressources de l’archivage sécurisé clé. Pour en savoir plus sur l’archivage sécurisé clé, voir [Quel est l’archivage sécurisé de clé Azure ?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Afin que l’archivage sécurisé clé à utiliser avec des machines virtuelles Azure le Gestionnaire de ressources, la propriété **EnabledForDeployment** sur l’archivage sécurisé de clé doit être définie sur true. Vous pouvez le faire dans différents clients.
>
>2. L’archivage sécurisé clé doit être créés dans le même abonnement et l’emplacement que la Machine virtuelle.

## <a name="use-powershell-to-set-up-key-vault"></a>Utiliser PowerShell pour configurer l’archivage sécurisé clé
Pour créer un archivage sécurisé clé à l’aide de PowerShell, voir [prise en main l’archivage sécurisé de clé Azure](../key-vault/key-vault-get-started.md#vault).

Pour chambres fortes clés nouvelle, vous pouvez utiliser cette applet de commande PowerShell :

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pour chambres fortes clés existants, vous pouvez utiliser cette applet de commande PowerShell :

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Nous infrastructure du langage commun pour configurer l’archivage sécurisé clé
Pour créer un archivage sécurisé clé à l’aide de l’interface de ligne (commande), voir [gérer l’archivage sécurisé clé à l’aide d’infrastructure du langage commun](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Pour l’infrastructure du langage commun, vous devez créer l’archivage sécurisé clé avant de vous attribuez la stratégie de déploiement. Vous pouvez le faire à l’aide de la commande suivante :

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utiliser des modèles pour configurer l’archivage sécurisé clé
Lorsque vous utilisez un modèle, vous devez définir la `enabledForDeployment` propriété `true` pour la ressource de l’archivage sécurisé clé.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Pour les autres options que vous pouvez configurer lorsque vous créez un archivage sécurisé clé à l’aide de modèles, voir [créer un archivage sécurisé clé](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
