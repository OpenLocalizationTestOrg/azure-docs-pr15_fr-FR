<properties
   pageTitle="Spécification des points de terminaison de service Service TISSU | Microsoft Azure"
   description="Comment faire pour décrire les ressources de point de terminaison dans un manifeste de service, y compris comment configurer des points de terminaison HTTPS"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="specify-resources-in-a-service-manifest"></a>Spécifier des ressources dans un manifeste de service

## <a name="overview"></a>Vue d’ensemble

Le manifeste de service permet de ressources qui sont utilisées par le service pour être déclaré/modifié sans modifier le code compilé. TISSU Service Azure prend en charge la configuration de ressources de point de terminaison du service. Le niveau d’accès aux ressources qui sont spécifiés dans le manifeste de service peut être contrôlée via la SecurityGroup du manifeste d’application. La déclaration de ressources permet ces ressources pour être modifié au moment du déploiement, ce qui signifie que le service n’a pas besoin d’introduire un nouveau mécanisme de configuration. La définition de schéma pour le fichier ServiceManifest.xml est installée avec le Service TISSU SDK et les outils pour *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Points de terminaison

Lorsqu’une ressource de point de terminaison est définie dans le manifeste de service, Service TISSU affecte les ports à partir de la plage de ports application réservés lorsqu’un port n’est pas explicitement spécifié. Par exemple, recherchez-le sur le point de terminaison *ServiceEndpoint1* spécifié dans l’extrait de code manifeste fourni après ce paragraphe. En outre, les services peuvent également demander un port spécifique dans une ressource. Réplicas de service en cours d’exécution sur différents nœuds de cluster peuvent être affectées numéros de port différents, tandis que les copies d’un service en cours d’exécution sur le même nœud partagent le port. Les réplicas service peuvent ensuite utiliser ces ports en fonction des besoins pour la réplication et à l’écoute des demandes du client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Pour en savoir plus sur faisant référence à des points de terminaison dans les paramètres du package config fichier (settings.xml), reportez-vous à [configuration avec état des Services fiables](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemple : spécifiant un point de terminaison HTTP pour votre service

Le manifeste de service suivant définit une ressource de point de terminaison TCP et des ressources de point de terminaison HTTP deux dans la &lt;ressources&gt; élément.

Points de terminaison HTTP sont automatiquement QU'ET le feriez en tissu de Service.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemple : spécifiant un point de terminaison HTTPS pour votre service

Le protocole HTTPS fournit une authentification serveur et est également utilisé pour chiffrer les communications client-serveur. Pour activer HTTPS sur votre service Service tissu, spécifiez le protocole dans la *ressources -> points de terminaison -> point de terminaison* section du manifeste de service, comme indiqué précédemment pour le point de terminaison *ServiceEndpoint3*.

>[AZURE.NOTE] Protocole d’un service ne sont pas modifiables pendant la mise à niveau de l’application sans il constituant un saut de modifie.


Voici un exemple ApplicationManifest dont vous avez besoin pour HTTPS. L’empreinte numérique pour votre certificat doit être fourni. Le EndpointRef est une référence à EndpointResource dans ServiceManifest, pour lequel vous définissez le protocole HTTPS. Vous pouvez ajouter plusieurs EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
