<properties
   pageTitle="Déployer une application Node.js qui utilise MongoDB | Microsoft Azure"
   description="Procédure créer un package plusieurs exécutables invité à déployer un cluster tissu de Service Azure"
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
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Déployer plusieurs fichiers exécutables invité

Cet article vous explique comment empaqueter et déployer plusieurs exécutables invité à Azure Service tissu. Pour créer et déployer un seul lot Service TISSU lire comment [déployer un invité exécutable pour tissu de Service](service-fabric-deploy-existing-app.md).

Bien que cette procédure montre comment déployer une application avec un frontal Node.js qui utilise MongoDB comme le magasin de données, vous pouvez appliquer les étapes à n’importe quelle application qui comporte des dépendances sur une autre application.   

Vous pouvez utiliser Visual Studio pour générer le package d’application qui contient plusieurs fichiers exécutables invité. Reportez-vous [à l’aide de Visual Studio pour créer un package une application existante](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Une fois que vous avez ajouté le premier exécutable invité, clic droit sur le projet d’application et sélectionnez **Add -> Nouveau tissu de Service service** pour ajouter le second projet exécutable invité à la solution. Remarque : Si vous choisissez associer la source dans le projet Visual Studio, création d’une solution Visual Studio, seront Assurez-vous que votre package d’application est à jour avec les modifications dans la source. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empaqueter manuellement l’application exécutable invité plusieurs
Vous pouvez également vous pouvez empaqueter manuellement l’invité exécutable. Pour l’emballage manuel, cet article utilise l’outil d’emballage tissu de Service, qui est disponible dans [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Compression de l’application Node.js
Cet article part du principe que Node.js n’est pas installé sur les nœuds du cluster tissu de Service. Par conséquent, vous devez ajouter Node.exe à la racine de votre application nœud avant d’emballage. La structure de l’annuaire de l’application Node.js (à l’aide d’infrastructure web Express et modèle Jade moteur) doit ressembler à celui qui suit :

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Étape suivante, vous créez un package d’application pour l’application Node.js. Le code suivant crée un package d’application Service TISSU qui contient l’application Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Vous trouverez ci-dessous une description des paramètres qui sont utilisées :

- **/source** pointe vers l’annuaire de l’application qui doit être empaquetée.
- **/target** définit le répertoire dans lequel le package doit être créé. Ce répertoire doit être différent du répertoire source.
- **AppName** définit le nom de l’application de l’application existante. Il est important de comprendre que cela se traduit par le nom de service dans le manifeste et non vers le nom de l’application tissu de Service.
- **/exe** définit l’exécutable tissu de Service est censée barre de lancement, dans ce cas `node.exe`.
- **/Ma** définit l’argument est utilisé pour lancer l’exécutable. Comme Node.js n’est pas installé, TISSU Service a besoin lancer le serveur web Node.js en exécutant `node.exe bin/www`.  `/ma:'bin/www'`Indique à l’outil de création d’un package à utiliser `bin/ma` comme argument pour node.exe.
- **/AppType** définit le nom du type application tissu de Service.

Si vous accédez au répertoire qui a été spécifié dans le paramètre /Target., vous pouvez voir que l’outil a créé un package de Service TISSU entièrement fonctionnel comme indiqué ci-dessous :

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Le ServiceManifest.xml généré contient maintenant une section qui décrit comment le serveur web Node.js doit être lancé, comme le montre l’extrait de code ci-dessous :

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Dans cet exemple, le serveur web Node.js écoute le port 3000, afin que vous devez mettre à jour les informations de point de terminaison dans le fichier ServiceManifest.xml comme illustré ci-dessous.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Compression de l’application MongoDB
À présent que vous avez empaqueté l’application Node.js, vous pouvez alors et MongoDB du package. Comme mentionné précédemment, les étapes qui vous traversent maintenant ne sont pas spécifiques à Node.js et MongoDB. En fait, elles s’appliquent à toutes les applications qui sont destinées à être réunis en une seule application tissu de Service.  

Pour créer un package MongoDB, vous vouliez vérifier que vous créez un package Mongod.exe et Mongo.exe. Les deux fichiers binaires se trouvent dans la `bin` répertoire du répertoire d’installation MongoDB. La structure du répertoire est semblable à celui qui suit.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
TISSU de service doit commencer MongoDB avec une commande semblable à celui ci-dessous, vous devez donc utiliser la `/ma` paramètre lors de la compression MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Les données ne sont pas conservées dans le cas d’une défaillance de nœud si vous placez le répertoire de données MongoDB sur le répertoire local du nœud. Vous devez utiliser le stockage résistant ou mise en œuvre d’un réplica MongoDB définir afin d’éviter la perte de données.  

Dans PowerShell ou l’invite de commandes, nous exécuter l’outil de création d’un package avec les paramètres suivants :

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Afin d’ajouter MongoDB à votre package d’application tissu de Service, vous devez vous assurer que le paramètre de pointe /target dans le même répertoire contenant déjà l’application manifeste ainsi que l’application Node.js. Vous devez également vous assurer que vous utilisez le même nom ApplicationType.

Nous allons, accédez au répertoire et examinez l’outil a créé.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Comme vous pouvez le voir, l’outil ajouté un nouveau dossier, MongoDB, dans le répertoire contenant les fichiers binaires MongoDB. Si vous ouvrez le `ApplicationManifest.xml` fichier, vous pouvez voir que le package contient à présent l’application Node.js et MongoDB. Le code suivant affiche le contenu du manifeste d’application.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publication de l’application
La dernière étape consiste à publier l’application pour le cluster Service TISSU local en utilisant les scripts PowerShell ci-dessous :

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Une fois que l’application est publiée avec succès vers le cluster local, vous pouvez accéder à l’application Node.js sur le port que nous avons entré dans le manifeste de service de l’application Node.js – http://localhost:3000 par exemple.

Dans ce didacticiel, vous avez appris à empaqueter facilement des deux applications existantes comme une application de Service tissu. Vous avez également appris à déployer pour tissu de Service afin qu’il peut bénéficier de certaines des fonctionnalités tissu de Service, telles que la disponibilité et l’intégration d’intégrité du système.

## <a name="next-steps"></a>Étapes suivantes

- Savoir comment déployer des conteneurs avec [vue d’ensemble des conteneurs et tissu de Service](service-fabric-containers-overview.md)
