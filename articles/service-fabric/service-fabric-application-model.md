<properties
   pageTitle="Modèle d’application service TISSU | Microsoft Azure"
   description="Découvrez comment modéliser et décrire les applications et services de tissu de Service."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Modéliser une application de Service TISSU

Cet article fournit une vue d’ensemble du modèle d’application Azure Service tissu. Il décrit également comment définir une application et le service via des fichiers manifeste et obtenir l’application empaquetés et prêt pour le déploiement.

## <a name="understand-the-application-model"></a>Comprendre le modèle d’application

Une application est un ensemble de services constituants qui exécutent une certaine fonction ou des fonctions. Un service effectue une fonction complète et autonome (il peut démarrer et exécuter indépendamment de celles d’autres services) et se compose de code, la configuration et données. Pour chaque service, code se compose des fichiers binaires exécutables et configuration se compose des paramètres de service qui peuvent être chargées au moment de l’exécution de données se composent de données statiques arbitraires et utilisées par le service. Chaque composant dans ce modèle d’application hiérarchique peut être gérées et mis à niveau séparément.

![Le modèle d’application tissu de Service][appmodel-diagram]


Type d’application se trouve une catégorisation d’une application et comprend un ensemble de types de service. Un type de service est une catégorisation d’un service. La catégorisation peut avoir des paramètres différents et configurations, mais la fonctionnalité principale reste la même. Les instances d’un service sont les variations de configuration de services différente du même type de service.  

Cours (ou « types ») des applications et services sont décrites dans les fichiers XML (manifestes d’application et service manifestes) qui se trouvent les modèles par rapport à laquelle les applications peuvent être instanciation à partir du magasin d’image du cluster. La définition de schéma pour le fichier ServiceManifest.xml et ApplicationManifest.xml est installée avec le Service TISSU SDK et les outils pour *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

Le code d’instances de l’autre application s’exécutera en tant que processus distincts même lorsque hébergé par le même nœud tissu de Service. En outre, le cycle de vie de chaque instance d’application pouvant être gérée (c'est-à-dire mise à niveau) séparément. Le diagramme suivant illustre la façon dont les types d’applications sont composés de types de service, qui à son tour sont composent de code, la configuration et packages. Pour simplifier le diagramme, seules les code/config/données packages pour `ServiceType4` sont affichés, bien que chaque type de service inclut certaines ou toutes les types de packages.

![Types d’applications tissu de service et les types de service][cluster-imagestore-apptypes]

Deux fichiers manifeste différentes sont utilisées pour décrire les applications et services : le service manifeste et le manifeste d’application. Celles-ci sont décrites en détail dans les sections résultante.

Une ou plusieurs instances d’un type de service peut être actif dans le cluster. Par exemple, instances de service dynamique, ou réplicas, assurer une grande fiabilité grâce à la réplication état entre réplicas situés sur différents nœuds dans le cluster. Cette réplication fournit essentiellement redondance pour le service soit disponible même si un nœud dans un cluster échoue. Un [service partitionnée](service-fabric-concepts-partitioning.md) davantage divise son état (et les modèles d’accès à cet état) entre les nœuds du cluster.

Le diagramme suivant montre la relation entre les applications et instances de service, partitions et duplications.

![Partitions et duplications au sein d’un service][cluster-application-instances]


>[AZURE.TIP] Vous pouvez afficher la mise en page des applications dans un cluster à l’aide de l’outil de Service TISSU Explorer disponible en http://&lt;yourclusteraddress&gt;: 19080/Explorer. Pour plus d’informations, voir [visualiser votre cluster avec l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Décrire un service

Le manifeste de service définit de manière déclarative le type de service et la version. Il spécifie les métadonnées de service tels que Type_Service propriétés état, l’équilibrage de charge des indicateurs, binaires du service et fichiers de configuration.  Autrement dit, il décrit les modules de code, la configuration et données qui composent un package de service pour prendre en charge un ou plusieurs types de service. Voici un exemple simple de manifeste de service :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Attributs de **version** sont des chaînes non structurées et non analysés par le système. Il s’agit des utilisés pour la version de chaque composant mises à jour.

**ServiceTypes** déclare quels types de service sont prises en charge par **CodePackages** dans ce manifeste. Lorsqu’un service instanciation par rapport à un de ces types de service, tous les modules de code déclarés dans ce manifeste sont activés en exécutant leurs points d’entrée. Les processus qui en résulte devraient s’enregistrer les types de service pris en charge en cours d’exécution. Notez que les types de service sont déclarées au niveau du manifest et non au niveau du package de code. Donc lorsqu’il y a plusieurs packages de code, ils sont tous activés chaque fois que le système de recherche pour l’un des types de service déclarés.

**SetupEntryPoint** est un point d’entrée dotés de privilèges qui s’exécute avec les mêmes informations d’identification en tant que Service tissu (généralement le compte *système local* ) avant le point d’entrée. Le fichier exécutable spécifié par un **point d’entrée** est généralement l’hôte de service longue. La présence d’un point d’entrée d’installation séparés évite d’avoir à exécuter l’hôte de service avec des privilèges élevés pour une période prolongée. Le fichier exécutable spécifié par un **point d’entrée** est exécuté après **SetupEntryPoint** a quitté avec succès. Le processus résultant est contrôlé et redémarré (commençant à nouveau **SetupEntryPoint**) si elle se termine jamais ou se bloque.

**DataPackage** déclare un dossier nommé par l’attribut **Name** , qui contient des données statiques arbitraires et utilisées par le processus en cours d’exécution.

**ConfigPackage** déclare un dossier nommé par l’attribut **Name** , qui contient un fichier *Settings.xml* . Ce fichier contient des sections de paramètres paire clé-valeur définies par l’utilisateur que le processus peut lire au moment de l’exécution. Au cours d’une mise à niveau, si une seule la **ConfigPackage** **version** a changé, puis le processus en cours d’exécution n’est pas redémarré. En revanche, un rappel vous avertit le processus qui les paramètres de configuration ont été modifiées afin qu’ils peuvent être recharger dynamiquement. Voici un exemple de fichier *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Manifeste d’un service peut contenir plusieurs code, la configuration et les packages de données. Chacun de ces peut être créée séparément.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Décrire une application


Le manifeste d’application décrit de manière déclarative le type d’application et la version. Il spécifie les métadonnées de composition de service tels que les noms stables, facteur de Nb/réplication instance, stratégie de sécurité/isolement, les contraintes de placement, remplacements de configuration et que les types de service constituant. Les domaines de l’équilibrage de charge dans lequel l’application est placée sont également décrites.

Par conséquent, un manifeste d’application décrit les éléments au niveau de l’application et fait référence à une ou plusieurs manifestes service pour composer un type d’application. Voici un exemple simple de manifeste d’application :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Comme service manifestes, les attributs de **Version** sont des chaînes non structurées et ne sont pas analysés par le système. Il s’agit également utilisés pour la version de chaque composant mises à jour.

**ServiceManifestImport** contient des références aux manifestes service qui composent ce type d’application. Service importées manifestes déterminent quels types de service sont valides dans ce type d’application.

**DefaultServices** déclare instances de service qui sont créés automatiquement chaque fois qu’une application instanciation par rapport à ce type d’application. Services par défaut sont simplement plus pratique et se comportent comme services normales à chaque égard après que qu’ils ont été créés. Ils sont mis à niveau en même temps que d’autres services dans l’instance d’application et peuvent également être supprimés.

> [AZURE.NOTE] Un manifeste d’application peut contenir plusieurs importations manifeste de service et les services par défaut. Chaque importation manifeste service peut être créée séparément.

Pour savoir comment mettre à jour d’application et les paramètres de service pour des environnements individuels, voir [Gestion des paramètres de l’application pour les environnements multiples](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Compresser une application

### <a name="package-layout"></a>Disposition de package

Le manifeste d’application, manifest(s) de service et autres fichiers de package nécessaires doivent être organisés dans une disposition spécifique pour le déploiement dans un cluster de tissu de Service. Les manifestes d’exemple dans cet article devront être organisées dans l’arborescence suivante :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Les dossiers sont nommés pour mettre en correspondance les attributs de **nom** de chaque élément correspondant. Par exemple, si le manifeste de service contenue deux packages code dont les noms **MyCodeA** et **MyCodeB**, puis deux dossiers avec les mêmes noms contient les fichiers binaires nécessaires pour chaque package de code.

### <a name="use-setupentrypoint"></a>Utiliser SetupEntryPoint

Scénarios courants d’utilisation **SetupEntryPoint** sont lorsque vous devez exécuter un fichier exécutable avant le démarrage du service, ou vous devez effectuer une opération avec des privilèges élevés. Par exemple :

- La configuration et l’initialisation de variables d’environnement nécessitant l’exécutable de service. Ce n’est pas limité à uniquement des exécutables écrits via les modèles de programmation tissu de Service. Par exemple, npm.exe doit certaines variables d’environnement configurés pour déployer une application node.js.

- La configuration de contrôle d’accès en installant des certificats de sécurité.

### <a name="build-a-package-by-using-visual-studio"></a>Créer un package à l’aide de Visual Studio

Si vous utilisez Visual Studio 2015 pour créer votre application, vous pouvez utiliser la commande Package pour créer automatiquement un package qui correspond à la mise en page ci-dessus.

Pour créer un package, cliquez sur le projet d’application dans l’Explorateur et choisissez la commande Package, comme indiqué ci-dessous :

![Compression d’une application avec Visual Studio][vs-package-command]

Lors de la création d’un package est terminée, vous trouverez l’emplacement du package dans la fenêtre de **sortie** . Notez que l’étape emballage s’affiche automatiquement lorsque vous déployez ou déboguez votre application dans Visual Studio.

### <a name="test-the-package"></a>Tester le package

Vous pouvez vérifier la structure de package localement via PowerShell à l’aide de la commande **ServiceFabricApplicationPackage de Test** . Cette commande vérifier les problèmes d’analyse de manifeste et vérifiez que toutes les références. Notez que cette commande vérifie uniquement l’exactitude structurel des répertoires et des fichiers dans le package. Il n'allez pas vérifier le contenu de package code ou des données au-delà de la vérification que tous les fichiers nécessaires sont présents.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Cette erreur indique que le fichier *MySetup.bat* référencé dans le service manifeste **SetupEntryPoint** est manquant dans le package de code. Une fois le fichier manquant est ajouté, la vérification de l’application passe :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Une fois que l’application est correctement empaquetée et transmet vérification, il est prêt pour le déploiement.

## <a name="next-steps"></a>Étapes suivantes

[Déployer et supprimer des applications][10]

[Gestion des paramètres de l’application pour les environnements multiples][11]

[RunAs : Exécution d’une application de Service tissu avec des autorisations de sécurité différent][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
