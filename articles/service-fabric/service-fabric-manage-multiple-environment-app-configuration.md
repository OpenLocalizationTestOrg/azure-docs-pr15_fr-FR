<properties
   pageTitle="Gérer plusieurs environnements dans Service TISSU | Microsoft Azure"
   description="Applications de service TISSU peuvent être exécutées sur des clusters dont la taille d’un ordinateur à des milliers de machines est comprise. Dans certains cas, vous devez configurer votre application différemment pour les environnements variés. Cet article explique comment définir les paramètres d’application différente par environnement."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/19/2016"
   ms.author="seanmck"/>

# <a name="manage-application-parameters-for-multiple-environments"></a>Gérer les paramètres de l’application pour les environnements multiples

Vous pouvez créer Azure Service TISSU clusters à l’aide de n’importe où depuis un à plusieurs milliers d’ordinateurs. Tandis que les fichiers binaires d’application peuvent s’exécuter sans modification sur ce large éventail d’environnements, vous souhaiterez souvent configurer l’application différemment, selon le nombre d’ordinateurs que vous êtes le déploiement.

Un exemple simple, il est conseillé de `InstanceCount` d’un service sans état. Lorsque vous exécutez des applications dans Azure, vous souhaiterez généralement définissez ce paramètre à la valeur spéciale « -1 ». Cela garantit que votre service s’exécute sur tous les nœuds du cluster. Toutefois, cette configuration n’est pas adaptée à un seul ordinateur cluster dans la mesure où vous ne peut pas contenir plusieurs processus à l’écoute sur le point de terminaison même sur un seul ordinateur. À la place, vous affecterez généralement `InstanceCount` sur « 1 ».

## <a name="specifying-environment-specific-parameters"></a>Spécification des paramètres spécifiques à l’environnement

La solution à ce problème de configuration est un ensemble de services par défaut paramétrée et fichiers de paramètre d’application renseignez ces valeurs de paramètre pour un environnement donné. Services par défaut et les paramètres d’application sont configurés dans les manifestes d’application et des services. La définition de schéma pour les fichiers ServiceManifest.xml et ApplicationManifest.xml est installée avec le Service TISSU SDK et les outils pour *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Services par défaut

Applications de service tissu sont constituées d’une collection d’instances de service. S’il est possible que vous créez une application vide, puis créez dynamiquement toutes les instances de service, la plupart des applications ont un ensemble de core services qui doivent toujours être créés lors de l’application est instanciation. Celles-ci sont appelées « services par défaut ». Ils sont spécifiés dans le manifeste d’application, avec des espaces réservés pour une configuration per environnement inclus entre crochets :

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type"
                TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
                MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]"
                    LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Chacun des paramètres nommés doit être défini dans l’élément de paramètres du manifeste d’application :

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

L’attribut ValeurParDéfaut spécifie la valeur à utiliser en l’absence d’un paramètre plus spécifiques pour un environnement donné.

>[AZURE.NOTE] Ne conviennent pas tous les paramètres de l’instance service pour une configuration per environnement. Dans l’exemple ci-dessus, les valeurs LowKey et HighKey de jeu partition du service définis explicitement toutes les instances du service dans la mesure où la plage de partition est une fonction du domaine de données, pas l’environnement.


### <a name="per-environment-service-configuration-settings"></a>Paramètres de configuration de service par environnement

Le [modèle d’application Service TISSU](service-fabric-application-model.md) permet aux services inclure les packages configuration qui contiennent des paires clé-valeur personnalisées qui sont lisibles en cours d’exécution. Les valeurs de ces paramètres peuvent également être différencier en environnement en spécifiant une `ConfigOverride` dans le manifeste d’application.

Supposons que vous ayez le paramètre suivant dans le fichier Config\Settings.xml pour la `Stateful1` service :


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Pour ignorer cette valeur pour une paire d’application/environnement spécifique, créez un `ConfigOverride` lorsque vous importez le manifeste de service dans le manifeste d’application.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Ce paramètre peut ensuite être configuré par environnement comme indiqué ci-dessus. Vous pouvez le faire en déclarer dans la section Paramètres du manifeste d’application et en spécifiant des valeurs spécifiques à l’environnement dans les fichiers de paramètres d’application.

>[AZURE.NOTE] Dans le cas des paramètres de configuration du service, il existe trois emplacements où la valeur d’une clé peut être définie : le package de configuration du service, le manifeste d’application et le fichier de paramètres d’application. Service TISSU choisiront toujours à partir du fichier de paramètre application tout d’abord (si spécifié), puis le manifeste d’application et enfin le package de configuration.


### <a name="application-parameter-files"></a>Fichiers de paramètres d’application

Le projet d’application tissu de Service peut inclure un ou plusieurs fichiers de paramètre d’application. Chacun d’eux définit les valeurs spécifiques pour les paramètres définis dans le manifeste d’application :

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Par défaut, une nouvelle application inclut deux fichiers paramètre d’application, nommés Local.xml et Cloud.xml :

![Fichiers de paramètre d’application dans l’Explorateur][app-parameters-solution-explorer]

Pour créer un nouveau fichier de paramètre, simplement copier et coller une existante, puis lui attribuer un nouveau nom.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identification des paramètres d’environnement spécifiques au cours du déploiement

Au moment du déploiement, vous devez choisir le fichier de paramètre approprié à appliquer à votre application. Vous pouvez le faire via la boîte de dialogue Publier dans Visual Studio ou PowerShell.

### <a name="deploy-from-visual-studio"></a>Déploiement de Visual Studio

Vous pouvez choisir dans la liste des fichiers de paramètres disponibles lorsque vous publiez votre application dans Visual Studio.

![Choisir un fichier de paramètre dans la boîte de dialogue Publier][publishdialog]

### <a name="deploy-from-powershell"></a>Déployer à partir de PowerShell

La `Deploy-FabricApplication.ps1` script PowerShell inclus dans le modèle de projet application accepte un profil de publication en tant que paramètre et la PublishProfile contient une référence au fichier de paramètres de l’application.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur certains des principaux concepts qui sont présentées dans cette rubrique, consultez la [présentation technique tissu de Service](service-fabric-technical-overview.md). Pour plus d’informations sur les autres fonctionnalités de gestion de l’application qui sont disponibles dans Visual Studio, voir [gérer vos applications de Service tissu dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
