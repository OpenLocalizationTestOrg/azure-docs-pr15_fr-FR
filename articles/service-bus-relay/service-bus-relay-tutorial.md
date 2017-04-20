<properties 
    pageTitle="Didacticiel Service Bus relais | Microsoft Azure"
    description="Créer un client Bus des services application et le service à l’aide du Service Bus relais."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Didacticiel Service Bus relais

Ce didacticiel décrit comment créer une application cliente de Service Bus simple et le service en utilisant les fonctionnalités de « relais » Bus des services. Pour un didacticiel correspondant qui utilise [traité messagerie](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging)Bus des services, consultez le [Service Bus : demandée un didacticiel de .NET de messagerie](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Travail grâce à ce didacticiel vous donne un comprendre les étapes nécessaires pour créer une application client et le service Bus des services. Comme leurs homologues WCF, un service est une construction qui expose un ou plusieurs points de terminaison, chacun d'entre eux expose une ou plusieurs opérations de service. Le point de terminaison d’un service indique l’adresse où se trouve le service, une liaison qui contient les informations qu’un client doit communiquer avec le service et d’un contrat qui définit les fonctionnalités fournies par le service à ses clients. La différence entre un service WCF et un service Service Bus principale est que le point de terminaison est exposé dans le cloud à la place de localement sur votre ordinateur.

Une fois que vous parcourez la séquence de rubriques dans ce didacticiel, vous aurez accès à un service en cours d’exécution et un client qui peut appeler les opérations du service. La première rubrique explique comment configurer un compte. Étapes suivantes décrivent comment définir un service qui utilise un contrat implémenter le service et comment configurer le service dans le code. Elles décrivent également comment héberger et exécuter le service. Le service est créé est autonome et le client et le service exécutent sur le même ordinateur. Vous pouvez configurer le service à l’aide de code ou un fichier de configuration.

Les trois étapes finales expliquent comment créer une application cliente, configurez l’application cliente et créer et utilisent un client qui peut accéder à la fonctionnalité de l’hôte.

Toutes les rubriques dans cette section part du principe que vous utilisez Visual Studio comme environnement de développement.

## <a name="sign-up-for-an-account"></a>S’inscrire à un compte

La première étape consiste à créer un espace de noms et pour obtenir une clé de Signature accès partagé (sa). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais Bus des services. Une clé de sa est générée automatiquement par le système lors de la création d’un espace de noms de service. La combinaison d’espace de noms de service et sa clé fournit les informations d’identification Bus des services authentifier l’accès à l’application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Définir un contrat de service WCF à utiliser à Service

Le contrat de service spécifie les opérations que le service (la terminologie du service Web pour méthodes ou les fonctions) prend en charge. Contrats sont créés en définissant une interface C++, c# ou Visual Basic. Chaque méthode dans l’interface correspond à une opération de service spécifique. Chaque interface doit avoir l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) qui lui est appliqué, et chaque opération doit avoir l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) qui lui est appliqué. Si une méthode dans une interface qui comporte l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) n’a pas l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , cette méthode n’est pas accessible. Le code pour ces tâches est fourni dans l’exemple suivant la procédure. Pour une description plus grande des contrats et des services, consultez [conception et implémentation des Services](https://msdn.microsoft.com/library/ms729746.aspx) dans la documentation WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Pour créer un contrat de Service Bus avec une interface

1. Ouvrir Visual Studio en tant qu’administrateur par clic droit sur le programme dans le menu **Démarrer** et en sélectionnant **Exécuter en tant qu’administrateur**.

2. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et sélectionnez **Nouveau**, puis cliquez sur **le projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual c#** (si **Visual c#** n’apparaît pas, regardez sous **Autres langages**). Cliquez sur le modèle **d’Application Console** et nommez-la **EchoService**. Cliquez sur **OK** pour créer le projet.

    ![][2]

3. Installez le package NuGet Bus de Service. Ce package ajoute automatiquement des références pour les bibliothèques Bus des services, ainsi que la WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms qui vous permet d’accéder par programme les fonctionnalités de base de WCF. Bus des services utilise la plupart des objets et les attributs de WCF pour définir des contrats de service.

    Dans l’Explorateur de solutions, avec le bouton droit de la solution, puis cliquez sur **Gérer les Packages NuGet pour la Solution**. Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Vérifiez que le nom du projet est sélectionné dans la zone **ou les versions** . Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![][3]

3. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs pour l’ouvrir dans l’éditeur, s’il n’est pas déjà ouvert.

4. Ajoutez les éléments suivants à l’aide d’instructions dans la partie supérieure du fichier :

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Modifier l’espace de noms à partir de son nom par défaut de **EchoService** à **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Ce didacticiel utilise l’espace de nom c# **Microsoft.ServiceBus.Samples**, ce qui correspond à l’espace de noms du type contrat géré qui est utilisé dans le fichier de configuration dans l’étape [configurer le client WCF](#configure-the-wcf-client) . Vous pouvez spécifier n’importe quel espace de noms lorsque vous créez cet exemple ; Toutefois, le didacticiel ne fonctionnera pas à moins que vous puis modifiez les espaces de noms du contrat de service en conséquence, dans le fichier de configuration d’application. L’espace de noms spécifié dans le fichier App.config doit être identique à l’espace de noms spécifié dans vos fichiers c#.

1. Juste après le `Microsoft.ServiceBus.Samples` déclaration d’espace de noms, mais l’espace de noms, définir une nouvelle interface nommée `IEchoContract` et appliquer le `ServiceContractAttribute` attribut à l’interface avec une valeur de l’espace de noms de **http://samples.microsoft.com/ServiceModel/Relay/**. La valeur de l’espace de noms est différent de l’espace de noms que vous utilisez dans l’ensemble de l’étendue de votre code. À la place, la valeur de l’espace de noms est utilisée comme un identificateur unique pour ce contrat. Spécification explicite de l’espace de noms empêche l’ajout le nom de contrat la valeur d’espace de noms par défaut.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] En règle générale, l’espace de noms de contrat de service contient un modèle de nom qui inclut des informations de version. Y compris les informations de version dans l’espace de noms de contrat de service Active les services à isoler les principales modifications en définissant un contrat de service avec un nouvel espace de noms et en exposant sur un point de terminaison. De cette façon, les clients peuvent continuer à utiliser l’ancien contrat de service sans avoir à mettre à jour. Version peut s’agir d’une date ou un numéro de version. Pour plus d’informations, voir [Le Service de contrôle de version](http://go.microsoft.com/fwlink/?LinkID=180498). Aux fins de ce didacticiel, le modèle d’attribution de nom de l’espace de noms de contrat de service ne contient pas les informations de version.

1. Dans la `IEchoContract` interface, déclarer une méthode pour l’opération unique le `IEchoContract` contrat expose dans l’interface et appliquer le `OperationContractAttribute` attribuer à la méthode que vous souhaitez exposer dans le cadre du contrat de Service Bus public.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Juste après le `IEchoContract` définition d’interface, déclarer un canal hérite les deux `IEchoContract` et également à la `IClientChannel` de l’interface, comme illustré ici :

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canal est l’objet WCF via lequel l’hôte et le client de passent des informations entre eux. Plus tard, vous allez écrire code contre le canal pour renvoyer des informations entre les deux applications.

1. Dans le menu **Générer** , cliquez sur **Générer la Solution** ou appuyez sur **Ctrl + Maj + B** pour confirmer l’exactitude de votre travail jusqu'à présent.

### <a name="example"></a>Exemple

Le code suivant montre une interface de base qui définit un contrat de Service Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Maintenant que l’interface est créée, vous pouvez implémenter l’interface.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Mettre en œuvre le contrat WCF à utiliser Bus des services

La création d’un relais Bus des services nécessite que vous créez tout d’abord le contrat, qui est défini en utilisant une interface. Pour plus d’informations sur la création de l’interface, voir l’étape précédente. L’étape suivante consiste pour implémenter l’interface. Cela implique la création d’une classe nommée `EchoService` qui mettent en œuvre, défini par l’utilisateur `IEchoContract` interface. Après l’implémentation de l’interface, vous configurez puis l’interface à l’aide d’un fichier de configuration App.config. Le fichier de configuration contient les informations nécessaires pour l’application, telles que le nom du service et le nom de contrat, ainsi que le type de protocole est utilisé pour communiquer à Service. Le code utilisé pour ces tâches est fourni dans l’exemple suivant la procédure. Pour une description plus générale sur la mise en œuvre d’un contrat de service, voir [Mise en œuvre des contrats de Service](https://msdn.microsoft.com/library/ms733764.aspx) dans la documentation WCF.

1. Créer une nouvelle classe nommée `EchoService` juste après la définition de la `IEchoContract` interface. La `EchoService` classe met en œuvre la `IEchoContract` interface. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Similaires aux autres mises en œuvre interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, pour ce didacticiel, la mise en œuvre se trouve dans le même fichier en tant que la définition d’interface et la `Main` méthode.

1. Appliquer l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à la `IEchoContract` interface. L’attribut spécifie le nom du service et espace de noms. Après cela, le `EchoService` classe se présente comme suit :

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Mettre en œuvre la `Echo` méthode définie dans le `IEchoContract` interface dans le `EchoService` cours. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Cliquez sur **Générer**, puis cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail.

### <a name="to-define-the-configuration-for-the-service-host"></a>Pour définir la configuration pour l’hôte de service

1. Le fichier de configuration est très similaire à un fichier de configuration WCF. Il inclut le nom du service et la liaison de point de terminaison (autrement dit, l’emplacement expose Bus des services pour les clients et hôtes communiquer avec eux) (le type de protocole qui est utilisé pour communiquer). La différence principale est que ce point de terminaison du service configuré fait référence à une liaison [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) , qui ne fait pas partie du .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) figure parmi les liaisons définies par Bus des services.

1. Dans l' **Explorateur de solutions**, double-cliquez sur le fichier App.config pour l’ouvrir dans l’éditeur Visual Studio.

2. Dans la `<appSettings>` élément, remplacer les espaces réservés par le nom de votre espace de noms de service et les associations de sécurité de clé que vous avez copié dans une étape précédente. 

1. Dans la `<system.serviceModel>` balises, ajouter un `<services>` élément. Vous pouvez définir plusieurs applications du Service Bus dans un seul fichier de configuration. Toutefois, ce didacticiel ne définit qu’un seul.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dans la `<services>` élément, ajoutez un `<service>` élément pour définir le nom du service.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dans la `<service>` élément, définir l’emplacement du contrat de point de terminaison, ainsi que le type de liaison pour le point de terminaison.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Le point de terminaison définit dans laquelle le client de recherche pour l’application hôte. Une version ultérieure, le didacticiel utilise cette étape pour créer un URI qui expose entièrement l’hôte via Bus des services. La liaison déclare que nous utilisons TCP comme protocole communiquer à Service.

1. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail.

### <a name="example"></a>Exemple

Le code suivant illustre l’implémentation du contrat de service.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Le code suivant montre le format de base du fichier App.config associé à l’hôte de service.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Héberger et exécuter un service Web de base pour inscrire à Service

Cette étape décrit l’exécution d’un service de Bus des services de base.

### <a name="to-create-the-service-bus-credentials"></a>Pour créer les informations d’identification Bus des services

1. Dans `Main()`, créez deux variables dans lesquelles vous pouvez stocker l’espace de noms et les associations de sécurité clés qui sont lues à partir de la fenêtre de la console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    La clé de sa doit être utilisée ultérieurement pour accéder à votre projet Bus des services. L’espace de noms est passé comme un paramètre pour `CreateServiceUri` pour créer un service URI.

4. Utilisation d’un objet [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , déclarer que vous utiliserez une clé associations de sécurité en tant que le type d’informations d’identification. Ajoutez le code suivant juste après le code ajouté à la dernière étape.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Pour créer une adresse de base pour le service

1. Après le code que vous avez ajouté à la dernière étape, créez un `Uri` instance pour l’adresse de base du service. Cet URI Spécifie le jeu de Bus des services, l’espace de noms et le chemin d’accès de l’interface de service.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    « petite » est l’abréviation du jeu de Bus des services et indique que nous utilisons TCP comme protocole. Cela a été également précédemment indiqué dans le fichier de configuration lors de la [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) a été spécifié en tant que la liaison.
    
    Pour ce didacticiel, l’URI est `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Pour créer et configurer l’hôte de service

1. Définir le mode de connectivité à `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Le mode de connectivité décrit le protocole utilisé par le service pour communiquer à Service ; HTTP ou TCP. À l’aide de la valeur par défaut `AutoDetect`, le service tente de se connecter au Service Bus sur TCP si elle est disponible et HTTP si TCP n’est pas disponible. Notez que cela est différent du protocole le service spécifie pour les communications client. Ce protocole est déterminé par la liaison utilisée. Par exemple, un service peut utiliser la liaison [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) , qui indique que son point de terminaison (exposée sur Bus Service) communique avec les clients sur HTTP. Ce service même pu spécifier **ConnectivityMode.AutoDetect** afin que le service communique à Service sur TCP.

1. Créer l’hôte de service, à l’aide de l’URI créé précédemment dans cette section.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    L’hôte de service est l’objet WCF qui instancie le service. Ici, vous le passer le type de service que vous souhaitez créer (un `EchoService` type) et également à l’adresse à laquelle vous voulez exposer le service.

1. En haut du fichier Program.cs, ajoutez des références à [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) et [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Dans `Main()`, configurez le point de terminaison pour activer l’accès public.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Cette étape vous informe Bus des services que votre application se trouve publiquement en examinant la ATOM Bus Service flux pour votre projet. Si vous définissez **DiscoveryType** en **privé**, un client serait toujours en mesure d’accéder au service. Toutefois, le service apparaît pas lors de la recherche l’espace de noms Bus des services. En revanche, le client devra connaître le chemin d’accès de point de terminaison au préalable.

1. Appliquer les informations d’identification de service pour les points de terminaison de service définis dans le fichier App.config :

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Comme indiqué dans l’étape précédente, vous avez pouvez déclaré plusieurs services et points de terminaison dans le fichier de configuration. Si vous aviez, ce code traversent le fichier de configuration et la recherche pour chaque point de terminaison auquel il doit être appliqué vos informations d’identification. Toutefois, pour ce didacticiel, le fichier de configuration a uniquement un point de terminaison.

### <a name="to-open-the-service-host"></a>Pour ouvrir l’hôte de service

1. Ouvrez le service.

    ```
    host.Open();
    ```

1. Informer l’utilisateur que le service est en cours d’exécution et expliquent comment arrêter le service.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Lorsque vous avez terminé, fermez l’hôte de service.

    ```
    host.Close();
    ```

1. Appuyez sur **Ctrl + Maj + B** pour générer le projet.

### <a name="example"></a>Exemple

L’exemple suivant inclut le contrat de service et la mise en œuvre des étapes précédentes dans le didacticiel et héberge le service dans une application console. Compiler les éléments suivants dans un fichier exécutable nommé EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Création d’un client WCF du contrat de service

L’étape suivante consiste à créer une application cliente Bus des services base et définir le contrat de service que vous allez implémenter dans les étapes suivantes. Notez que la plupart de ces étapes ressemblent aux étapes à suivre pour créer un service : définition d’un contrat, un App.config la modification de fichiers, à l’aide des informations d’identification pour vous connecter au Service Bus et ainsi de suite. Le code utilisé pour ces tâches est fourni dans l’exemple suivant la procédure.

1. Créer un projet dans la solution Visual Studio en cours pour le client en procédant comme suit :
    1. Dans l’Explorateur, dans la même solution qui contient le service, avec le bouton droit de la solution en cours (et non sur le projet), puis cliquez sur **Ajouter**. Cliquez sur **Nouveau projet**.
    2. Dans la boîte de dialogue **Ajouter un nouveau projet** , cliquez sur **Visual c#** (si **Visual c#** n’apparaît pas, regardez sous **Autres langages**), sélectionnez le modèle **d’Application Console** et nommez-la **EchoClient**.
    3. Cliquez sur **OK**.
<br />

1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs dans le projet **EchoClient** pour l’ouvrir dans l’éditeur, s’il n’est pas déjà ouvert.

1. Modifier l’espace de noms à partir de son nom par défaut de `EchoClient` à `Microsoft.ServiceBus.Samples`.

1. Installez le [package NuGet Bus de Service](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Dans l’Explorateur, droit sur le projet **EchoClient** , puis cliquez sur **Gérer les Packages NuGet**. Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![][3]

1. Ajouter un `using` déclaration de l’espace de noms [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) dans le fichier Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Ajoutez la définition du contrat de service à l’espace de noms, comme illustré dans l’exemple suivant. Notez que cette définition est identique à la définition utilisée dans le projet de **Service** . Vous devez ajouter ce code en haut de la `Microsoft.ServiceBus.Samples` espace de noms.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Appuyez sur **Ctrl + Maj + B** pour générer le client.

### <a name="example"></a>Exemple

Le code suivant affiche l’état actuel du fichier Program.cs dans le projet EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurer le client WCF

Dans cette étape, vous créez un fichier App.config pour une application cliente simple qui accède au service créé précédemment dans ce didacticiel. Ce fichier App.config définit le contrat, la liaison et le nom du point de terminaison. Le code utilisé pour ces tâches est fourni dans l’exemple suivant la procédure.

1. Dans l’Explorateur de solutions, dans le projet **EchoClient** , double-cliquez sur **App.config** pour ouvrir le fichier dans l’éditeur Visual Studio.

2. Dans la `<appSettings>` élément, remplacer les espaces réservés par le nom de votre espace de noms de service et les associations de sécurité de clé que vous avez copié dans une étape précédente.

1. Dans l’élément system.serviceModel, ajoutez un `<client>` élément.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Cette procédure permet de déclarer que vous définissez une application cliente WCF style.

1. Dans la `client` élément, définissez le nom, le contrat et le type de liaison pour le point de terminaison.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Cette étape définit le nom du point de terminaison, le contrat défini dans le service et le fait que l’application cliente utilise TCP pour communiquer à Service. Le nom du point de terminaison est utilisé dans l’étape suivante pour lier cette configuration de point de terminaison avec l’URI de service.

1. Cliquez sur **fichier**, puis sur **Enregistrer tout**.

## <a name="example"></a>Exemple

Le code suivant montre le fichier App.config pour le client de l’écho.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implémenter le client WCF pour appeler Bus des services

Dans cette étape, vous implémentez une application cliente simple qui accède au service que vous avez créé précédemment dans ce didacticiel. Semblable au service, le client effectue de nombreuses opérations mêmes pour accéder aux Bus des services :

1. Définit le mode de connexion.
1. Crée l’URI qui localise le service hôte.
1. Définit les informations d’identification de sécurité.
1. Applique les informations d’identification pour la connexion.
1. Ouvre la connexion.
1. Effectue les tâches spécifiques à l’application.
1. Ferme la connexion.

Toutefois, un des principales différences est que l’application cliente utilise un canal pour vous connecter au Service Bus, alors que le service utilise un appel à **ServiceHost**. Le code utilisé pour ces tâches est fourni dans l’exemple suivant la procédure.

### <a name="to-implement-a-client-application"></a>Pour mettre en œuvre une application cliente

1. Définir le mode de connectivité à **détecter automatiquement**. Ajoutez le code suivant à l’intérieur de la `Main()` méthode de l’application **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Définir des variables pour contenir les valeurs pour l’espace de noms de service et sa clé lus à partir de la console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Créer l’URI qui définit l’emplacement de l’hôte dans votre projet Bus des services.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Créer l’objet d’informations d’identification pour votre point de terminaison de service espace de noms.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Créer la fabrication de canal qui charge la configuration décrite dans le fichier App.config.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Une usine de canaux est un objet WCF qui crée un canal via lequel communiquent les applications clientes et de service.

1. Appliquer les informations d’identification Bus des services.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Créer et ouvrir le canal au service.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Écrire l’interface utilisateur de base et les fonctionnalités pour que l’écho.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Notez que le code utilise l’instance de l’objet de canal en tant que proxy pour le service.

1. Fermer le canal, puis fermez l’usine.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Pour exécuter les applications

1. Appuyez sur **Ctrl + Maj + B** pour générer la solution. Cela crée le projet client et le projet de service que vous avez créé lors des étapes précédentes.

2. Avant d’exécuter l’application cliente, vous devez vous assurer que l’application de service est en cours d’exécution. Dans l’Explorateur de solutions dans Visual Studio, avec le bouton droit de la solution **EchoService** , puis cliquez sur **Propriétés**.

3. Dans la boîte de dialogue Propriétés de la solution, cliquez sur **Projet de démarrage**, puis cliquez sur le bouton de **plusieurs projets de démarrage** . Vérifiez que **EchoService** apparaît en premier dans la liste. 

4. Définir la zone **Action** pour les **EchoService** **EchoClient** projets et pour **Démarrer**.

    ![][5]

5. Cliquez sur **dépendances du projet**. Dans la zone **projets** , sélectionnez **EchoClient**. Dans la zone **dépend** , assurez-vous que **EchoService** est cochée.

    ![][6]

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés** .

7. Appuyez sur **F5** pour exécuter les deux projets.

8. Les deux fenêtres console ouvrent et invite à entrer le nom de l’espace de noms. Le service doit exécutées en premier, alors entrez l’espace de noms dans la fenêtre de console **EchoService** et appuyez sur **entrée**.

9. Ensuite, vous êtes invité à votre clé associations de sécurité. Entrez la clé associations de sécurité, puis appuyez sur ENTRÉE.

    Voici un exemple de sortie à partir de la fenêtre de la console. Notez que les valeurs fournies uniquement à des fins Voici par exemple.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    L’application de service imprime dans la fenêtre console l’adresse sur lequel il est à l’écoute, comme illustré dans l’exemple suivant.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Dans la fenêtre de la console **EchoClient** , entrez les mêmes informations que vous avez entrées précédemment pour l’application de service. Suivez les étapes précédentes pour entrer le même espace de noms de service et les valeurs clés associations de sécurité pour l’application cliente.

11. Après avoir entré les valeurs suivantes, le client ouvre un canal au service et vous invite à entrer du texte, comme illustré dans l’exemple de sortie console suivant.

    `Enter text to echo (or [Enter] to exit):` 

    Entrer du texte pour envoyer à l’application de service et appuyez sur ENTRÉE. Ce texte est envoyé au service via l’opération de service écho et apparaît dans la fenêtre de la console service comme dans l’exemple de sortie suivant.

    `Echoing: My sample text`

    L’application cliente reçoit la valeur de retour de la `Echo` opération, ce qui correspond au texte d’origine et imprime à sa fenêtre console. Voici un exemple de sortie à partir de la fenêtre de la console client.

    `Server echoed: My sample text`

12. Vous pouvez continuer à envoyer des messages texte à partir du client au service de cette manière. Lorsque vous avez terminé, appuyez sur entrée dans les fenêtres de console client et le service pour terminer les deux applications.

## <a name="example"></a>Exemple

L’exemple suivant montre comment créer une application cliente, comment appeler les opérations du service et fermer le client une fois que vous avez terminé l’appel de l’opération.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel montré comment créer un client Bus des services application et le service en utilisant les fonctionnalités de « relais » Bus des services. Pour obtenir un didacticiel similaire qui utilise Bus des services [messagerie](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consultez le [Service Bus : demandée un didacticiel de .NET de messagerie](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Pour en savoir plus sur Bus des services, consultez les rubriques suivantes.

- [Vue d’ensemble de la messagerie Bus des services](../service-bus-messaging/service-bus-messaging-overview.md)
- [Principes de base de Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Bus de service](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
