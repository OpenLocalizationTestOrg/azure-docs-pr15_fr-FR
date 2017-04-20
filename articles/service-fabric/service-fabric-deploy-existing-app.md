<properties
   pageTitle="Déployer un exécutable existant pour tissu de Service Azure | Microsoft Azure"
   description="Procédure compresser une application existante en tant qu’invité exécutable, afin qu’il peut être déployé sur un cluster de tissu de Service"
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
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Déployer un invité exécutable pour tissu de Service

Vous pouvez exécuter n’importe quel type d’application, tel que node.js, Java ou applications natives dans Azure Service tissu. Service TISSU fait référence à ces types d’applications en tant qu’invité exécutables.
Invité exécutables sont traités par tissu de Service telles que les services sans état. Par conséquent, ils sont placés sur des nœuds dans un cluster, en fonction de la disponibilité et d’autres indicateurs. Cet article décrit comment empaqueter et déployer un invité exécutable vers un cluster de tissu de Service, à l’aide de Visual Studio ou un utilitaire de ligne de commande.

Dans cet article aborde les étapes pour déployer pour tissu de Service et de package exécutable qu’invité.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Avantages de l’exécution d’invité TISSU Service un fichier exécutable

Il existe plusieurs avantages fournis avec un invité exécutable dans un cluster de tissu de Service en cours d’exécution :

- Disponibilité. Applications qui s’exécutent dans le Service tissu sont rendues disponibles. Service TISSU garantit que les instances d’une application sont exécutent.
- Contrôle d’état. Contrôle d’état TISSU service détecte si une application est en cours d’exécution et fournit des informations de diagnostic s’il existe une défaillance.   
- Gestion du cycle de vie des applications. En plus de fournir des mises à niveau sans interruption de service, Service TISSU fournit la restauration automatique à la version précédente s’il existe un événement d’état incorrect signalé au cours d’une mise à niveau.    
- Densité. Vous pouvez exécuter plusieurs applications dans un cluster, ce qui évite d’avoir chaque application peut s’exécuter sur sa propre matériel.


## <a name="overview-of-application-and-service-manifest-files"></a>Vue d’ensemble des applications et les fichiers manifeste de service

Dans le cadre du déploiement d’un invité exécutable, il est utile de comprendre l’emballage tissu de Service et modèle de déploiement comme décrit le [modèle d’application](service-fabric-application-model.md). Le modèle d’emballage TISSU Service repose sur deux fichiers XML : les manifestes d’application et des services. La définition de schéma pour les fichiers ApplicationManifest.xml et ServiceManifest.xml est installée avec le Kit de développement de tissu de Service dans *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifeste d’application** Le manifeste d’application est utilisé pour décrire l’application. Il répertorie les services qui composent il et autres paramètres qui permettent de définir comment l’un ou plusieurs services doivent être déployés, telles que le nombre d’instances.

  Dans la structure de Service une application est une unité de déploiement et de mise à niveau. Une application peut être mis à niveau comme une seule unité où est gérées les éventuels échecs et annulation potentielles. Service TISSU garantit que le processus de mise à niveau est réussie, ou, si la mise à niveau échoue, ne laisse pas l’application dans un état inconnu/instable.

* **Manifeste de service** Le manifeste de service décrit les composants d’un service. Il inclut les données, tels que le nom et le type de service et son code, la configuration et les données. Le manifeste de service inclut également des paramètres supplémentaires pouvant être utilisées pour configurer le service une fois qu’elle est déployée.


## <a name="application-package-file-structure"></a>Structure de fichier de package application
Pour déployer une application pour tissu de Service, l’application doit suivre une structure de répertoire prédéfini. L’exemple suivant de cette structure.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

La ApplicationPackageRoot contient le fichier ApplicationManifest.xml qui définit l’application. Un sous-répertoire pour chaque service inclus dans l’application est utilisé pour contenir toutes les les objets nécessitant le service--la ServiceManifest.xml et en règle générale, les trois répertoires suivants :

- *Code*. Ce répertoire contient le code de service.
- *Configuration de la*. Ce répertoire contient un fichier Settings.xml (et autres fichiers si nécessaire) que le service peut accéder en cours d’exécution pour extraire les paramètres de configuration spécifiques.
- *Données*. Il s’agit d’un répertoire supplémentaire pour stocker des données locales supplémentaires que le service peut avoir besoin. Remarque : Les données doivent être utilisées pour ne stocker que des données éphémères. Service tissu ne pas copier/répliquer les modifications au répertoire de données si le service doit être déplacé : par exemple, au cours du basculement.

Remarque : Vous n’avez pas à créer le `config` et `data` répertoires si vous n’en avez pas besoin.

## <a name="packaging-an-existing-executable"></a>Le conditionnement un exécutable existant

Lors de la compression d’un fichier exécutable invité, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou [créer manuellement le package d’application](#manually). À l’aide de Visual Studio, la structure de package d’application et les fichiers manifeste sont créés par l’Assistant Nouveau projet à votre place.

>[AZURE.NOTE] Pour créer un package un exécutable dans un service Windows existant, le plus simple consiste à utiliser Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>À l’aide de Visual Studio pour créer un package un exécutable existant

Visual Studio fournit un modèle de service tissu de Service pour vous aider à déployer un invité exécutable vers un cluster de tissu de Service.

Accédez aux étapes suivantes pour terminer la publication :

1. Sélectionnez Fichier -> Nouveau projet et créez une Application de tissu de Service.
2. Sélectionnez fichier exécutable invité en tant que le modèle de Service.
3. Cliquez sur Parcourir pour sélectionner le dossier avec votre fichier exécutable, remplissez le reste des paramètres pour créer le service.
    - *Comportement de code de Package* peuvent être définies pour copier tout le contenu de votre dossier dans le projet Visual Studio, qui est utile si le fichier exécutable ne change pas. Si vous prévoyez de l’exécutable à modifier, puis souhaitent pouvoir décrochez dynamiquement de nouvelles versions, vous pouvez choisir de créer un lien vers le dossier à la place. Notez que vous pouvez utiliser des dossiers liés lorsque vous créez le projet d’application dans Visual Studio. Cette fournit des liens vers l’emplacement source à partir du projet, ce qui vous permet de mettre à jour l’invité exécutable dans sa destination source, présentant les mises à jour deviennent partie intégrante du package d’application sur la version.
    - *Programme* - cliquez sur le fichier exécutable qui doit être exécuté pour démarrer le service.
    - *Arguments* - spécifier les arguments doivent être passés à l’exécutable. Il peut être une liste des paramètres avec arguments.
    - *WorkingFolder* - Spécifie le répertoire de travail pour le processus qui va agir en pour qu’il démarre. Vous pouvez spécifier trois valeurs :
        - `CodeBase`Spécifie que le répertoire de travail va être définie à l’annuaire de code dans le package d’application (`Code` répertoire dans le fichier présentée précédant).
        - `CodePackage`Spécifie que le répertoire de travail va être définie à la racine du package d’application (`GuestService1Pkg` dans le fichier présentée précédant).
        - `Work`Spécifie que les fichiers sont placés dans un sous-répertoire appelé travail
4. Donnez un nom à votre service, puis cliquez sur OK.
5. Si votre service a besoin d’un point de terminaison de communication, vous pouvez désormais ajouter le protocole, Port et le Type dans le fichier ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Vous pouvez maintenant utiliser le package et publier action par rapport à votre cluster local par déboguer la solution dans Visual Studio. Lorsque vous êtes prêt que vous pouvez publier l’application sur un cluster distant ou archiver la solution au contrôle de source.
7. Accédez à la fin de cet article pour savoir comment afficher vous service exécutable invité qui s’exécute dans l’Explorateur de tissu de Service.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Manuellement empaqueter et déployer un exécutable existant
Le processus d’emballage manuellement un fichier exécutable invité est basé sur les opérations suivantes :

1. Créer la structure du répertoire package.
2. Ajouter des fichiers de code et la configuration de l’application.
3. Modifier le fichier manifeste du service.
4. Modifier le fichier manifeste d’application.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Créer la structure du répertoire package
Vous pouvez commencer par créer la structure du répertoire, comme décrit plus haut.

### <a name="add-the-applications-code-and-configuration-files"></a>Ajouter des fichiers de code et la configuration de l’application
Après avoir créé la structure du répertoire, vous pouvez ajouter le code de l’application et fichiers de configuration dans les répertoires de code et config. Vous pouvez également créer des répertoires supplémentaires ou sous-répertoires sous les répertoires code ou la configuration.

Service tissu effectue une x-copie du contenu du répertoire racine, par conséquent, il n’existe aucune structure prédéfini à utiliser d’autres que la création de deux répertoires supérieure, code et les paramètres. (Vous pouvez choisir des noms différents si vous voulez. Obtenir plus d’informations sont dans la section suivante.)

>[AZURE.NOTE] Vous assurer que vous incluez toutes les fichiers/dépendances nécessaires à l’application. Service TISSU copie le contenu du package d’application sur tous les nœuds du cluster où les services de l’application passent à déployer. Le package doit contenir tout le code de l’application doit s’exécuter. Nous ne recommandons pas en supposant que les dépendances sont déjà installées.

### <a name="edit-the-service-manifest-file"></a>Modifier le fichier manifeste de service
L’étape suivante consiste à modifier le fichier manifeste de service pour inclure les informations suivantes :

- Le nom du type de service. Il s’agit d’un numéro d’identification TISSU Service utilise pour identifier un service.
- La commande à utiliser pour lancer l’application (ExeHost).
- N’importe quel script qui doit être exécuté pour définir haut/configurer l’application (SetupEntrypoint).

Voici un exemple d’un `ServiceManifest.xml` fichier :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Passons les différentes parties du fichier que vous devez mettre à jour :

#### <a name="updating-the-servicetypes"></a>Mise à jour de la ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Vous pouvez choisir n’importe quel nom que vous voulez pour `ServiceTypeName`. La valeur est utilisée dans la `ApplicationManifest.xml` fichier pour identifier le service.
- Vous devez spécifier `UseImplicitHost="true"`. Cet attribut indique Service tissu que le service est basé sur une application autonome, afin que tous les Service TISSU a à faire est démarrée comme un processus et contrôler son état.

#### <a name="updating-the-codepackage"></a>Mise à jour de la CodePackage
L’élément CodePackage indique l’emplacement (et version) de code du service.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

La `Name` élément est utilisé pour spécifier le nom de l’annuaire dans le package d’application qui contient le code du service. `CodePackage`est également la `version` attribut. Cela peut être utilisé pour spécifier la version du code--et peut peut également être utilisé pour mettre à niveau du code à l’aide d’infrastructure de gestion du cycle de vie du Service TISSU application.
#### <a name="optional-updating-the-setupentrypoint"></a>Facultatif : Mise à jour de la SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L’élément SetupEntrypoint est utilisé pour spécifier un fichier exécutable ou le lot doit être exécuté avant le code du service est lancé. Il est une étape facultative, afin qu’il n’a pas besoin d’inclure si aucune initialisation/paramétrage n’est requis. Le SetupEntryPoint s’exécute chaque fois que le service est redémarré.

N’est qu’un seul SetupEntrypoint, le programme d’installation/config scripts doivent être regroupés dans un fichier seul lot si le programme d’installation/config de l’application requiert plusieurs scripts. La SetupEntrypoint peut exécuter n’importe quel type de fichier--fichiers exécutables, les fichiers de commandes et les applets de commande PowerShell. Pour plus d’informations, voir [Configurer SetupEntryPoint](service-fabric-application-runas-security.md).

Dans l’exemple précédent, le SetupEntrypoint s’exécute un fichier de commandes appelé `LaunchConfig.cmd` qui se trouve dans le `scripts` sous-répertoire du répertoire code (en supposant que l’élément WorkingFolder est défini sur base de code).

#### <a name="updating-the-entrypoint"></a>Mise à jour du point d’entrée

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

La `Entrypoint` élément dans le fichier manifeste de service est utilisé pour spécifier la manière de démarrer le service. La `ExeHost` élément spécifie le fichier exécutable (et les arguments) qui doivent être utilisés pour démarrer le service.

- `Program`Spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
- `Arguments`Spécifie les arguments doivent être passés à l’exécutable. Il peut être une liste des paramètres avec arguments.
- `WorkingFolder`Spécifie le répertoire de travail pour le processus qui va agir en pour qu’il démarre. Vous pouvez spécifier trois valeurs :
    - `CodeBase`Spécifie que le répertoire de travail va être définie à l’annuaire de code dans le package d’application (`Code` répertoire dans la structure de fichier précédent).
    - `CodePackage`Spécifie que le répertoire de travail va être définie à la racine du package d’application (`GuestService1Pkg` dans la structure de fichier précédent).
  - `Work`Spécifie que les fichiers sont placés dans un sous-répertoire appelé travail

La WorkingFolder est utile pour définir le répertoire de travail correct afin que les chemins d’accès relatifs peuvent être utilisées par l’application ou l’initialisation de scripts.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Mise à jour les points de terminaison et l’enregistrement dans Service DNS pour la communication

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Dans l’exemple précédent le `Endpoint` élément spécifie les points de terminaison que l’application peut écouter sur. Dans cet exemple, l’application Node.js écoute http sur le port 3000.

Par ailleurs, vous pouvez demander tissu de Service à publier ce point de terminaison sur le Service de dénomination afin que d’autres services de découvrir l’adresse de point de terminaison pour ce service. Ainsi, vous permettent d’être en mesure de communiquer entre les services qui sont exécutables invité.
L’adresse de point de terminaison publiés est de l’écran `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`et `PathSuffix` sont des attributs facultatifs. `IPAddressOrFQDN`est l’adresse IP ou le nom de domaine complet du nœud ce fichier exécutable est placé sur et est calculé pour vous.

Dans l’exemple suivant une fois que le service est déployé, dans l’Explorateur de tissu Service vous voyez un point de terminaison semblable à `http://10.1.4.92:3000/myapp/` publié pour l’instance de service ou s’il s’agit d’un ordinateur local, vous voyez `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Vous pouvez utiliser ces adresses avec le [proxy inverse](service-fabric-reverseproxy.md) pour communiquer entre les services.

### <a name="edit-the-application-manifest-file"></a>Modifier le fichier manifeste d’application

Une fois que vous avez configuré la `Servicemanifest.xml` fichier, vous devez apporter quelques modifications à la `ApplicationManifest.xml` fichier pour vous assurer que le type de service correcte et le nom sont utilisés.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

Dans la `ServiceManifestImport` élément, vous pouvez spécifier un ou plusieurs services que vous souhaitez inclure dans l’application. Services sont référencées avec `ServiceManifestName`, qui spécifie le nom du répertoire où le `ServiceManifest.xml` fichier se trouve.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurer la journalisation
Des exécutables invité, il est utile être en mesure de voir les fichiers journaux de console à déterminer si les scripts application et configuration affichent toutes les erreurs.
Redirection de la console peut être configuré dans le `ServiceManifest.xml` de fichiers à l’aide de la `ConsoleRedirection` élément.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`peut être utilisée pour rediriger sortie de la console (stdout et stderr) vers un répertoire de travail afin qu’elles peuvent être utilisées pour vérifier qu’il n’y a aucune erreur pendant l’installation ou l’exécution de l’application dans le cluster tissu de Service.

    * `FileRetentionCount`Détermine le nombre de fichiers est enregistré dans le répertoire de travail. Une valeur de 5, par exemple, signifie que les fichiers journaux pour les cinq exécutions précédente sont stockés dans le répertoire de travail.
    * `FileMaxSizeInKb`Spécifie la taille maximale des fichiers journaux.

Les fichiers journaux sont enregistrés dans un des répertoires de travail du service. Pour déterminer où se trouvent les fichiers, vous devez utiliser Service TISSU Explorer pour déterminer le nœud que le service est en cours d’exécution et le répertoire de travail est utilisé. Ce processus est traité plus loin dans cet article.

## <a name="deployment"></a>Déploiement
La dernière étape consiste à déployer votre application. Le script PowerShell suivant montre comment déployer votre application dans le cluster de développement local et démarrer un nouveau service tissu de Service.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un service de tissu de Service peut être déployé dans différentes « configurations ». Par exemple, il peut être déployé en tant qu’un ou plusieurs instances ou elle peut être déployée de telle sorte qu’il existe une instance du service sur chaque nœud du cluster tissu de Service.

La `InstanceCount` paramètre de la `New-ServiceFabricService` applet de commande permet de spécifier le nombre d’instances du service doit être lancé dans le cluster tissu de Service. Vous pouvez définir la `InstanceCount` valeur, selon le type d’application que vous déployez. Les deux scénarios courants sont :

* `InstanceCount = "1"`. Dans ce cas, qu’une seule instance du service est déployée dans le cluster. Planificateur de service TISSU détermine le nœud va être déployé sur le service.

* `InstanceCount ="-1"`. Dans ce cas, une instance du service est déployée sur tous les nœuds du cluster tissu de Service. Le résultat est disposer d’un seul (et unique) instance du service pour chaque nœud du cluster.

Il s’agit d’une configuration utiles pour les applications frontale (par exemple, un point de terminaison reste), car les applications clientes doivent se « connecter » à aucun des nœuds dans le cluster à utiliser le point de terminaison. Cette configuration peut également être utilisée lorsque, par exemple, tous les nœuds du cluster Service TISSU êtes connectés à un équilibrage de charge pour le trafic client peut être distribué sur le service qui s’exécute sur tous les nœuds du cluster.

## <a name="check-your-running-application"></a>Vérifier votre application en cours d’exécution

Dans l’Explorateur de tissu de Service, identifier le nœud où le service est en cours d’exécution. Dans cet exemple, il s’exécute sur le nœud 1 :

![Nœud où le service est en cours d’exécution](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si vous accédez au nœud et accédez à l’application, vous voyez les informations essentielles nœud, y compris son emplacement sur le disque.

![Emplacement sur le disque](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si vous accédez à l’annuaire à l’aide de l’Explorateur de serveur, vous pouvez trouver le répertoire de travail et le dossier du service journal comme le montre l’image suivante.

![Emplacement du journal](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à déployer pour tissu de Service et de package d’un fichier exécutable invité. Étape suivante, vous pouvez consulter du contenu supplémentaire pour cette rubrique.

- [Exemple d’emballage et de déploiement invité exécutable sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), ainsi qu’un lien vers la version préliminaire de l’outil de création d’un package
- [Déployer plusieurs fichiers exécutables invité](service-fabric-deploy-multiple-apps.md)
- [Créer votre première application tissu de Service à l’aide de Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
