<properties
   pageTitle="À l’aide de l’extension Azure Docker machine virtuelle | Microsoft Azure"
   description="Apprenez à utiliser l’extension Docker machine virtuelle pour déployer rapidement et en toute sécurité un environnement Docker dans Azure à l’aide de modèles de gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Créer un environnement Docker dans Azure à l’aide de l’extension Docker machine virtuelle
Docker est une gestion des conteneurs populaires et images plateforme qui permet d’utiliser rapidement des conteneurs sur Linux (et Windows également). Dans Azure, il existe différentes façons, vous pouvez déployer Docker selon vos besoins. Cet article se concentre sur l’utilisation de l’extension Docker machine virtuelle et modèles Azure le Gestionnaire de ressources. 

Pour plus d’informations sur les différentes méthodes de déploiement, notamment à l’aide de la Machine Docker et Azure conteneur Services, consultez les articles suivants :

- Prototype rapidement une application, vous pouvez créer un seul hôte Docker à l’aide de la [Machine Docker](./virtual-machines-linux-docker-machine.md).
- Pour les environnements plus grandes et plus stables, vous pouvez utiliser l’extension Azure Docker machine virtuelle, qui prennent également en charge [Docker composer](https://docs.docker.com/compose/overview/) pour générer des déploiements conteneur cohérentes. Cet article explique en utilisant l’extension Azure Docker machine virtuelle.
- Pour créer des environnements opérationnelle, scalable qui fournissent des outils de planification et de gestion supplémentaires, vous pouvez déployer un [cluster Docker par essaim sur Azure conteneur Services](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Vue d’ensemble de l’extension Docker machine virtuelle Azure
L’extension Azure Docker machine virtuelle installe et configure le processus Docker client Docker et Docker composer dans votre Linux machine virtuelle (). En utilisant l’extension Azure Docker machine virtuelle, vous avez plus de contrôle et de fonctionnalités de simplement à l’aide de la Machine Docker ou de création de l’hôte Docker vous-même. Ces fonctionnalités supplémentaires, telles que [Docker composer](https://docs.docker.com/compose/overview/), vérifiez l’extension Azure Docker machine virtuelle adaptée aux environnements de production ou développeur plus performantes grâce aux.

Les modèles de gestionnaire de ressources Azure définissent l’intégralité de la structure de votre environnement. Modèles permettent de créer et configurer des ressources telles que l’hôte Docker machines virtuelles, stockage, contrôles d’accès basé sur un rôle (RBAC) et diagnostics. Vous pouvez réutiliser ces modèles pour créer des déploiements supplémentaires de manière cohérente. Pour plus d’informations sur le Gestionnaire de ressources Azure et les modèles, voir [vue d’ensemble du Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Déployer un modèle avec l’extension Azure Docker machine virtuelle
Nous allons utiliser un modèle de démarrage rapide existant pour créer une VM Ubuntu qui utilise l’extension Azure Docker machine virtuelle pour installer et configurer l’hôte Docker. Vous pouvez afficher le modèle ici : [déploiement Simple d’une machine virtuelle Ubuntu avec Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Vous devez la [Dernière Azure infrastructure du langage commun](../xplat-cli-install.md) installé et connecté à l’aide du mode directeur des ressources comme suit :

```
azure config mode arm
```

Déployez le modèle à l’aide de l’infrastructure du Azure langage commun, spécifiant le modèle URI. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUS` emplacement. Utiliser votre propre nom de groupe de ressources et l’emplacement comme suit :

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Répondez aux invites pour nommer votre compte de stockage, fournir un nom d’utilisateur et mot de passe et fournissez un nom DNS. Le résultat est semblable à l’exemple suivant :

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

La renvoie Azure infrastructure du langage commun à l’invite après seulement quelques secondes, mais votre hôte Docker est toujours en cours créé et configuré par l’extension Azure Docker machine virtuelle. Il peut prendre quelques minutes pour le déploiement terminer. Vous pouvez afficher des détails sur l’état hôte Docker à l’aide du `azure vm show` commande.

L’exemple suivant vérifie l’état de la machine virtuelle nommée `myDockerVM` (le nom par défaut à partir du modèle - ne modifiez pas ce nom) dans le groupe de ressources nommé `myResourceGroup`. Entrez le nom du groupe de ressources que vous avez créé à l’étape précédente :

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

Le résultat de la `azure vm show` commande est semblable à l’exemple suivant :

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Dans la partie supérieure de la sortie, vous voyez la `ProvisioningState` de la machine virtuelle. Lorsque cette option affiche `Succeeded`, le déploiement a terminé et que vous pouvez SSH de la machine virtuelle.

Vers la fin de la sortie, `FQDN` affiche le nom de domaine complet de votre hôte Docker. Ce nom de domaine complet est ce que vous utilisez pour SSH à votre hôte Docker dans les étapes restantes.


## <a name="deploy-your-first-nginx-container"></a>Déployez votre premier conteneur nginx
Une fois le déploiement terminée, SSH vers votre nouvel hôte Docker à partir de votre ordinateur local. Entrez votre propre nom d’utilisateur et le nom de domaine complet comme suit :

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Une fois connecté à l’hôte Docker, nous allons exécuter un conteneur nginx :

```
sudo docker run -d -p 80:80 nginx
```

Le résultat est semblable à l’exemple suivant que l’image nginx est téléchargée et un conteneur de mise en route :

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Vérifier l’état des conteneurs s’exécutant sur votre hôte Docker comme suit :

```
sudo docker ps
```

Le résultat est semblable à l’exemple suivant, montrant que le conteneur nginx est en cours d’exécution et les ports TCP 80 et 443 et transféré :

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Pour afficher votre conteneur en action, ouvrez un navigateur web et entrez le nom de domaine complet de votre hôte Docker :

![Conteneur ngnix en cours d’exécution](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Référence du modèle Azure machine virtuelle Docker extension
L’exemple précédent utilise un modèle de démarrage rapide existant. Vous pouvez également déployer l’extension Azure Docker machine virtuelle avec vos propres modèles Gestionnaire de ressources. Pour ce faire, ajoutez le code suivant vos modèles Gestionnaire de ressources, définir la `vmName` de votre machine virtuelle correctement :

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Vous pouvez trouver plus détaillées sur l’utilisation des modèles de gestionnaire de ressources en lisant l’article [Présentation du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Étapes suivantes
Vous souhaiterez peut-être [configurer le processus Docker port TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprendre la [sécurité Docker](https://docs.docker.com/engine/security/security/)ou déployer conteneurs à l’aide [Docker composer](https://docs.docker.com/compose/overview/). Pour plus d’informations sur l’Extension de machine virtuelle Docker Azure lui-même, voir le [projet GitHub](https://github.com/Azure/azure-docker-extension/).

En savoir plus sur les options de déploiement Docker supplémentaires dans Azure :

- [Utiliser Docker Machine avec le pilote d’Azure](./virtual-machines-linux-docker-machine.md)  
- [Prise en main Docker et composition pour définir et exécuter une application conteneur à plusieurs sur une machine virtuelle Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Déployer un cluster de Service de conteneur Azure](../container-service/container-service-deployment.md)
