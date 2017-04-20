<properties 
  pageTitle="Déploiement de votre propre registre privé Docker sur Azure | Microsoft Azure"
  description="Décrit comment vous pouvez utiliser Docker Registre pour accueillir vos images conteneur sur le service de stockage d’objets Blob Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Déploiement de votre propre registre privé Docker sur Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Ce document explique quels Docker Registre privé est et montre comment vous pouvez déployer une image de conteneur Docker Registre 2.0 à un Registre privé Docker sur Microsoft Azure à l’aide de stockage d’objets Blob Azure.

Ce document suppose que :

1. Vous savez comment utiliser Docker et avez Docker d’images à stocker. (Vous n’apparaissent pas ? [En savoir plus sur Docker](https://www.docker.com))
2. Vous avez un serveur qui comporte le moteur Docker installé. (Vous n’apparaissent pas ? [Faire rapidement dans Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>Qu’est un Registre Docker privé ?

Pour pouvoir livrer vos applications en conteneur dans le cloud, vous construisez une image de conteneur Docker et stockez quelque part afin qu’il puisse être utilisé par vous-même et par d’autres personnes. 

Création d’une image de conteneur et de livraison dans le cloud sont simple, mais il est compliqué pour stocker de manière fiable l’image générée. Pour cette raison, Docker propose un service centralisé appelé [Concentrateur Docker] [ docker-hub] pour stocker conteneur images sur le nuage et vous permet de créer des conteneurs à tout moment à l’aide de ces images.

Bien que le [Hub Docker] [ docker-hub] est un service payant pour stocker le conteneur de votre application privé des images, des besoins Docker respectant les développeurs et fournit une boîte à outils open source pour stocker vos images dans votre propre registre Docker privé derrière un pare-feu ou local sans cliquer sur l’Internet public.
Stockage d’objets Blob Azure étant sécuriser, vous pouvez rapidement l’utiliser pour créer et utiliser un Registre Docker privé dans Azure que vous contrôlez vous-même.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Pourquoi doit héberger un Registre Docker sur Azure ?

En qui héberge votre instance de Registre Docker sur Microsoft Azure et stocker vos images sur stockage d’objets Blob Azure, vous pouvez avoir plusieurs avantages :

**Sécurité :** Vos images Docker ne laissent pas centres de données Azure, afin qu’ils ne passent pas l’Internet public comme si vous utilisiez Docker concentrateur.
  
**Performances :** Vos images Docker sont stockées dans le centre de données ou la région même en tant que vos applications. Cela signifie que les images se fera plus rapide et efficace par rapport à Docker concentrateur.

**Fiabilité :** À l’aide de Microsoft Azure Blob Storage, vous pouvez apporter utilisez de nombreuses propriétés de stockage à haute disponibilité, redondance, stockage premium (SSDs) et ainsi de suite.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configuration du Registre Docker d’utiliser le stockage Blob Azure

(Il est recommandé de lire la [documentation Docker Registre 2.0][Registre documents] avant de poursuivre.)

Vous pouvez [configurer] [ registry-config] le Registre Docker de deux façons différentes.
Vous pouvez :

1. Utiliser un `config.yml` fichier. Dans ce cas, vous devrez créer une image Docker distincte au-dessus `registry` image.
2. Remplacer le fichier de configuration par défaut les variables d’environnement : Obtient sans créer et maintenir une image Docker distincte la réalisation des tâches.

Pour des raisons de simplicité, cette rubrique suit option 2, en utilisant les variables d’environnement.

Pour exécuter un Registre Docker instance qui :

* utilise votre compte de stockage Azure pour stocker les images
* écoute sur le port de la Machine virtuelle 5000
* aucune méthode d’authentification configuré (non recommandé, voir la Remarque ci-dessous)

Vous devez exécuter la commande Docker suivante dans votre terminal bash (remplacer `<storage-account>` et `<storage-key>` avec vos informations d’identification) :

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Une fois que la commande se ferme, vous pouvez voir le conteneur qui héberge l’instance du Registre Docker privé en exécutant la `docker ps` commande sur votre hôte :

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configuration de la sécurité du Registre Docker non abordé dans ce document et le Registre seront accessible à quiconque sans authentification par défaut si vous ouvrez le port pour le port du Registre sur le point de terminaison Machine virtuelle ou l’équilibrage de charge si vous utilisez la commande déploiement ci-dessus.
>
> Veuillez lire la [Configuration du Registre de Docker] [ registry-config] documentation pour savoir comment sécuriser l’instance du Registre et vos images.

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre Registre configurer, il est temps pour accéder à utiliser plus. Précédez la docker [Registre docs]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[documents de Registre]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
