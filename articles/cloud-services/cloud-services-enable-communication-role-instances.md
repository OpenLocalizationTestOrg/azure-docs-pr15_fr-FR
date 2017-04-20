<properties 
pageTitle="Communication pour les rôles dans les Services en nuage | Microsoft Azure" 
description="Instances de rôles dans les Services en nuage peuvent contenir des points de terminaison (http, https, tcp et udp) définis pour eux de communiquer avec l’extérieur ou entre autres instances de rôle." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Activer la communication instances de rôle dans azure

Rôles de service cloud communiquent par le biais des connexions internes et externes. Connexions externes sont appelées **extrémités d’entrée** tandis que les connexions internes sont appelées des **points de terminaison internes**. Cette rubrique explique comment modifier la [définition du service](cloud-services-model-and-package.md#csdef) pour créer des points de terminaison.


## <a name="input-endpoint"></a>Point de terminaison d’entrée
Le point de terminaison d’entrée est utilisé lorsque vous souhaitez exposer un port vers l’extérieur. Vous spécifiez le type de protocole et le port du point de terminaison qui s’applique ensuite pour les ports internes et externes pour le point de terminaison. Si vous le souhaitez, vous pouvez spécifier un autre port interne du point de terminaison avec l’attribut [Port_local](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

Le point de terminaison d’entrée peut utiliser les protocoles suivants : **http, https, tcp et udp**.

Pour créer un point de terminaison d’entrée, ajoutez l’élément enfant **InputEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un collaborateur.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Point de terminaison instance d’entrée
Points de terminaison d’entrée instance sont similaires aux entrées points de terminaison, mais vous permet de mapper les ports not spécifiques pour chaque instance de rôle individuel en utilisant la redirection de port sur l’équilibrage de charge. Vous pouvez spécifier un seul port accessible au public, ou une plage de ports.

Le point de terminaison d’entrée instance ne pouvez utiliser **tcp** ou **udp** comme protocole.

Pour créer un point de terminaison instance d’entrée, ajoutez l’élément enfant **InstanceInputEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un collaborateur.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Point de terminaison interne
Points de terminaison internes sont disponibles pour la communication d’instance de l’instance. Le port est facultatif et en cas d’omission, un port dynamique est affecté au point de terminaison. Une plage de ports peut être utilisée. Il existe une limite de cinq points de terminaison internes par rôle.

Le point de terminaison interne peut utiliser les protocoles suivants : **http, tcp et udp, n’importe quel**.

Pour créer un point de terminaison d’entrée interne, ajoutez l’élément enfant **InternalEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un collaborateur.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Vous pouvez également utiliser une plage de ports.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Rôles de travail et rôles Web

Il existe une différence mineure avec points de terminaison lorsque vous travaillez avec collaborateur et rôles web. Le rôle web doit avoir au moins un point de terminaison d’entrée unique à l’aide du protocole **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>À l’aide du Kit de développement .NET pour accéder à un point de terminaison
La bibliothèque gérées Azure fournit des méthodes instances de rôle de communiquer en cours d’exécution. À partir de code en cours d’exécution au sein d’une instance de rôle, vous pouvez récupérer des informations sur l’existence d’autres instances de rôles et leurs points de terminaison, ainsi que les informations concernant l’instance actuelle de rôle.

> [AZURE.NOTE] Vous ne pouvez récupérer des informations sur les instances de rôle qui s’exécutent dans votre service cloud et qui définissent au moins un point de terminaison interne. Vous ne pouvez pas obtenir des données à propos des instances de rôle en cours d’exécution dans un autre service.

Vous pouvez utiliser la propriété [Instances](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) pour récupérer des instances d’un rôle. Utilisez d’abord la [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) pour renvoyer une référence à l’instance actuelle de rôle, puis utilisez la propriété [rôle](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) pour renvoyer une référence au rôle lui-même.

Lorsque vous vous connectez à une instance de rôle par programme via le Kit de développement .NET, il est relativement facile à accéder aux informations du point de terminaison. Par exemple, une fois que vous avez déjà connecté à un environnement de rôle spécifique, vous pouvez obtenir le port d’un point de terminaison spécifique avec ce code :

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

La propriété **Instances** renvoie une collection d’objets **RoleInstance** . Cette collection contient toujours l’instance actuelle. Si le rôle ne définit un point de terminaison interne, la collection inclut l’instance actuelle mais aucune autre instance. Le nombre d’instances de rôle dans la collection sera toujours 1 dans le cas où aucun point de terminaison interne n’est défini pour le rôle. Si le rôle définit un point de terminaison interne, ses instances sont détectable pour la recherche en cours d’exécution et le nombre d’instances de la collection correspondra au nombre d’instances spécifié pour le rôle dans le fichier de configuration de service.

> [AZURE.NOTE] La bibliothèque gérées Azure ne fournit pas un moyen de déterminer l’état des autres instances de rôle, mais vous pouvez appliquer ces évaluations d’intégrité vous-même si votre service a besoin de cette fonctionnalité. Vous pouvez utiliser [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) pour obtenir des informations sur l’exécution d’instances de rôle.

Pour déterminer le numéro de port pour un point de terminaison interne sur une instance de rôle, vous pouvez utiliser la propriété [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) pour renvoyer un objet de dictionnaire qui contient les noms de point de terminaison et leurs ports et les adresses IP correspondantes. La propriété [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) renvoie l’adresse IP et le port pour un point de terminaison spécifié. La propriété **PublicIPEndpoint** renvoie le port d’un point de terminaison équilibrée de charge. La partie adresse IP de la propriété **PublicIPEndpoint** n’est pas utilisée.

Voici un exemple qui produit une itération instances de rôle.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Voici un exemple d’un rôle de collaborateur qui obtient le point de terminaison exposée via la définition du service et commence à écouter les connexions.

> [AZURE.WARNING] Ce code fonctionne uniquement pour un service déployé. Lors de l’exécution dans l’émulateur de calcul Azure, les éléments de configuration de service qui créent des points de terminaison de port directe (éléments**InstanceInputEndpoint** ) sont ignorées.

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Règles de trafic réseau pour contrôler la communication entre les rôles
Une fois que vous définissez des points de terminaison internes, vous pouvez ajouter des règles de trafic réseau (basés sur les points de terminaison que vous avez créé) pour contrôler comment les instances de rôles puissent communiquer avec eux. L’illustration ci-dessous présente quelques scénarios courants pour contrôler la communication de rôle :

![Scénarios de règles de trafic réseau] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Scénarios de règles de trafic réseau")

L’exemple suivant montre les définitions de rôles pour les rôles présentés dans l’illustration précédente. Chaque définition de rôle inclut au moins un point de terminaison interne défini :

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Restriction de communication entre les rôles peut se produire avec des points de terminaison internes des deux corrigé et ports assignés automatiquement.

Par défaut, après avoir défini un point de terminaison interne, communication peut être transmis à partir de n’importe quel rôle au point de terminaison interne d’un rôle sans restrictions. Pour limiter la communication, vous devez ajouter un élément **NetworkTrafficRules** à l’élément **ServiceDefinition** dans le fichier de définition de service.

### <a name="scenario-1"></a>Scénario 1
Autoriser uniquement le trafic réseau **WebRole1** vers **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scénario 2
Autorise uniquement le trafic réseau de **WebRole1** **WorkerRole1** et **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scénario 3
Autorise uniquement le trafic réseau de **WebRole1** **WorkerRole1**et **WorkerRole1** à **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scénario 4
Autorise uniquement le trafic réseau de **WebRole1** **WorkerRole1**, **WebRole1** à **WorkerRole2**et **WorkerRole1** à **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Une référence de schéma XML pour les éléments utilisés ci-dessus sont accessibles [ici](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le Service Cloud [modèle](cloud-services-model-and-package.md).