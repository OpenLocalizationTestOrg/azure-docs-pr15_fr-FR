<properties
   pageTitle="Créer des hôtes Docker dans Azure avec Machine Docker | Microsoft Azure"
   description="Décrit l’utilisation de la Machine Docker pour créer des hôtes docker dans Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Créer des hôtes Docker dans Azure avec Docker Machine

Exécution de conteneurs [Docker](https://www.docker.com/) nécessite un hôte machine virtuelle le processus docker en cours d’exécution.
Cette rubrique décrit comment utiliser la commande [docker machine](https://docs.docker.com/machine/) pour créer de nouvelles machines virtuelles Linux, configuré avec le processus Docker, en cours d’exécution dans Azure. 

**Remarque :** 
- *Cet article dépend docker machine version 0.7.0 ou version ultérieure*
- *Conteneurs de Windows est pris en charge par le biais docker machine dans un futur proche*

## <a name="create-vms-with-docker-machine"></a>Créer des ordinateurs virtuels avec Docker Machine

Créer des machines virtuelles hôte docker dans Azure avec la `docker-machine create` commande à l’aide de la `azure` pilote. 

Le pilote Azure devront votre ID d’abonnement. Vous pouvez utiliser l' [Infrastructure du langage commun Azure](xplat-cli-install.md) ou le [Portail Azure](https://portal.azure.com) pour récupérer votre abonnement Azure. 

**À l’aide du portail Azure**
- Sélectionnez abonnements dans la page de navigation gauche, puis copiez à l’id de l’abonnement.

**À l’aide de l’infrastructure du langage commun Azure**
- Type de ```azure account list``` et copiez l’id de l’abonnement.

Type de `docker-machine create --driver azure` pour afficher les options et leurs valeurs par défaut.
Vous pouvez également voir la [documentation Docker Azure pilotes](https://docs.docker.com/machine/drivers/azure/) pour plus d’informations. 

L’exemple suivant s’appuie sur les valeurs par défaut, mais elle s’ouvre éventuellement le port 80 sur l’ordinateur virtuel pour accéder à internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Choisir un hôte docker avec docker machine
Lorsque vous avez une entrée dans docker machine à votre hôte, vous pouvez définir l’hôte par défaut lors de l’exécution de commandes docker.
##<a name="using-powershell"></a>À l’aide de PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>À l’aide de Bash

```bash
eval $(docker-machine env MyDockerHost)
```

Vous pouvez maintenant exécuter des commandes docker sur l’hôte spécifié

```
docker ps
docker info
```

## <a name="run-a-container"></a>Exécuter un conteneur

Avec un hôte configuré, vous pouvez maintenant exécuter un serveur web simple pour tester si votre hôte a été configurée correctement.
Ici nous utiliser une image standard nginx, spécifiez qu’il doit écouter sur le port 80, et que si le redémarrage de l’hôte de machine virtuelle, le conteneur fera redémarrer également (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

La sortie doit ressembler à ce qui suit :

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Tester le conteneur

Examiner les conteneurs en cours d’exécution à l’aide de `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Pour vérifier le conteneur en cours d’exécution, tapez `docker-machine ip <VM name>` pour trouver l’adresse IP à entrer dans le navigateur :

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Conteneur ngnix en cours d’exécution](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Résumé
Avec docker machine, vous pouvez facilement prévoir hôtes docker dans Azure pour votre validations hôte docker individuels.
Pour l’hébergement de production de conteneurs, voir le [Service de conteneur Azure](http://aka.ms/AzureContainerService)

Pour développer des Applications principales .NET Visual Studio, consultez [Docker Tools pour Visual Studio](http://aka.ms/DockerToolsForVS)
