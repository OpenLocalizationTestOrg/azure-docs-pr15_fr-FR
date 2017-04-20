<properties
    pageTitle="Créer une VM Linux à l’aide d’un modèle Azure | Microsoft Azure"
    description="Créer une VM Linux sur Azure à l’aide d’un modèle Azure le Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Créer une VM Linux à l’aide d’un modèle d’Azure

Cet article vous explique comment déployer rapidement une Machine virtuelle Linux sur Azure à l’aide d’un modèle Azure.  L’article nécessite :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).

- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide `azure login`.

- le mode de gestionnaire de ressources Azure Azure infrastructure du langage commun _doit être placé dans_ `azure config mode arm`.

Vous pouvez également rapidement déployer un modèle Linux VM à l’aide du [portail Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Récapitulatif des commandes rapide

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas détaillé

Les modèles permettent de créer des machines virtuelles sur Azure dont les paramètres que vous voulez personnaliser pendant le menu de lancement, paramètres, tels que les noms d’utilisateur et les noms d’hôte. Pour cet article, nous le lancement débute un modèle Azure utilisant une VM Ubuntu ainsi qu’un groupe de sécurité réseau (NSG) avec port 22 ouvrir événementsSSH.

Azure modèles Gestionnaire de ressources sont des fichiers JSON qui peuvent être utilisés pour des tâches uniques simples comme lancer une Ubuntu VM comme traité dans cet article.  Modèles Azure peuvent également être utilisés pour créer des configurations Azure complexes des environnements entiers comme une pile de déploiement de test, de développement ou de production.

## <a name="create-the-linux-vm"></a>Créer la machine virtuelle Linux

L’exemple suivant montre comment appeler `azure group create` pour créer un groupe de ressources et déployer un VM Linux sécurisé SSH en même temps à l’aide de [ce modèle Azure le Gestionnaire de ressources](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). N’oubliez pas que dans votre exemple, vous devez utiliser des noms uniques à votre environnement. Cet exemple utilise `myResourceGroup` comme nom du groupe de ressources, et `myVM` en tant que le nom de la machine virtuelle.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

La sortie doit ressembler au bloc de sortie suivant :

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Cet exemple déployé une machine virtuelle à l’aide du `--template-uri` paramètre.  Vous pouvez également télécharger ou créer un modèle localement et passez le modèle à l’aide du `--template-file` paramètre avec un chemin d’accès au fichier de modèle en tant qu’argument. L’infrastructure du langage commun Azure vous invite pour les paramètres requis par le modèle.

## <a name="next-steps"></a>Étapes suivantes

Rechercher dans la [Galerie de modèles](https://azure.microsoft.com/documentation/templates/) pour découvrir les structures d’application pour déployer suivant.
