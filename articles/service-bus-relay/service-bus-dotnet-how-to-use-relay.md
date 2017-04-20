<properties
    pageTitle="Comment utiliser le relais Bus des services avec .NET | Microsoft Azure"
    description="Découvrez comment utiliser le service de relais Bus des services Azure pour vous connecter deux applications hébergées à des endroits différents."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Comment utiliser le service de relais Bus des services Azure

Cet article décrit comment utiliser le service de relais Bus des services. Les exemples sont écrits en c# et utilisent l’API Windows Communication Foundation (WCF) avec les extensions contenues dans l’ensemble de Bus de Service. Pour plus d’informations sur le relais Bus des services, consultez la présentation [relais Bus des services de messagerie](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>Quel est le relais Bus des services ?

Le service [Service Bus *relais* ](service-bus-relay-overview.md) vous permet de vous permettent de créer des applications hybrides qui s’exécutent dans un centre de données Azure et votre propre environnement d’entreprise en local. Le relais Bus des services facilite cela en activant en toute sécurité exposer les services Windows Communication Foundation (WCF) qui résident dans un réseau d’entreprise dans le cloud public, sans avoir à ouvrir une connexion de pare-feu ou apporter de changements à une infrastructure de réseau d’entreprise.

![Concepts de relais](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Le relais Bus des services vous permet d’héberger des services WCF au sein de votre environnement d’entreprise existant. Vous pouvez ensuite déléguer à l’écoute d’entrant sessions et requêtes à ces services WCF au service Bus des services qui s’exécutent dans Azure. Cela vous permet d’exposer ces services au code de l’application en cours d’exécution dans Azure, ou pour les utilisateurs mobiles ou environnements partenaire extranet. Bus des services vous permet de contrôler en toute sécurité les personnes peut accéder à ces services à un niveau Permissions. Il propose un moyen puissant et sécurisé pour exposer des fonctionnalités de l’application et des données à partir de vos solutions d’entreprise existantes et tirer parti de celle-ci à partir du cloud.

Cet article explique comment utiliser le relais Bus des services pour créer un service web WCF, exposé à l’aide d’une liaison de canal TCP, qui mettent en œuvre, une conversation sécurisée entre deux parties.

## <a name="create-a-service-namespace"></a>Créer un espace de noms de service

Pour commencer à utiliser le relais Bus des services dans Azure, vous devez d’abord créer un espace de noms. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application.

Pour créer un espace de noms de service :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obtenir le package NuGet Bus de Service

Le [package Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple pour obtenir de l’API Bus Service et configurer votre application avec toutes les dépendances Bus des services. Pour installer le package NuGet dans votre projet, procédez comme suit :

1.  Dans l’Explorateur de solutions, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.
2.  Recherchez « Bus des services » et sélectionnez l’élément **Bus des services Microsoft Azure** . Cliquez sur **installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante :

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Utiliser Bus des services pour afficher et utiliser un service web SOAP avec TCP

Pour exposer un service web WCF SOAP existant pour l’extérieur, vous devez apporter des modifications pour les liaisons de service et les adresses. Cela peut nécessiter des modifications apportées à votre fichier de configuration ou elle peut nécessiter des modifications du code, selon la façon dont vous avez installé et configuré vos services WCF. Notez que WCF vous permet d’avoir plusieurs points de terminaison réseau sur le même service, afin de mémoriser les points de terminaison internes existants lors de l’ajout de points de terminaison de Bus des services pour l’accès externe en même temps.

Dans cette tâche, vous créer un service WCF simple et ajouter un récepteur Bus des services. Cet exercice connaissance de Visual Studio et par conséquent n’effectue pas de recherche sur les détails de la création d’un projet. Au lieu de cela, il se concentre sur le code.

Avant de commencer cette procédure, procédez comme suit pour configurer votre environnement :

1.  Dans Visual Studio, créez une application console qui contient deux projets, « Client » et « Service », au sein de la solution.
2.  Ajouter le package Microsoft Azure Service Bus NuGet dans les deux projets. Ce package ajoute toutes les références assembly nécessaires à vos projets.

### <a name="how-to-create-the-service"></a>Comment créer le service

Commencez par créer le service lui-même. N’importe quel service WCF comporte au moins trois parties distinctes :

-   Définition d’un contrat qui décrit les messages échangés et opérations sont à appeler.
-   Implémentation de ce contrat.
-   Hôte qui héberge le service WCF et expose plusieurs points de terminaison.

Les exemples de code dans cette section appliqué à chacune de ces composants.

Le contrat définit une seule opération, `AddNumbers`, qui ajoute deux nombres et retourne le résultat. La `IProblemSolverChannel` interface permet au client gérer plus facilement la durée de vie de proxy. Création d’une telle interface est considéré comme une meilleure pratique. Il est recommandé de placer cette définition de contrat dans un fichier séparé afin que vous pouvez faire référence à ce fichier à partir de projets à la fois votre « Client » et « Service », mais vous pouvez également copier le code dans les deux projets.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Avec le contrat en place, l’implémentation est simple.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurer un hôte de service par programme

Avec le contrat et implémentation en place, vous pouvez désormais héberger le service. Hébergement se produit à l’intérieur d’un objet [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , qui prend en charge de la gestion des instances du service et héberge les points de terminaison recevoir des messages. Le code suivant configure le service avec un point de terminaison local normal et un point de terminaison du Service Bus pour illustrer l’apparence, côte à côte, des points de terminaison internes et externes. Remplacer *l’espace de noms* de chaîne avec votre espace de noms et *yourKey* avec la touche associations de sécurité que vous avez obtenu à l’étape précédente le programme d’installation.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Dans l’exemple, vous créez deux points de terminaison qui se trouvent sur la même implémentation de contrat. Une est locale et une est projetée via Bus des services. Les principales différences entre elles sont les liaisons ; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) pour l’option local et [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) pour le point de terminaison du Service Bus et les adresses. Le point de terminaison local possède une adresse réseau local avec un port distinct. Le point de terminaison du Service Bus possède une adresse de point de terminaison composée de la chaîne `sb`, votre espace de noms et le chemin d’accès « du solveur. » Cela se traduit par l’URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identifier le point de terminaison du service en tant qu’un point de terminaison du Service Bus TCP avec un nom DNS externe complet. Si vous placez le code de remplacer les espaces réservés dans les `Main` fonction de l’application de **Service** , vous aurez un service fonctionnel. Si vous souhaitez que votre service d’écouter en mode exclusif Bus des services, supprimez la déclaration de point de terminaison local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurer un hôte de service dans le fichier App.config

Vous pouvez également configurer l’hôte en utilisant le fichier App.config. Le service d’hébergement dans ce cas de code apparaît dans l’exemple suivant.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Les définitions de point de terminaison déplacement vers le fichier App.config. Le package NuGet a déjà ajouté une plage de définitions dans le fichier App.config, qui sont les configuration requise des extensions Bus des services. L’exemple suivant, ce qui correspond à l’équivalent exact du code précédent, doit apparaître directement en dessous de l’élément **system.serviceModel** . Cet exemple de code suppose que votre espace de noms de projet c# est nommé **Service**.
Remplacer les espaces réservés par votre espace de noms de service Bus des services et des touches associations de sécurité.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Après avoir effectué ces modifications, le service démarre comme auparavant, mais avec deux points de terminaison live : un utilisateur local et à l’écoute une dans le cloud.

### <a name="create-the-client"></a>Création du client

#### <a name="configure-a-client-programmatically"></a>Configurer un client par programme

Pour utiliser le service, vous pouvez créer un client WCF à l’aide d’un objet [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Bus des services utilise un modèle basé sur un jeton de sécurité implémenté à l’aide des associations de sécurité. La classe [TokenProvider a](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jeton de sécurité avec les méthodes d’usine intégrée qui renvoient certains fournisseurs jetons connus. L’exemple suivant utilise la méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour gérer l’acquisition du jeton de sa approprié. Le nom et la clé sont ceux obtenus à partir du portail comme décrit dans la section précédente.

Tout d’abord, référence ou copier la `IProblemSolver` code à partir du service de contrat dans votre projet client.

Ensuite, remplacez le code dans la `Main` méthode du client, remplaçant à nouveau le texte d’espace réservé par votre espace de noms Bus des services et des touches associations de sécurité.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Vous pouvez à présent générer le client et le service, exécutez les (exécutez d’abord le service), et le client appelle le service et imprime **9**. Vous pouvez exécuter le client et le serveur sur des ordinateurs différents, y compris dans les réseaux, et la communication fonctionnera toujours. Le code client peut également exécuter dans le nuage ou localement.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configuration d’un client dans le fichier App.config

Le code suivant montre comment configurer le client en utilisant le fichier App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Les définitions de point de terminaison déplacement vers le fichier App.config. L’exemple suivant, qui est identique au code répertorié précédemment, doit apparaître directement en dessous de l’élément **system.serviceModel** . Ici, comme auparavant, vous devez remplacer les espaces réservés par votre espace de noms Bus des services et des touches associations de sécurité.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base du service de relais Bus des services, suivez ces liens pour en savoir plus.

- [Service Bus relais messagerie vue d’ensemble](service-bus-relay-overview.md)
- [Vue d’ensemble d’architecturale Azure Bus des services](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Télécharger les exemples de Bus de Service à partir [d’Azure échantillons][] ou afficher un [Aperçu des exemples de Bus des services][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Exemples d’Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [vue d’ensemble des exemples de Bus des services]: ../service-bus-messaging/service-bus-samples.md