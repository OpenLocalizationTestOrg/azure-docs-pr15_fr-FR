<properties
    pageTitle="Application de cloud/le locaux hybride (.NET) | Microsoft Azure"
    description="Apprenez à créer une application de cloud/le locaux hybride .NET en utilisant le relais Bus des services Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Application de cloud/le locaux hybride .NET à l’aide de Azure Service Bus relais

## <a name="introduction"></a>Introduction

Cet article décrit comment créer une application en nuage hybride avec Microsoft Azure et Visual Studio. Ce didacticiel, que vous n’avez aucune expérience préalable à l’aide d’Azure. En moins de 30 minutes, vous aurez une application qui utilise plusieurs ressources Azure vers le haut et en cours d’exécution dans le cloud.

Vous allez apprendre :

-   Comment créer ou adapter un service web existant pour la consommation en une solution web.
-   Comment utiliser le service de relais de Bus Azure Service pour partager des données entre une application Azure et un service web hébergé ailleurs.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Comment le relais Bus des services vous aide à grâce aux solutions hybride

Solutions d’entreprise sont généralement composées d’une combinaison de code personnalisé écrit à s’attaquer uniques des exigences et les fonctionnalités existantes fournies par les solutions et les systèmes qui sont déjà en place.

Architectes de solution commencent à utiliser le cloud pour gérer plus facilement des exigences échelle et réduire les coûts opérationnels. Dans ce cas, ils trouver que des ressources de service existantes qu'ils souhaiteraient tirer parti des blocs de construction pour leurs solutions sont à l’intérieur du pare-feu d’entreprise et se déconnecter facile atteint pour l’accès par la solution cloud. De nombreux services internes pas intégrés ou hébergés de sorte qu’elles peuvent être facilement exposées en périphérie du réseau d’entreprise.

Le relais Bus des services est conçu pour le cas d’utilisation des services web de Windows Communication Foundation (WCF) existants et rendre ces services en toute sécurité accessibles aux solutions qui se trouvent en dehors du périmètre d’entreprise sans apporter de changements à l’infrastructure réseau d’entreprise. Ces services de relais Bus des services sont toujours hébergés dans leur environnement existant, mais ils déléguer à l’écoute d’entrant sessions et demandes au Bus Service hébergé sur le nuage. Service Bus protège également ces services des accès non autorisés à l’aide de l’authentification [Partagé Access Signature](../service-bus-messaging/service-bus-sas-overview.md) (sa).

## <a name="solution-scenario"></a>Scénario de solution

Dans ce didacticiel, vous allez créer un site Web ASP.NET qui vous permet d’afficher la liste de produits dans la page d’inventaire de produit.

![][0]

Le didacticiel suppose que vous possédez des informations sur le produit d’un système local existant et utilise le relais Bus des services pour accéder à ce système. Ceci est simulé par un service web qui s’exécute dans une application console simple et est sauvegardé par un ensemble en mémoire de produits. Vous pourrez exécuter cette application console sur votre ordinateur et déployer le rôle web dans Azure. En procédant ainsi, vous verrez comment le rôle web en cours d’exécution dans le centre de données Azure est bien appelle à votre ordinateur, même si votre ordinateur résideront certainement derrière au moins un pare-feu et un calque réseau adresse de la traduction.

Voici une capture d’écran de la page de démarrage de l’application web terminée.

![][1]

## <a name="set-up-the-development-environment"></a>Configurer l’environnement de développement

Avant de pouvoir commencer le développement d’applications Azure, utilisez les outils et configurer votre environnement de développement.

1.  Installez le Kit de développement Azure pour .NET à partir de la page [obtenir des outils et Kit de développement logiciel][] .

2.  Cliquez sur **installer le Kit de développement** pour la version de Visual Studio que vous utilisez. Les étapes décrites dans ce didacticiel utilisent Visual Studio 2015.

4.  Lorsque vous êtes invité à exécuter ou enregistrer le programme d’installation, cliquez sur **exécuter**.

5.  Dans **Programme d’installation de la plateforme Web**, cliquez sur **l’installation** et poursuivre l’installation.

6.  Une fois l’installation terminée, vous aurez accès à tous les éléments nécessaires pour commencer à développer l’application. Le Kit de développement inclut des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si vous n’avez pas installé Visual Studio, le Kit de développement installe également la version gratuite Express Visual Studio.

## <a name="create-a-namespace"></a>Créer un espace de noms

Pour commencer à utiliser les fonctionnalités ESB dans Azure, vous devez d’abord créer un espace de noms de service. Un espace de noms fournit un conteneur portée d’adressage ressources Bus de Service au sein de votre application.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Création d’un serveur local

Tout d’abord, vous allez créer un système de catalogue produit locaux (fictifs). Il sera assez simple ; Vous pouvez voir ceci comme représentant un système de catalogue produit réel en local avec une surface de service complet que nous voulons intégrer.

Ce projet est une application de console Visual Studio et utilise le [package Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pour inclure les bibliothèques de Bus des services et les paramètres de configuration.

### <a name="create-the-project"></a>Créer le projet

1.  En utilisant des privilèges d’administrateur, démarrez Microsoft Visual Studio. Pour démarrer Visual Studio en tant qu’administrateur, cliquez sur l’icône du programme **Visual Studio** , puis cliquez sur **Exécuter en tant qu’administrateur**.

2.  Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

3.  À partir de **Modèles installés**, sous **Visual c#**, cliquez sur **Application Console**. Dans la zone **nom** , tapez le nom **ProductsServer**:

    ![][11]

4.  Cliquez sur **OK** pour créer le projet **ProductsServer** .

7.  Si vous avez déjà installé le Gestionnaire de package NuGet pour Visual Studio, passez à l’étape suivante. Dans le cas contraire, visitez [NuGet][] et cliquez sur [Installer NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Suivez les invites pour installer le Gestionnaire de package NuGet, puis redémarrez Visual Studio.

7.  Dans l’Explorateur, droit sur le projet **ProductsServer** , puis cliquez sur **Gérer les Packages NuGet**.

8.  Cliquez sur l’onglet **Parcourir** , puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **l’installation**et acceptez les conditions d’utilisation.

    ![][13]

    Notez que les assemblys client requis sont désormais référencés.

9.  Ajouter une nouvelle classe pour votre contrat produit. Dans l’Explorateur, droit sur le projet **ProductsServer** et cliquez sur **Ajouter**, puis cliquez sur **cours**.

10. Dans la zone **nom** , tapez le nom **ProductsContract.cs**. Puis cliquez sur **Ajouter**.

11. Dans **ProductsContract.cs**, remplacez la définition de l’espace de noms par le code suivant, qui définit le contrat de service.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. Dans Program.cs, remplacez la définition de l’espace de noms par le code suivant, qui ajoute le service de profil et de l’hôte de celui-ci.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. Dans l’Explorateur de solutions, double-cliquez sur le fichier **App.config** pour l’ouvrir dans l’éditeur Visual Studio. Dans la partie inférieure de la ** &lt;système. ServiceModel&gt; ** élément (mais toujours compris &lt;système. ServiceModel&gt;), ajoutez le code XML suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms et *yourKey* avec la touche associations de sécurité que vous avez récupérée précédemment à partir du portail :

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Toujours dans App.config, dans le ** &lt;appSettings&gt; ** élément, remplacer la chaîne de connexion valeur avec la chaîne de connexion que vous avez précédemment obtenu à partir du portail. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Appuyez sur **Ctrl + Maj + B** ou dans le menu **Générer** , cliquez sur **Générer la Solution** pour générer l’application et vérifiez l’exactitude de votre travail jusqu'à présent.

## <a name="create-an-aspnet-application"></a>Créer une application ASP.NET

Dans cette section vous allez créer une application ASP.NET simple qui affiche des données extraites votre service de produit.

### <a name="create-the-project"></a>Créer le projet

1.  Assurez-vous que Visual Studio s’exécute en tant qu’administrateur.

2.  Dans Visual Studio, dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.

3.  À partir de **Modèles installés**, sous **Visual c#**, cliquez sur **Application Web ASP.NET**. Nommez le projet **ProductsPortal**. Cliquez ensuite sur **OK**.

    ![][15]

4.  Dans la liste **Sélectionner un modèle** , cliquez sur **MVC**. 

6.  Cochez la case pour **hôte dans le cloud**.

    ![][16]

5. Cliquez sur le bouton **Modifier l’authentification** . Dans la boîte de dialogue **Modifier l’authentification** , cliquez sur **Aucune authentification**, puis cliquez sur **OK**. Pour ce didacticiel, vous déployez une application qui ne doit pas une connexion de l’utilisateur.

    ![][18]

6.  Dans la section **Microsoft Azure** de la boîte de dialogue **Nouveau projet ASP.NET** , assurez-vous que **l’hôte dans le nuage** est sélectionnée et que **L’application Service** est sélectionné dans la liste déroulante.

    ![][19]

7. Cliquez sur **OK**. 

8. Vous devez à présent configurer Azure ressources pour une nouvelle application web. Suivez les étapes dans la section [configurer Azure ressources pour une nouvelle application web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Ensuite, retournez dans ce didacticiel, puis procédez à l’étape suivante.

5.  Dans l’Explorateur de solutions, avec le bouton droit de **modèles** et cliquez sur **Ajouter**, puis cliquez sur **cours**. Dans la zone **nom** , tapez le nom **Product.cs**. Puis cliquez sur **Ajouter**.

    ![][17]

### <a name="modify-the-web-application"></a>Modifier l’application web

1.  Dans le fichier Product.cs dans Visual Studio, remplacez la définition d’espace de noms existant par le code suivant.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  Dans l’Explorateur de solutions, développez le dossier **Controllers** , puis double-cliquez sur le fichier **HomeController.cs** pour l’ouvrir dans Visual Studio.

3. Dans **HomeController.cs**, remplacez la définition d’espace de noms existante par le code suivant.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  Dans l’Explorateur de solutions, développez le dossier Views\Shared, puis double-cliquez sur **_Layout.cshtml** pour l’ouvrir dans l’éditeur Visual Studio.

5.  Remplacez toutes les occurrences de **Mon Application ASP.NET** **produits de LITWARE**.

6. Supprimer les liens **accueil**, **sur**et **Contact** . Dans l’exemple suivant, supprimez le code en surbrillance.

    ![][41]

7.  Dans l’Explorateur de solutions, développez le dossier Views\Home, puis double-cliquez sur **Index.cshtml** pour l’ouvrir dans l’éditeur Visual Studio.
    Remplacez tout le contenu du fichier par le code suivant.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Pour vérifier l’exactitude de votre travail jusque là, vous pouvez appuyer sur **Ctrl + Maj + B** pour générer le projet.


### <a name="run-the-app-locally"></a>Exécuter l’application localement

Exécutez l’application pour vérifier qu’il fonctionne.

1.  Vérifiez que **ProductsPortal** est le projet actif. Cliquez sur le nom du projet dans l’Explorateur et sélectionnez **Définir comme projet de démarrage**.
2.  Dans Visual Studio, appuyez sur F5.
3.  Votre application doit apparaître, en cours d’exécution dans un navigateur.

    ![][21]

## <a name="put-the-pieces-together"></a>Rassembler les différents éléments

L’étape suivante consiste à connecter le serveur de produits en local avec l’application ASP.NET.

1.  Si elle n’est pas déjà ouvert, dans Visual Studio rouvrir le projet **ProductsPortal** que vous avez créé dans la section [Création d’une application ASP.NET](#create-an-aspnet-application) .

2.  Similaire à l’étape dans la section « Créer d’un serveur local de suite », ajoutez le package NuGet aux références du projet. Dans l’Explorateur, droit sur le projet **ProductsPortal** , puis cliquez sur **Gérer les Packages NuGet**.

3.  Recherchez « Bus des services » et sélectionnez l’élément **Bus des services Microsoft Azure** . Terminer l’installation, puis fermez la boîte de dialogue.

4.  Dans l’Explorateur de solutions, droit sur le projet **ProductsPortal** , puis cliquez sur **Ajouter**, puis **Un élément existant**.

5.  Accédez au fichier **ProductsContract.cs** du projet de console **ProductsServer** . Cliquez sur pour mettre en surbrillance ProductsContract.cs. Cliquez sur la flèche vers le bas en regard de **Ajouter**, puis cliquez sur **Ajouter en tant que lien**.

    ![][24]

6.  Ouvrez le fichier **HomeController.cs** dans l’éditeur Visual Studio et remplacez la définition de l’espace de noms par le code suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms de service et *yourKey* avec votre clé associations de sécurité. Vous activez le client appeler le service en local, renvoie le résultat de l’appel.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  Dans l’Explorateur de solutions, cliquez sur la solution **ProductsPortal** (veillez avec le bouton droit de la solution, et non sur le projet). Cliquez sur **Ajouter**, puis cliquez sur **Projet existant**.

8.  Naviguez jusqu’au projet **ProductsServer** , puis double-cliquez sur le fichier de solution **ProductsServer.csproj** pour l’ajouter.

9.  **ProductsServer** devez exécuter pour afficher les données sur **ProductsPortal**. Dans l’Explorateur, avec le bouton droit de la solution **ProductsPortal** , puis cliquez sur **Propriétés**. La boîte de dialogue **Pages de propriétés** s’affiche.

10. Dans la partie gauche, cliquez sur **Projet de démarrage**. Sur le côté droit, cliquez sur **plusieurs projets de démarrage**. Vérifier que **ProductsServer** et **ProductsPortal** s’affichent, dans cet ordre, en cliquant sur **Démarrer** définir en tant que l’action pour les deux.

      ![][25]

11. Toujours dans la boîte de dialogue **Propriétés** , cliquez sur **Dépendances du projet** sur le côté gauche.

12. Dans la liste des **projets** , cliquez sur **ProductsServer**. Vous assurer que **ProductsPortal** n’est **pas** sélectionnée.

14. Dans la liste des **projets** , cliquez sur **ProductsPortal**. Vérifiez que **ProductsServer** est sélectionné. 

    ![][26]

15. Cliquez sur **OK** dans la boîte de dialogue **Pages de propriétés** .

## <a name="run-the-project-locally"></a>Exécuter le projet localement

Pour tester l’application localement, dans Visual Studio, appuyez sur **F5**. Le serveur local (**ProductsServer**) doit démarrer en premier, puis l’application **ProductsPortal** doit commencer dans une fenêtre de navigateur. Cette fois, vous verrez que l’inventaire produits répertorie les données récupérées à partir du système produit service local.

![][10]

Appuyez sur **Actualiser** dans la page **ProductsPortal** . Chaque fois que vous actualisez la page, vous verrez l’application serveur afficher un message lorsque `GetProducts()` à partir de **ProductsServer** est appelée.

Fermez les deux applications avant de procéder à l’étape suivante.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Déployer le projet ProductsPortal dans une application web Azure

L’étape suivante consiste à convertir frontend **ProductsPortal** pour une application web Azure. Tout d’abord, déployez le projet **ProductsPortal** , suivant les étapes décrites dans la section [déployer le projet web à l’application web Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Après que le déploiement est terminé, revenez à ce didacticiel et passez à l’étape suivante.

> [AZURE.NOTE] Vous pouvez constater un message d’erreur dans la fenêtre de navigateur lorsque le projet web **ProductsPortal** est lancé automatiquement après le déploiement. Cela est prévu et se produit car l’application **ProductsServer** n’est pas encore exécuté.

Copiez l’URL de l’application web déployée, car vous en aurez besoin l’URL de l’étape suivante. Vous pouvez également obtenir cette URL à partir de la fenêtre d’activité de Service d’application Azure dans Visual Studio :

![][9] 

### <a name="set-productsportal-as-web-app"></a>ProductsPortal définir en tant que dans le navigateur

Avant d’exécuter l’application dans le cloud, vous devez vous assurer que **ProductsPortal** est lancé depuis Visual Studio comme une application web.

1. Dans Visual Studio, cliquez sur le projet **ProjectsPortal** , puis sur **Propriétés**.

3. Dans la colonne de gauche, cliquez sur **le Web**.

5. Dans la section **Action de démarrage** , cliquez sur le bouton **Démarrer l’URL** et dans la zone de texte, entrez l’URL de votre application web précédemment déployées ; par exemple, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. Dans le menu **fichier** dans Visual Studio, cliquez sur **Enregistrer tout**.

7. Dans le menu Générer dans Visual Studio, cliquez sur **Reconstruire la Solution**.

## <a name="run-the-application"></a>Exécuter l’application

2.  Appuyez sur F5 pour générer et exécuter l’application. Le serveur local (l’application de la console **ProductsServer** ) doit démarrer en premier, puis l’application **ProductsPortal** doit commencer dans une fenêtre de navigateur, comme le montre la capture d’écran suivante. Avis de nouveau que l’inventaire produits répertorie les données extraites du service de produit système local et affiche ces données dans l’application web. Vérifiez l’URL pour vous assurer que **ProductsPortal** est en cours d’exécution dans le cloud, comme une application web Azure. 

    ![][1]

    > [AZURE.IMPORTANT] L’application de console **ProductsServer** doit être en mesure de traiter les données à l’application **ProductsPortal** et en cours d’exécution. Si le navigateur affiche une erreur, patientez quelques secondes plus **ProductsServer** charger et afficher le message suivant. Appuyez ensuite sur **Actualiser** dans le navigateur.

    ![][37]

3. Dans le navigateur, appuyez sur **Actualiser** dans la page **ProductsPortal** . Chaque fois que vous actualisez la page, vous verrez l’application serveur afficher un message lorsque `GetProducts()` à partir de **ProductsServer** est appelée.

    ![][38]

## <a name="next-steps"></a>Étapes suivantes  

Pour en savoir plus sur Bus des services, consultez les ressources suivantes :  

* [Bus des services Azure][sbwacom]  
* [Comment utiliser les files d’attente de Bus de Service][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenir des outils et Kit de développement]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

