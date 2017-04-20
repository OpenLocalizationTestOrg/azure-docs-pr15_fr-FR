<properties
   pageTitle="Prise en main le déploiement et la mise à niveau des applications sur votre cluster local | Microsoft Azure"
   description="Configurer un cluster de Service TISSU local et déployer une application existante, puis mettre à niveau de cette application."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Prise en main le déploiement et la mise à niveau des applications sur votre cluster local
Le Service de tissu Azure SDK inclut un environnement de développement local complet que vous pouvez utiliser pour commencer rapidement à déployer et gérer des applications sur un cluster local. Dans cet article, vous créez un cluster local, déployez une application existante qui lui et puis mise à niveau de cette application vers une nouvelle version, à partir de Windows PowerShell.

> [AZURE.NOTE] Cet article suppose que vous avez déjà [configuré votre environnement de développement](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Créer un cluster local
Un cluster de Service TISSU représente un ensemble de ressources matérielles que vous pouvez déployer des applications à. En règle générale, un cluster est constitué de n’importe quel endroit de cinq à des milliers d’ordinateurs. Toutefois, le Kit de développement de tissu Service inclut une configuration cluster pouvant s’exécuter sur un seul ordinateur.

Il est important de comprendre que le cluster local tissu de Service n’est pas un émulateur ou simulator. Il s’exécute le même code de plateforme qui se trouve sur clusters comportant plusieurs ordinateurs. La seule différence est qu’il s’exécute les processus de plateforme qui sont en règle générale, répartis sur cinq ordinateurs sur un ordinateur.

Le Kit de développement fournit deux moyens de configurer un cluster local : un script Windows PowerShell et l’application de barre d’état système Gestionnaire de Cluster locale. Dans ce didacticiel, nous utilisons le script PowerShell.

> [AZURE.NOTE] Si vous avez déjà créé un cluster local en déployant une application à partir de Visual Studio, vous pouvez ignorer cette section.


1. Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur.

2. Exécuter le script de configuration de cluster à partir du dossier du Kit de développement logiciel :

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Programme d’installation cluster prend quelques instants. Une fois que le programme d’installation est terminée, vous devriez voir ressemblant à :

    ![Sortie du programme d’installation de cluster][cluster-setup-success]

    Vous êtes maintenant prêt à essayer le déploiement d’une application à votre cluster.

## <a name="deploy-an-application"></a>Déployer une application
Le Kit de développement de tissu de Service offre un large éventail de structures et développeur des outils de création d’applications. Si vous souhaitez savoir comment créer des applications dans Visual Studio, voir [créer votre première application Service tissu dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Dans ce didacticiel, nous allons utiliser un exemple d’application existant (appelé WordCount) afin que nous pouvons se concentrer sur les aspects de gestion de la plate-forme, y compris le déploiement, la surveillance et la mise à niveau.


1. Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur.

2. Importer le module Service TISSU SDK PowerShell.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Créez un répertoire pour stocker l’application que vous téléchargez et déployez, par exemple C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Télécharger l’application WordCount](http://aka.ms/servicefabric-wordcountapp) à l’emplacement que vous avez créé.  Remarque : le navigateur Microsoft Edge enregistre le fichier avec une extension *.zip* .  Remplacez l’extension de fichier *.sfpkg*.

5. Se connecter pour le cluster local :

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Créer une nouvelle application à l’aide de la commande déploiement du Kit de développement avec un nom et un chemin d’accès pour le package d’application.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Si tout se passe bien, vous devriez voir sortie comme suit :

    ![Déployer une application sur le cluster local][deploy-app-to-local-cluster]

7. Pour voir l’application en action, lancer le navigateur et accédez à [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Vous devriez voir :

    ![Application déployée l’interface utilisateur][deployed-app-ui]

    L’application WordCount est très simple. Il inclut le code de JavaScript côté client pour générer aléatoires cinq caractères « mots », qui sont ensuite transférées à l’application via API Web ASP.NET. Un service dynamique suit le nombre de mots prises en compte. Ils sont regroupées par le premier caractère du mot. Vous pouvez trouver le code source pour l’application WordCount dans les [exemples de mise en route](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    L’application que nous avons déployé contient quatre partitions. Afin que les mots qui commencent par les lettres A à G sont stockés dans la première partition, mots qui commencent par H à N sont stockés dans la deuxième partition et ainsi de suite.

## <a name="view-application-details-and-status"></a>Afficher les détails et l’état
À présent que nous avons déployé l’application, nous allons étudier certains détails de l’application dans PowerShell.

1. Requête déployées toutes les applications sur le cluster :

    ```powershell
    Get-ServiceFabricApplication
    ```

    En supposant que vous avez déployé uniquement l’application WordCount, vous voyez une semblable à :

    ![Interroger des applications tout déployées dans PowerShell][ps-getsfapp]

2. Accédez à un niveau avancé en interrogation de l’ensemble des services qui sont inclus dans l’application WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Liste des services pour l’application dans PowerShell][ps-getsfsvc]

    L’application est constituée de deux services--web frontal et le service dynamique qui gère les mots.

3. Enfin, consultez la liste des partitions pour WordCountService :

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Afficher les partitions service dans PowerShell][ps-getsfpartitions]

    L’ensemble des commandes que vous avez utilisé, ainsi que toutes les commandes PowerShell tissu de Service, sont disponibles pour un cluster que vous pouvez vous connecter, locale ou distante.

    Pour un moyen plus visuel interagir avec le cluster, vous pouvez utiliser l’outil de Service TISSU Explorer-based en accédant à [http://localhost:19080/Explorer](http://localhost:19080/Explorer) dans le navigateur.

    ![Afficher les détails d’application dans l’Explorateur de tissu de Service][sfx-service-overview]

    > [AZURE.NOTE] Pour plus d’informations sur l’Explorateur de tissu de Service, voir [visualiser votre cluster avec l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Mise à niveau une application
Service TISSU fournit des mises à niveau sans interruption de service non par analyse du fonctionnement de l’application comme il déploie au sein du cluster. Nous allons effectuer une mise à niveau simple de l’application WordCount.

La nouvelle version de l’application maintenant compte uniquement les mots qui commencent par une voyelle. Comme la mise à niveau déploie, nous voyons deux modifications apportées au comportement de l’application. Tout d’abord, la fréquence à laquelle le nombre augmente doit ralentir, étant donné que moins de mots sont comptés. Ensuite, étant donné que la première partition comporte deux voyelles (A et E), et toutes les autres partitions contient un seul chaque, son décompte doit commencer par la suite de dépassement des autres colonnes.

1. [Télécharger le package v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) au même emplacement dans lequel vous avez téléchargé le package v1.

2. Revenez à la fenêtre PowerShell et commande de mise à niveau du Kit de développement permet d’enregistrer la nouvelle version du cluster. Puis commencer la mise à niveau de la structure : / application WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Vous devriez voir le résultat dans PowerShell qui ressemble à ce qui suit la mise à niveau commence.

    ![Mise à niveau de l’avancement dans PowerShell][ps-appupgradeprogress]

3. Pendant la mise à niveau est en cours, il peut s’avérer plus facile de contrôler son statut à partir de l’Explorateur de tissu de Service. Ouvrir une fenêtre de navigateur et accédez à [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Développez des **Applications** dans l’arborescence à gauche, puis choisissez **WordCount**et enfin **TISSU : / WordCount**. Dans l’onglet essentials, vous verrez l’état de la mise à niveau mesure qu’elles traversent domaines mise à niveau du cluster.

    ![Mise à niveau de l’avancement dans l’Explorateur de tissu de Service][sfx-upgradeprogress]

    Au fur et la mise à niveau à chaque domaine, vérifications au niveau sont effectuées pour vous assurer que l’application semble fonctionner correctement.

4. Si vous exécutez à nouveau la requête précédente pour l’ensemble des services dans la structure : / application WordCount, notez que la version de WordCountService modifiée mais que la version de WordCountWebService n’a pas :

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Services d’application requête après mise à niveau][ps-getsfsvc-postupgrade]

    Cela met en surbrillance comment TISSU Service gère les mises à niveau de l’application. Elle touche uniquement l’ensemble des services (ou colis code/configuration au sein de ces services) qui ont été modifiées, ce qui permet du processus de mise à niveau plus rapide et plus fiable.

5. Enfin, retourner au navigateur pour observer le comportement de la nouvelle version de l’application. Comme prévu, le nombre progresse plus lentement, et la première partition finit avec légèrement plus du volume.

    ![Afficher la nouvelle version de l’application dans le navigateur][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Nettoyage des

Avant de résumé, il est important n’oubliez pas que le cluster local est de type real. Applications continuent à s’exécuter en arrière-plan jusqu'à ce que vous les supprimer.  Selon la nature de vos applications, une application en cours d’exécution peut prendre jusqu'à des ressources importantes sur votre ordinateur. Vous disposez de plusieurs options pour gérer les applications et le cluster :

1. Pour supprimer une application individuelle et toutes ses données, exécutez la commande suivante :

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Sinon, supprimez l’application dans le menu **ACTIONS** d’Explorer tissu de Service ou le menu contextuel dans la liste application du volet gauche.

    ![Supprimer une application est Service TISSU Explorer][sfe-delete-application]

2. Après la suppression de l’application à partir du cluster, vous pouvez annuler l’enregistrement de versions 1.0.0 et 2.0.0 WordCount application de type. Suppression supprime les packages d’application, y compris le code et la configuration, à partir du magasin d’image du cluster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Ou, dans l’Explorateur de tissu de Service, choisissez **Le Type de la mise hors service** pour l’application.

3. Pour arrêter le cluster tout en conservant les données d’application et traces, cliquez sur **Arrêter le Cluster Local** dans l’application de barre d’état système.

4. Pour supprimer complètement le cluster, cliquez sur **Supprimer le Cluster Local** dans l’application de barre d’état système. Cette option entraîne dans un autre déploiement lent la prochaine fois que vous appuyez sur F5 dans Visual Studio. Supprimer le cluster local uniquement si vous ne prévoyez d’utiliser depuis un certain temps ou si vous avez besoin récupérer des ressources.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nœud et le mode cluster nœud 5

Lorsque vous travaillez avec le cluster local pour développer des applications, vous trouvez souvent vos déplacements itérations rapides de l’écriture de code, débogage modification du code, etc. débogage. Pour optimiser ce processus, le cluster local peut exécuter dans deux modes : 5 ou 1 nœud. Les deux modes cluster ont leurs avantages.
Le mode cluster nœud 5 vous permet de travailler avec un cluster réel. Vous pouvez tester les scénarios de basculement, travailler avec plusieurs instances et les copies de vos services.
Le mode cluster nœud 1 est optimisé pour effectuer un déploiement rapide et enregistrement des services, pour vous aider à valider rapidement le code à l’aide de l’exécution de tissu de Service.

À la fois 1 nœud mode cluster et le mode cluster nœud 5 n’est pas un émulateur ou simulator. Il s’exécute le même code de plateforme qui se trouve sur clusters comportant plusieurs ordinateurs.

> [AZURE.NOTE] Cette fonctionnalité est disponible dans le Kit de développement logiciel version 5,2. et au-dessus.

Pour modifier le mode de calcul à un cluster de nœud 1, utilisez le Gestionnaire de Cluster Service TISSU locale ou PowerShell comme suit :

1. Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur.

2. Exécuter le script de configuration de cluster à partir du dossier du Kit de développement logiciel :

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Programme d’installation cluster prend quelques instants. Une fois que le programme d’installation est terminée, vous devriez voir ressemblant à :
    
    ![Sortie du programme d’installation de cluster][cluster-setup-success-1-node]

Si vous utilisez le Gestionnaire de Cluster Service TISSU Local :

![Mode de calcul][switch-cluster-mode]

> [AZURE.WARNING] Lorsque vous modifiez le mode cluster, le cluster actuel est supprimé de votre système et un nouveau cluster est créé. Les données que vous devez stockées dans le cluster sont supprimés lorsque vous passez en mode de calcul.

## <a name="next-steps"></a>Étapes suivantes
- À présent que vous avez déployée et mis à niveau de certaines applications prédéfinies, vous pouvez [essayer de créer votre propre dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Les actions exécutées sur le cluster local dans cet article peuvent être effectuées sur un [cluster Azure](service-fabric-cluster-creation-via-portal.md) .
- La mise à niveau qui nous effectuées dans cet article a été simple. Consultez la [documentation de mise à niveau](service-fabric-application-upgrade.md) vers plus d’informations sur la puissance et la flexibilité des mises à niveau du Service tissu.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
