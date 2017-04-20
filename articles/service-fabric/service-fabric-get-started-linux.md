<properties
   pageTitle="Configurer votre environnement de développement sur Linux | Microsoft Azure"
   description="Installez le runtime et le Kit de développement logiciel et créer un cluster de développement local sur Linux. Au terme de ce programme d’installation, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Préparer votre environnement de développement sous Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Pour déployer et exécuter des [applications Azure Service TISSU](service-fabric-application-model.md) sur votre ordinateur de développement Linux, installez le runtime et le Kit de développement logiciel courantes. Vous pouvez également installer facultatif SDK pour Java et .NET Core.

## <a name="prerequisites"></a>Conditions préalables
### <a name="supported-operating-system-versions"></a>Versions de système d’exploitation pris en charge
Les versions des systèmes d’exploitation suivants sont pris en charge pour le développement :

- Ubuntu 16.04 (Xerus Xenial)

## <a name="update-your-apt-sources"></a>Mettre à jour de vos sources susceptibles

Pour installer le Kit de développement et le package runtime associé par nature recevoir, vous devez tout d’abord mettre à jour vos sources susceptibles.

1. Ouvrez un terminal.
2. Ajouter la mis en pension tissu de Service à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Ajouter la nouvelle clé GPG à votre trousseau susceptibles de clés.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Actualisez vos listes de package basés sur les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Installer et configurer le Kit de développement

Une fois vos sources sont mis à jour, vous pouvez installer le Kit de développement.

1. Installez le package SDK tissu de Service. Vous êtes invité à confirmer l’installation et pour accepter un contrat de licence.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Exécuter le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurer l’infrastructure du langage commun disponibilité sur plusieurs plateformes Azure

L' [infrastructure du langage commun Azure disponibilité sur plusieurs plateformes] [ azure-xplat-cli-github] inclut des commandes pour interagir avec les entités tissu de Service, notamment les clusters et les applications. Il est basé sur Node.js c’est le cas [Vérifiez que vous avez installé le nœud] [ install-node] avant de poursuivre les instructions ci-dessous.

1. Dupliquer les mis en pension github à votre ordinateur de développement.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Basculer vers la mis en pension cloné et installer des dépendances de l’infrastructure du langage commun en utilisant le Gestionnaire de Package nœud (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Créer un type à partir du dossier bin/azure la mis en pension cloné à /usr/bin/azure afin qu’il est ajouté à votre chemin d’accès et commandes sont disponibles à partir de n’importe quel répertoire.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Pour finir, activer les commandes de Service tissu de la saisie semi-automatique.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurer un cluster local

Si tout a été installé correctement, vous devriez pouvoir démarrer un cluster local.

1. Exécuter le script de configuration de cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Ouvrez un navigateur web et accédez à http://localhost:19080/Explorer. Si le cluster a déjà commencé, vous devriez voir le tableau de bord Service TISSU Explorer.

    ![Service TISSU Explorer sous Linux][sfx-linux]

À ce stade, vous êtes en mesure de déployer des packages d’application Service TISSU prédéfinies ou nouveaux basés sur des conteneurs invité ou invité exécutable. Pour créer de nouveaux services à l’aide de la Java ou .NET Core SDK, suivez les étapes d’installation facultatifs ci-dessous.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Installez le Kit de développement logiciel Java et Eclipse néon plug-in (facultatif)

Le Kit de développement Java fournit les bibliothèques et les modèles nécessaires pour créer des services tissu de Service à l’aide de Java.

1. Installez le package Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Exécuter le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Vous pouvez installer le plug-in Eclipse pour tissu de Service à partir de l’IDE néon Eclipse.

1. Dans Eclipse, vérifiez que vous avez Buildship version 1.0.17 ou version ultérieure est installé. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’Installation**. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update].

2. Pour installer le plug-in tissu de Service, choisissez **Aide > installer le logiciel nouveau...**

3. Dans la zone de texte « Utiliser », entrez : http://dl.windowsazure.com/eclipse/servicefabric

4. Cliquez sur Ajouter.

    ![Plug-in Eclipse][sf-eclipse-plugin]

5. Sélectionnez le plug-in tissu de Service et cliquez sur Suivant.

6. Passez en revue l’installation et acceptez le contrat de licence utilisateur final.

## <a name="install-the-net-core-sdk-optional"></a>Installez le Kit de développement .NET Core (facultatif)

Le Kit de développement .NET Core fournit les bibliothèques et les modèles nécessaires pour créer des services tissu de Service à l’aide de la disponibilité sur plusieurs plateformes .NET Core.

1. Installez le package .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Exécuter le script de configuration du Kit de développement logiciel.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application Java sous Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Préparer votre environnement de développement sur OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
