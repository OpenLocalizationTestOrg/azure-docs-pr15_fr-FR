<properties
   pageTitle="Créer votre première application Service tissu sur Linux à l’aide de c# | Microsoft Azure"
   description="Créer et déployer une application de Service tissu à l’aide de C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Créer votre première application Azure Service TISSU

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Service TISSU fournit SDK pour générer des services sur Linux dans .NET Core et Java. Dans ce didacticiel, nous examinons comment créer une application pour Linux et générer un service à l’aide de c# (.NET Core).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, vérifiez que vous disposez de [configurer votre environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement une case Linux sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Créer l’application

Une application de Service TISSU peut contenir un ou plusieurs services, chacune avec un rôle spécifique à proposer des fonctionnalités de l’application. Le Kit de développement de tissu de Service pour Linux inclut un générateur [Yeoman](http://yeoman.io/) qui facilite pour créer votre premier service et ajouter d’autres ultérieurement. Nous allons utiliser Yeoman pour créer une application avec un seul service.

1. Dans un terminal, tapez la commande suivante pour commencer à créer la structure :`yo azuresfcsharp`

2. Nommez votre application.

3. Choisissez le type de votre premier service et nommez-la. Aux fins de ce didacticiel, nous choisir un Service acteur fiable.

  ![Générateur de service TISSU Yeoman pour C#][sf-yeoman]

>[AZURE.NOTE] Pour plus d’informations sur les options, voir [Présentation du modèle de programmation tissu de Service](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Créer l’application

Les modèles de Service TISSU Yeoman incluent un script de génération que vous pouvez utiliser pour créer l’application à partir du terminal (après déplacement vers le dossier d’application).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Démarrez le client de test et effectuez un basculement

Projets acteur ne font rien eux-mêmes. Ils ont besoin d’un autre service ou client pour leur envoyer des messages. Le modèle acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service acteur.

1. Exécuter le script à l’aide de l’utilitaire espion pour afficher la sortie du service acteur.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Dans l’Explorateur de tissu de Service, recherchez le nœud qui héberge réplica principal pour le service acteur. Dans la capture d’écran ci-dessous, il s’agit nœud 3.

    ![Recherche réplica principal dans l’Explorateur de tissu de Service][sfx-primary]

3. Cliquez sur le nœud que vous avez trouvé à l’étape précédente, puis sélectionnez **désactiver (redémarrage)** dans le menu Actions. Cette action redémarre un des cinq nœuds dans votre cluster local forcer un basculement à un réplica secondaire en cours d’exécution sur un autre nœud. Lorsque vous effectuez cette action, soyez attentif à la sortie à partir du client de test et notez que le compteur continue à incrémenter malgré le basculement.


## <a name="next-steps"></a>Étapes suivantes

- [Pour plus d’informations sur des intervenants fiable](service-fabric-reliable-actors-introduction.md)
- [Interaction avec les clusters tissu de Service à l’aide de l’infrastructure du langage commun Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
