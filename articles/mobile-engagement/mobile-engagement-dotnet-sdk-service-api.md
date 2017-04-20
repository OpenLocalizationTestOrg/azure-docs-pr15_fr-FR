<properties 
    pageTitle="À l’aide du Kit de développement .NET pour accéder aux API de Service Engagement Azure Mobile" 
    description="Décrit comment utiliser le Kit de développement Mobile Engagement .NET pour accéder aux API de Service Engagement Azure Mobile"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>À l’aide du Kit de développement .NET pour accéder aux API de Service Engagement Azure Mobile

Azure Mobile Engagement expose un ensemble d’API vous permettant de gérer les périphériques, accessibles/Push campagnes etc.. Pour interagir avec ces API, nous également vous fournir un [fichier Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que vous pouvez utiliser avec les outils pour générer SDK pour la langue par défaut. Nous vous recommandons d’utiliser l’outil [AutoRest](https://github.com/Azure/AutoRest) pour générer votre SDK à partir de notre fichier Swagger. 

Nous avons créé un SDK .NET de la même manière qui vous permet d’interagir avec ces API en utilisant un emballage c# et vous n’êtes pas obligé de suivre la négociation jeton d’authentification et actualiser vous-même.  

Cet exemple parcourt l’ensemble des étapes à suivre pour utiliser le Kit de développement .NET :

1. Tout d’abord, vous devez configurer l’authentification pour votre API utilisant Azure Active Directory comme indiqué [ici](mobile-engagement-api-authentication.md#authentication). À la fin de cette procédure, vous devez avoir un **SubscriptionId**, **TenantId**, **ApplicationId** et **Secret**valide. 

2. Nous allons utiliser une application de Console Windows simple pour illustrer l’utilisation du Kit de développement .NET avec le scénario de création d’une campagne d’annonce. Pour ouvrir Visual Studio et créez une **Application Console**.   

3. Vous devez ensuite télécharger le Kit de développement .NET est disponible en tant que **Microsoft Azure Engagement gestion de la bibliothèque** dans la galerie Nuget [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Si vous installez la Nuget de Visual Studio, vous devez vous assurer que vous avez activé l’option **inclure la version préliminaire** lors de la recherche pour le package :

    ![][1]

4. Dans la `Program.cs` , ajoutez des espaces de noms suivants :

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. Ensuite, vous devez définir l’une des constantes suivantes que nous utiliserons pour l’authentification et interaction avec l’application d’Engagement Mobile dans lequel vous créez la campagne annonce :

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Définir la `EngagementManagementClient` variable que nous utiliserons d’appeler les méthodes de kit de développement Mobile Engagement :

        static EngagementManagementClient engagementClient; 

7. Ajoutez les éléments suivants à votre `Main` méthode :

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Définir la méthode suivante qui prend en charge de l’initialisation de la `EngagementManagementClient` par tout d’abord l’authentification, puis associer lui-même avec l’application d’Engagement Mobile dans lequel vous prévoyez de créer la campagne annonce :

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Notez que vous devez utiliser le **Nom de la ressource application** telle que définie dans le portail de gestion Azure pour le paramètre AppName. 

9. Enfin, définissez la méthode CreateCampaign qui se charge de l’utilisation de la EngagementClient précédemment initialisé pour créer un simple **à tout moment** & **Notification seule** campagne avec un titre et un message : 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Exécuter l’application console et vous verrez les éléments suivants sur la création de réussite de la campagne :

    **Id de campagne '159' a été créé correctement et qu’il est dans l’état « Brouillon »**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
