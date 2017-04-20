<properties
   pageTitle="Aide de communication sécurisée services dans Service TISSU | Microsoft Azure"
   description="Vue d’ensemble de la façon de sécuriser une communication pour les services fiables qui s’exécutent dans un cluster Azure Service tissu."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Communication sécurisée de l’aide pour les services dans Azure Service TISSU

Sécurité est un des aspects plus importants de communication. L’infrastructure d’application Services fiables fournit plusieurs outils que vous pouvez utiliser pour améliorer la sécurité et piles de communication prédéfinis. Cet article parler de comment faire pour améliorer la sécurité lorsque vous utilisez accès distant service et la pile de communication Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Sécuriser un service lorsque vous utilisez accès distant service

Nous utiliserons existant [exemple](service-fabric-reliable-services-communication-remoting.md) qui explique comment configurer la communication à distance pour les services fiables. Pour sécuriser un service lorsque vous utilisez accès distant service, procédez comme suit :

1. Créer une interface `IHelloWorldStateful`, qui définit les méthodes qui seront disponibles pour un appel de procédure distante sur votre service. Votre service utilisera `FabricTransportServiceRemotingListener`, laquelle est déclaré dans le `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` espace de noms. Il s’agit d’un `ICommunicationListener` implémentation qui fournit des fonctionnalités à distance.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Ajouter des paramètres de récepteur et les informations d’identification de sécurité.

    Assurez-vous que le certificat que vous voulez utiliser pour sécuriser vos communications service est installé sur tous les nœuds du cluster. Il existe deux manières que vous pouvez fournir les paramètres de récepteur et informations d’identification de sécurité :

    1. Leur donner directement dans le code de service :

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Leur donner à l’aide d’un [package de configuration](service-fabric-application-model.md):

        Ajouter un `TransportSettings` section dans le fichier settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Dans ce cas, le `CreateServiceReplicaListeners` méthode ressemble à ceci :

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Si vous ajoutez un `TransportSettings` section dans le fichier settings.xml sans préfixe, `FabricTransportListenerSettings` charge tous les paramètres à partir de cette section par défaut.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Dans ce cas, le `CreateServiceReplicaListeners` méthode ressemble à ceci :

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Lorsque vous appelez des méthodes sur un service sécurisé à l’aide de la pile d’accès distant, au lieu d’utiliser la `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` cours pour créer un proxy de service, utilisez `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passez `FabricTransportSettings`, qui contient `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le code client est en cours d’exécution dans le cadre d’un service, vous pouvez charger `FabricTransportSettings` à partir du fichier settings.xml. Créez une section TransportSettings qui est semblable au code de service, comme indiqué précédemment. Apportez les modifications suivantes au code client :

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le client ne fonctionne pas dans le cadre d’un service, vous pouvez créer un fichier client_name.settings.xml au même emplacement où se trouve le client_name.exe. Puis créer une section TransportSettings dans ce fichier.

    Semblable au service, si vous ajoutez un `TransportSettings` section sans préfixe dans settings.xml/client_name.settings.xml client, `FabricTransportSettings` charge tous les paramètres à partir de cette section par défaut.

    Dans ce cas, le code précédent est simplifié encore plus loin :  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF

Nous utiliserons existant [exemple](service-fabric-reliable-services-communication-wcf.md) qui explique comment configurer une pile de communication basée sur WCF pour des services fiables. Pour sécuriser un service lorsque vous utilisez une pile de communication basée sur WCF, procédez comme suit :

1. Pour le service, vous devez sécuriser le récepteur de communication WCF (`WcfCommunicationListener`) que vous créez. Pour ce faire, modifiez le `CreateServiceReplicaListeners` méthode.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. Dans le client, le `WcfCommunicationClient` cours qui a été créé dans l' [exemple](service-fabric-reliable-services-communication-wcf.md) précédent restent inchangée. Mais vous devez remplacer la `CreateClientAsync` méthode de `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utiliser `SecureWcfCommunicationClientFactory` pour créer un client de communication WCF (`WcfCommunicationClient`). Utiliser le client pour appeler des méthodes de service.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Étapes suivantes

* [API avec OWIN dans fiables Services Web](service-fabric-reliable-services-communication-webapi.md)
