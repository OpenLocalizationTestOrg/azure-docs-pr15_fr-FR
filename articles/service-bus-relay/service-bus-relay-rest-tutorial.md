<properties
    pageTitle="À l’aide de didacticiels Service Bus reste relais messagerie | Microsoft Azure"
    description="Créer une application hôte relais Bus des services simple qui expose une interface basée sur REST."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Didacticiel Service Bus relais reste

Ce didacticiel décrit comment créer une application hôte Service Bus simple qui expose une interface basée sur REST. RESTE permet à un client web, par exemple un navigateur web, pour accéder à l’API Bus Service via les requêtes HTTP.

Ce didacticiel utilise le reste de Windows Communication Foundation (WCF) modèle de programmation pour créer un service reste sur Bus des services. Pour plus d’informations, voir [Le modèle de programmation reste WCF](https://msdn.microsoft.com/library/bb412169.aspx) et [conception et implémentation des Services](https://msdn.microsoft.com/library/ms729746.aspx) dans la documentation WCF.

## <a name="step-1-create-a-service-namespace"></a>Étape 1 : Créer un espace de noms de service

La première étape consiste à créer un espace de noms et pour obtenir une clé de Signature accès partagé (sa). Un espace de noms fournit une limite d’application pour chaque application exposé par le biais Bus des services. Une clé de sa est générée automatiquement par le système lors de la création d’un espace de noms de service. La combinaison d’espace de noms de service et sa clé fournit les informations d’identification Bus des services authentifier l’accès à l’application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Étape 2 : Définissez un contrat de service WCF basée sur REST à utiliser à Service

Comme avec d’autres services Bus des services, lorsque vous créez un service de style REST, vous devez définir le contrat. Le contrat spécifie les opérations que l’hôte prend en charge. Une opération de service peut être considérée comme une méthode de service web. Contrats sont créés en définissant une interface C++, c# ou Visual Basic. Chaque méthode dans l’interface correspond à une opération de service spécifique. L’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) doit être appliqué à chaque interface et l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) doit être appliqué à chaque opération. Si une méthode dans une interface qui possède la [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) n’a pas l' [attribut OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), cette méthode n’est pas accessible. Le code utilisé pour ces tâches est indiqué dans l’exemple suivant la procédure.

La principale différence entre un contrat de Service Bus simple et d’un contrat style REST est l’ajout d’une propriété pour l' [attribut OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Cette propriété permet de mapper une méthode dans l’interface à une méthode de l’autre côté de l’interface. Dans ce cas, nous allons utiliser [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) à lier une méthode HTTP GET. Cela permet Bus de Service de précisément comment récupérer et interpréter les commandes envoyées à l’interface.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Pour créer un contrat de Service Bus avec une interface

1. Ouvrir Visual Studio en tant qu’administrateur : cliquez sur le programme dans le menu **Démarrer** , puis cliquez sur **Exécuter en tant qu’administrateur**.

2. Créer un nouveau projet d’application console. Cliquez sur le menu **fichier** et sélectionnez **Nouveau**, puis sélectionnez **projet**. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **Visual c#**, sélectionnez le modèle **d’Application Console** et nommez-la **ImageListener**. Utilisez **l’emplacement**par défaut. Cliquez sur **OK** pour créer le projet.

3. Pour un projet c#, Visual Studio crée un `Program.cs` fichier. Ce cours contient vide `Main()` méthode, requis pour un projet d’application console générer correctement.

4. Ajoutez des références au Service Bus et **System.ServiceModel.dll** au projet en installant le package NuGet Bus de Service. Ce package ajoute automatiquement des références pour les bibliothèques Bus des services, ainsi que la WCF **System.ServiceModel**. Dans l’Explorateur, droit sur le projet **ImageListener** , puis cliquez sur **Gérer les Packages NuGet**. Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

5. Vous devez ajouter explicitement une référence à **System.ServiceModel.Web.dll** au projet :

    un. Dans l’Explorateur de solutions, cliquez sur le dossier **références** sous le dossier du projet, puis sur **Ajouter une référence**.

    b. Dans la boîte de dialogue **Ajouter une référence** , cliquez sur l’onglet **Framework** sur le côté gauche et dans la zone **Rechercher** , tapez **System.ServiceModel.Web**. Activez la case à cocher **System.ServiceModel.Web** , puis cliquez sur **OK**.

6. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms qui permet d’accéder par programme aux fonctionnalités de base de WCF. Bus des services utilise la plupart des objets et les attributs de WCF pour définir des contrats de service. Vous allez utiliser cet espace de noms dans la plupart de vos applications de relais Bus des services. De même, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) permet de définir le canal, ce qui correspond à l’objet par le biais duquel vous communiquez avec Bus des services et le navigateur web client. Enfin, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contient les types qui vous permettent de créer des applications web.

7. Renommer le `ImageListener` **Microsoft.ServiceBus.Samples**de l’espace de noms.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Directement après l’accolade ouvrante de la déclaration d’espace de noms, définir une nouvelle interface nommée **IImageContract** et appliquer l’attribut **ServiceContractAttribute** à l’interface avec une valeur de `http://samples.microsoft.com/ServiceModel/Relay/`. La valeur de l’espace de noms est différent de l’espace de noms que vous utilisez dans l’ensemble de l’étendue de votre code. La valeur de l’espace de noms est utilisée comme un identificateur unique pour ce contrat et doit contenir des informations de version. Pour plus d’informations, voir [Le Service de contrôle de version](http://go.microsoft.com/fwlink/?LinkID=180498). Spécification explicite de l’espace de noms empêche l’ajout le nom de contrat la valeur d’espace de noms par défaut.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dans la `IImageContract` interface, déclarer une méthode pour l’opération unique le `IImageContract` contrat expose dans l’interface et appliquer le `OperationContractAttribute` attribuer à la méthode que vous souhaitez exposer dans le cadre du contrat de Service Bus public.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Dans l’attribut **OperationContract** , ajoutez la valeur **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Ceci permet de Bus des services pour acheminer les requêtes HTTP GET à `GetImage`et pour convertir les valeurs renvoyées par `GetImage` dans une réponse HTTP GETRESPONSE. Plus loin dans le didacticiel, vous allez utiliser un navigateur web pour accéder à cette méthode et pour afficher l’image dans le navigateur.

11. Juste après le `IImageContract` définition, déclarer un canal hérite à la fois le `IImageContract` et `IClientChannel` interfaces.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Un canal est l’objet WCF via lequel le service et le client passent des informations entre eux. Plus tard, vous allez créer le canal dans votre application hôte. Service Bus utilise ensuite ce canal pour transmettre les requêtes HTTP GET à partir du navigateur à votre implémentation **GetImage** . Bus des services utilise également le canal pour prendre la valeur de retour **GetImage** et traduire en un GETRESPONSE HTTP du navigateur client.

12. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour vérifier l’exactitude de votre travail jusqu'à présent.

### <a name="example"></a>Exemple

Le code suivant montre une interface de base qui définit un contrat de Service Bus.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Étape 3 : Mise en œuvre d’un contrat de service WCF basée sur REST utiliser Bus des services

Création d’un service de style REST Service Bus nécessite que vous créez tout d’abord le contrat, qui est défini en utilisant une interface. L’étape suivante consiste pour implémenter l’interface. Cela implique la création d’une classe nommée **ImageService** qui mettent en œuvre, l’interface **IImageContract** définies par l’utilisateur. Après avoir implémenté le contrat, vous configurez puis l’interface à l’aide d’un fichier App.config. Le fichier de configuration contient les informations nécessaires pour l’application, telles que le nom du service et le nom de contrat, ainsi que le type de protocole est utilisé pour communiquer à Service. Le code utilisé pour ces tâches est fourni dans l’exemple suivant la procédure.

Comme avec la procédure précédente, il est très peu de différences entre l’implémentation d’un contrat reste style et un contrat de Service Bus base.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Pour mettre en œuvre un contrat reste style Bus des services

1. Créer une nouvelle classe nommée **ImageService** juste après la définition de l’interface **IImageContract** . La classe **ImageService** met en œuvre l’interface **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Similaires aux autres mises en œuvre interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, pour ce didacticiel, la mise en œuvre s’affiche dans le même fichier en tant que la définition d’interface et `Main()` méthode.

2. Appliquer l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à la classe **IImageService** pour indiquer que la classe est une implémentation d’un contrat WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Comme indiqué précédemment, cet espace de noms n’est pas un espace de noms traditionnel. Au lieu de cela, il fait partie de l’architecture WCF qui identifie le contrat. Pour plus d’informations, consultez la rubrique [Noms de contrat de données](https://msdn.microsoft.com/library/ms731045.aspx) dans la documentation WCF.

3. Ajouter une image .jpg à votre projet.  

    Il s’agit d’une image qui affiche le service dans le navigateur de réception. Avec le bouton droit de votre projet, puis cliquez sur **Ajouter**. Cliquez ensuite sur **Un élément existant**. Utiliser la boîte de dialogue **Ajouter un élément existant** pour accéder à un fichier .jpg approprié, puis cliquez sur **Ajouter**.

    Lorsque vous ajoutez le fichier, assurez-vous que **Tous les fichiers** est sélectionné dans la liste déroulante à côté du **nom de fichier :** champ. Le reste de ce didacticiel suppose que le nom de l’image est « image.jpg ». Si vous avez un autre fichier, vous devrez renommer l’image, ou modifier votre code à compense perte.

4. Pour vous assurer que le service en cours d’exécution peut trouver le fichier image, dans **L’Explorateur de** droit sur le fichier image, puis cliquez sur **Propriétés**. Dans le volet **Propriétés** , définissez **Copier dans le répertoire de sortie** à **Copier si plus récent**.

5. Ajoutez une référence à l’assembly **System.Drawing.dll** au projet et ajoutez également ce qui suit associé `using` instructions.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Dans la classe **ImageService** , ajoutez le constructeur suivant qui charge le bitmap et prépare pour l’envoyer vers le navigateur client.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Juste après le code précédent, ajoutez la méthode **GetImage** suivante de la classe **ImageService** pour renvoyer un message HTTP qui contient l’image.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Cette implémentation utilise **MemoryStream** pour récupérer l’image et le préparer pour la diffusion en continu vers le navigateur. Elle commence la position du flux à zéro, déclare le contenu de flux de données en tant que jpeg et utilise les informations.

8. Dans le menu **Générer** , cliquez sur **Générer la Solution**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Pour définir la configuration pour l’exécution du service web sur Bus des services

1. Dans l' **Explorateur de solutions**, double-cliquez sur **App.config** pour l’ouvrir dans l’éditeur Visual Studio.

    Le fichier **App.config** ressemble à un fichier de configuration WCF et inclut le nom de service, le point de terminaison (autrement dit, l’emplacement expose Bus des services pour les clients et hôtes communiquer avec eux) et la liaison (le type de protocole qui est utilisé pour communiquer). La différence principale est que le point de terminaison du service configuré fait référence à une liaison [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) , qui ne fait pas partie du .NET Framework.

2. La `<system.serviceModel>` élément XML est un élément WCF qui définit un ou plusieurs services. Ici, elle est utilisée pour définir le nom de service et le point de terminaison. En bas de la `<system.serviceModel>` élément (mais toujours dans `<system.serviceModel>`), ajoutez un `<bindings>` élément qui comporte le contenu suivant. Cette opération définit les liaisons utilisées dans l’application. Vous pouvez définir plusieurs liaisons, mais pour ce didacticiel vous définissez qu’un seul.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Cette étape définit une liaison de Service Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) avec **relayClientAuthenticationType** définie sur **Aucune**. Ce paramètre indique qu’un point de terminaison à l’aide de cette liaison ne nécessite pas d’informations d’identification client.

3. Après le `<bindings>` élément, ajoutez un `<services>` élément. Comme pour les liaisons, vous pouvez définir plusieurs services dans un seul fichier de configuration. Toutefois, pour ce didacticiel, vous ne définissez qu’un seul.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Cette étape configure un service qui utilise la valeur par défaut défini précédemment **webHttpRelayBinding**. Elle utilise également la valeur par défaut **sbTokenProvider**, qui est défini à l’étape suivante.

4. Après le `<services>` élément, créer une `<behaviors>` élément avec le contenu suivant, en remplaçant « SAS_KEY » avec la touche *Partagés accès Signature* (sa) obtenu à partir du [portail Azure][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Toujours dans App.config, dans le `<appSettings>` élément, la valeur de chaîne de la connexion entière avec la chaîne de connexion que vous avez précédemment obtenu à partir du portail de remplacer. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Dans le menu **Générer** , cliquez sur **Générer la Solution** pour générer la solution entière.

### <a name="example"></a>Exemple

Le code suivant illustre l’implémentation de contrat ou service d’un service basée sur REST qui s’exécute sur Bus des services à l’aide de la liaison **WebHttpRelayBinding** .

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

L’exemple suivant montre le fichier App.config associé au service.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Étape 4 : Héberger le service WCF basée sur REST pour utiliser Bus des services

Cette étape décrit l’exécution d’un service web à l’aide d’une application console sur Bus des services. Liste complète du code écrit dans cette étape est fournie dans l’exemple suivant la procédure.

### <a name="to-create-a-base-address-for-the-service"></a>Pour créer une adresse de base pour le service

1. Dans la `Main()` déclaration de fonction, créer une variable pour stocker l’espace de noms de votre projet Bus des services. Veillez à remplacer `yourNamespace` avec le nom de l’espace de noms de service que vous avez créé précédemment.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus utilise le nom de votre espace de noms pour créer un URI unique.

2. Créer un `Uri` instance pour l’adresse de base du service basé sur l’espace de noms.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Pour créer et configurer l’hôte de service web

- Créer l’hôte de service web à l’aide de l’adresse URI créée précédemment dans cette section.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    L’hôte de service est l’objet WCF qui instancie l’application hôte. Cet exemple passe le type d’hôte que vous voulez créer (un **ImageService**), ainsi que l’adresse à laquelle vous souhaitez exposer l’application hôte.

### <a name="to-run-the-web-service-host"></a>Pour exécuter l’hôte de service web

1. Ouvrez le service.

    ```
    host.Open();
    ```
    Le service est actif.

2. Afficher un message indiquant que le service est en cours d’exécution et comment arrêter le service.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Lorsque vous avez terminé, fermez l’hôte de service.

    ```
    host.Close();
    ```

## <a name="example"></a>Exemple

L’exemple suivant inclut le contrat de service et la mise en œuvre des étapes précédentes dans le didacticiel et héberge le service dans une application console. Compiler le code suivant dans un fichier exécutable nommé ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilation du code

Après avoir créé la solution, procédez comme suit pour exécuter l’application :

1. Appuyez sur **F5**, ou recherchez l’emplacement du fichier exécutable (ImageListener\bin\Debug\ImageListener.exe) pour exécuter le service. Maintenir l’application en cours d’exécution, est nécessaire pour effectuer l’étape suivante.

2. Copiez et collez l’adresse de l’invite de commande dans un navigateur pour voir l’image.

3. Lorsque vous avez terminé, appuyez sur **entrée** dans la fenêtre d’invite de commande pour fermer l’application.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez créé une application qui utilise le service de relais Bus des services, consultez les articles suivants pour plus d’informations sur la messagerie relayé :

- [Vue d’ensemble d’architecturale Azure Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Comment utiliser le Service de relais Bus](service-bus-dotnet-how-to-use-relay.md)

[Portail Azure]: https://portal.azure.com