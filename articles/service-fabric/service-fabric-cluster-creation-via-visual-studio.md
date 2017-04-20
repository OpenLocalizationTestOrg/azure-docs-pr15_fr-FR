<properties
   pageTitle="La configuration d’un cluster de tissu de Service à l’aide de Visual Studio | Microsoft Azure"
   description="Décrit comment configurer un cluster tissu de Service à l’aide du Gestionnaire de ressources Azure modèle créé par un projet de groupe de ressources Azure dans Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurer un cluster tissu de Service à l’aide de Visual Studio
Cet article décrit comment configurer un cluster Azure Service tissu à l’aide de Visual Studio et un modèle d’Azure le Gestionnaire de ressources. Nous utiliserons un projet de groupe de ressources Visual Studio Azure pour créer le modèle. Une fois que le modèle a été créé, il peut être déployé directement sur Azure à partir de Visual Studio. Il peut également être utilisé à partir d’un script ou dans le cadre de facilité d’intégration continue (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Créer un modèle de cluster tissu de Service à l’aide d’un projet de groupe de ressources Azure
Pour commencer, ouvrez Visual Studio et créer un projet de groupe de ressources Azure (il est disponible dans le dossier **Cloud** ) :

![Boîte de dialogue Nouveau projet avec le projet de groupe de ressources Azure sélectionné][1]

Vous pouvez créer une solution Visual Studio pour ce projet ou ajouter à une solution existante.

>[AZURE.NOTE] Si vous ne voyez pas le projet de groupe de ressources Azure sous le nœud Cloud, vous n’avez pas le Kit de développement Azure installé. Lancement Web Platform Installer ([Installer maintenant](http://www.microsoft.com/web/downloads/platform.aspx) si vous n’avez pas déjà), puis recherchez « Azure SDK pour .NET » et installer la version est compatible avec votre version de Visual Studio.

Après avoir appuyé sur le bouton OK, Visual Studio vous demandera de sélectionner le modèle de gestionnaire de ressources que vous voulez créer :

![Sélectionner la boîte de dialogue modèle Azure avec sélection du modèle Service TISSU Cluster][2]

Sélectionnez le modèle de Service TISSU Cluster et cliquez à nouveau sur le bouton OK. Le projet et le modèle de gestionnaire de ressources ont été créés.

## <a name="prepare-the-template-for-deployment"></a>Préparer le modèle pour le déploiement
Avant de déployer le modèle pour créer le cluster, vous devez fournir des valeurs pour les paramètres de modèle requis. Ces valeurs de paramètre sont lues à partir de la `ServiceFabricCluster.parameters.json` fichier qui se trouve dans le `Templates` dossier du projet de groupe de ressources. Ouvrez le fichier et fournir les valeurs suivantes :

|Nom du paramètre           |Description|
|-----------------------  |--------------------------|
|adminUserName            |Le nom du compte d’administrateur pour tissu de Service machines (nœuds).|
|certificateThumbprint    |L’empreinte numérique du certificat qui permet de sécuriser le cluster.|
|sourceVaultResourceId    |*Nº ressource* de l’archivage sécurisé clé où est stocké le certificat qui permet de sécuriser le cluster.|
|certificateUrlValue      |L’URL du certificat de sécurité cluster.|

Le modèle de gestionnaire de ressources de Visual Studio Service TISSU crée un cluster sécurisé qui est protégé par un certificat. Ce certificat est identifié par les paramètres de trois derniers modèle (`certificateThumbprint`, `sourceVaultValue`, et `certificateUrlValue`), et il doit exister dans un **L’archivage sécurisé de clé Azure**. Pour plus d’informations sur la création du certificat de sécurité cluster, voir l’article de [Service TISSU cluster scénarios de sécurité](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Facultatif : modifier le nom du cluster
Chaque cluster tissu de Service a un nom. Lorsqu’un cluster tissu est créé dans Azure, nom de cluster détermine (ainsi que la région Azure) le nom du système DNS (Domain Name) pour le cluster. Par exemple, si vous nommez votre cluster `myBigCluster`et l’emplacement (Azure région) du groupe de ressources hébergeant le nouveau cluster est US Extrême-Orient, le nom DNS du cluster sera `myBigCluster.eastus.cloudapp.azure.com`.

Par défaut le nom du cluster est généré automatiquement et rendu unique en associant un suffixe aléatoire à un préfixe « cluster ». Cela rend très facile à utiliser le modèle dans le cadre d’un système d' **intégration continue** (CI). Si vous souhaitez utiliser un nom pour votre cluster spécifique, qui a un sens pour vous, définissez la valeur de la `clusterName` variables dans le fichier de modèle Gestionnaire de ressources (`ServiceFabricCluster.json`) pour le nom de votre choix. Il est la première variable définie dans ce fichier.

## <a name="optional-add-public-application-ports"></a>Facultatif : ajoutez des ports d’application public
Vous souhaitiez également modifier les ports application public pour le cluster avant de déployer. Par défaut, le modèle s’ouvre seulement deux ports TCP public (80 et 8081). Si vous avez besoin de plus de vos applications, modifiez la définition d’équilibrage de charge Azure dans le modèle. La définition est stockée dans le fichier de modèle principale (`ServiceFabricCluster.json`). Ouvrez le fichier et recherchez `loadBalancedAppPort`. Chaque port est associé à trois objets :

1. Variable de modèle qui définit la valeur du port TCP pour le port :

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. *Sonde* qui définit la fréquence et l’intervalle de temps au chargement Azure équilibrage tente d’utiliser un nœud TISSU Service spécifique avant de basculer vers un autre. Les sondes font partie de la ressource d’équilibrage de charge. Voici la définition de sonde pour le premier port d’application par défaut :

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Une *règle d’équilibrage de charge* qui relie le port et la sonde, qui permet à un ensemble de nœuds de cluster tissu de Service d’équilibrage de charge :

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Si les applications que vous comptez déployer au cluster ont besoin de davantage de ports, vous pouvez les ajouter en sonde supplémentaire création et l’équilibrage de charge les définitions de règles. Pour plus d’informations sur l’utilisation des équilibrage de charge Azure via des modèles de gestionnaire de ressources, voir [créer un équilibrage de charge interne à l’aide d’un modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Déployez le modèle à l’aide de Visual Studio
Après avoir enregistré toutes les valeurs de paramètre requis dans la`ServiceFabricCluster.param.dev.json` fichier, vous êtes prêt à déployer le modèle et créez votre cluster tissu de Service. Cliquez sur le projet de groupe de ressources dans l’Explorateur de Visual Studio et choisissez **déployer | Nouveau déploiement...**. Si nécessaire, Visual Studio affiche la boîte de dialogue **déployer au groupe de ressources** , vous demandant s’authentifier à Azure :

![Déployer à boîte de dialogue groupe de ressources][3]

La boîte de dialogue vous permet de choisir un groupe de ressources le Gestionnaire de ressources existant pour le cluster et vous donne la possibilité d’en créer un nouveau. Normalement, il est préférable d’utiliser un groupe de ressources distinct pour un cluster de tissu de Service.

Après avoir appuyé sur le bouton déployer, Visual Studio vous invite à confirmer les valeurs de paramètre de modèle. Cliquez sur le bouton **Enregistrer** . Un seul paramètre n’a pas une valeur persistante : le mot de passe du compte d’administrateur pour le cluster. Vous devez fournir une valeur de mot de passe lorsque Visual Studio vous en demande un.

>[AZURE.NOTE] En commençant par Azure SDK 2,9, Visual Studio prend en charge les mots de passe lecture de **L’archivage sécurisé de clé Azure** pendant le déploiement. Dans la boîte de dialogue de paramètres de modèle Remarquez que la `adminPassword` zone de texte paramètre comprend une petite icône « clé » à droite. Cette icône vous permet de sélectionner un code secret l’archivage sécurisé clés existant en tant que le mot de passe d’administration pour le cluster. Assurez-vous d’abord activé le Gestionnaire de ressources Azure access pour le déploiement de modèle dans les stratégies d’accès avancés de votre l’archivage sécurisé clé. 

Vous pouvez surveiller l’avancement du processus de déploiement dans la fenêtre de sortie de Visual Studio. Une fois le déploiement de modèle est terminé, votre nouveau cluster est prêt à utiliser.

>[AZURE.NOTE] Si PowerShell a jamais utilisé pour administrer Azure à partir de l’ordinateur que vous utilisez, vous devez effectuer quelques maintenance.
>1. Activer l’écriture de script PowerShell en exécutant la [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) commande. Pour les ordinateurs de développement, stratégie « illimité » est généralement acceptable.
>2. Décider si vous souhaitez autoriser la collecte de données de diagnostic à partir des commandes PowerShell Azure, puis exécutez [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) si nécessaire. Cela permet d’éviter les invites inutiles au cours du déploiement du modèle.

S’il existe des erreurs, accédez au [portail Azure](https://portal.azure.com/) et ouvrez le groupe de ressources que vous avez déployé sur. Cliquez sur **tous les paramètres**, puis sur **les déploiements** sur la carte de paramètres. Échec du déploiement de groupe de ressources laisse les informations de diagnostic détaillées il.

>[AZURE.NOTE] Service TISSU clusters nécessitent un certain nombre de nœuds à être opérationnel pour garantir disponibilité et conserver l’état - appelée « maintenance quorum ». Par conséquent, il n’est pas approuvé arrêter tous les ordinateurs du cluster, sauf si vous avez effectué tout d’abord une [sauvegarde de votre état](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur la configuration cluster tissu de Service à l’aide du portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Découvrez comment gérer et déployer des applications de Service tissu à l’aide de Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
