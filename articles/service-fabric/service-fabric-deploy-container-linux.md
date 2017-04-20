<properties
   pageTitle="Service tissu et déploiement conteneurs dans Linux | Microsoft Azure"
   description="TISSU de service et l’utilisation de conteneurs Docker pour déployer des applications microservice. Cet article décrit les fonctionnalités qui tissu de Service fournit des conteneurs et comment déployer une image de conteneur Docker dans un cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Déployer un conteneur Docker pour tissu de Service

> [AZURE.SELECTOR]
- [Déployer Windows conteneur](service-fabric-deploy-container.md)
- [Déployer Docker conteneur](service-fabric-deploy-container-linux.md)

Cet article vous guide dans la création de services en conteneur dans des conteneurs de Docker sous Linux.

Service TISSU présente plusieurs fonctionnalités de conteneur qui vous aident à créer des applications qui sont composées de microservices sont en conteneur. Il s’agit des services.

Les fonctionnalités incluent ;

- L’activation et déploiement de l’image conteneur
- Gouvernance de ressources
- Authentification référentiel
- Port conteneur sur le port hôte
- Découverte de conteneur à l’autre et communication
- Capacité à configurer et définir des variables d’environnement


## <a name="packaging-a-docker-container-with-yeoman"></a>Empaqueter un conteneur docker avec yeoman
Lors de la compression un conteneur sur Linux, vous pouvez choisir d’utiliser un modèle yeoman ou [créer manuellement le package d’application](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Une application de Service TISSU peut contenir un ou plusieurs conteneurs, chacune avec un rôle spécifique à proposer des fonctionnalités de l’application. Le Kit de développement de tissu de Service pour Linux inclut un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre application et ajouter une image conteneur. Nous allons utiliser Yeoman pour créer une application à un seul conteneur Docker appelé *SimpleContainerApp*. Vous pouvez ajouter que plusieurs services ultérieurement en modifiant les fichiers manifeste.

## <a name="create-the-application"></a>Créer l’application

1. Dans un terminal, tapez **yo azuresfguest**.

2. Pour l’infrastructure choisissez **conteneur** .

3. Nommez votre application, par exemple SimpleContainerApp

4. Indiquez l’URL de l’image conteneur à partir d’un mis en pension DockerHub. Il prend la forme [mis en pension] / [nom de l’image]

![Générateur de service TISSU Yeoman des conteneurs][sf-yeoman]

## <a name="deploy-the-application"></a>Déploiement de l’application

Une fois l’application est créée, vous pouvez le déployez dans le cluster local à l’aide de l’infrastructure du langage commun Azure.

1. Se connecter pour le cluster tissu de Service local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilisez le script d’installation fourni dans le modèle pour copier le package d’application pour le magasin d’image du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

3. Ouvrez un navigateur et accédez au Service TISSU Explorer en http://localhost:19080/Explorer (remplacer hôte local avec l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X).

4. Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et l’autre pour la première instance de ce type.

5. Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance d’application et unregister le type d’application.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des conteneurs et tissu de Service](service-fabric-containers-overview.md)
- [Interaction avec les clusters tissu de Service à l’aide de l’infrastructure du langage commun Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

