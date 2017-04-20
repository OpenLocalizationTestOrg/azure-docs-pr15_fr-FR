<properties 
    pageTitle="Pour accéder aux API de Service Engagement Azure Mobile à l’aide de l’API REST" 
    description="Décrit comment utiliser l’API REST de Engagement Mobile pour accéder aux API de Service Engagement Azure Mobile"       
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="wesmc;ricksal" />

#<a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>À l’aide de repos pour accéder aux API de Service Engagement Azure Mobile

Azure Engagement Mobile fournit l' [API REST de Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) vous permettant de gérer les périphériques, accessibles/Push campagnes etc.. Cet exemple utilise l’API REST directement à créer une campagne d’annonce puis activer et transmission à un ensemble d’appareils. 

Si vous ne souhaitez pas utiliser les API REST directement, nous proposons également un [Swagger fichier](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que vous pouvez utiliser avec les outils pour générer SDK pour la langue par défaut. Nous vous recommandons d’utiliser l’outil [AutoRest](https://github.com/Azure/AutoRest) pour générer votre SDK à partir de notre fichier Swagger. Nous avons créé un SDK .NET de la même manière qui vous permet d’interagir avec ces API en utilisant un emballage c# et vous n’êtes pas obligé de suivre la négociation jeton d’authentification et actualiser vous-même. Si vous voulez parcourir un exemple similaire à l’aide de cette emballage, voir [Service API .NET SDK exemple](mobile-engagement-dotnet-sdk-service-api.md)

Cet exemple utilise l’API REST directement pour créer et activer une campagne d’annonce. 

## <a name="adding-a-username-appinfo-to-the-mobile-engagement-app"></a>Ajout d’un appInfo nom_utilisateur à l’application Mobile Engagement

Cet exemple nécessite une balise d’application-info ajoutée à l’application Mobile Engagement. Dans le portail d’engagement pour votre application, vous pouvez ajouter la balise en cliquant sur **paramètres** > **balise (application-info)** > **nouvelle balise (application-info)**. Ajouter la nouvelle balise nommée **nom_utilisateur** comme type de **chaîne** .

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



Si vous avez suivi [Prise en main Azure Engagement pour Windows universel applications Mobile](mobile-engagement-windows-store-dotnet-get-started.md), vous pouvez tester l’envoi de la balise **nom_utilisateur** en substituant simplement `OnNavigatedTo()` dans votre `MainPage` classe pour envoyer la balise application-info semblable au code suivant :

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## <a name="creating-the-service-api-app"></a>Création de l’application de Service API

1. Vous devez tout d’abord, quatre paramètres d’authentification à utiliser avec cet exemple. Ces paramètres sont **SubscriptionId**, **TenantId**, **ApplicationId** et le **code Secret**. Pour obtenir ces paramètres d’authentification, il est recommandé que vous utilisez l’approche de script PowerShell mentionnée dans la section *configuration uniques (à l’aide de script)* dans le didacticiel [d’authentification](mobile-engagement-api-authentication.md#authentication) . 

2. Nous allons utiliser une application de Console Windows simple pour montrer le travail avec les API de Service reste pour créer et activer une campagne annonce. Pour ouvrir Visual Studio et créez une nouvelle **Application Console**.   

3. Ensuite, ajoutez le package NuGet **Newtonsoft.Json** à votre projet.

4. Dans la `Program.cs` de fichiers, ajoutez le code suivant `using` déclarations des espaces de noms suivants :

        using System.IO;
        using System.Net;
        using Newtonsoft.Json.Linq;
        using Newtonsoft.Json;

5. Vous devez ensuite définir les constantes dans les `Program` cours. Ces seront utilisés pour l’authentification et interagir avec l’application d’Engagement Mobile dans lequel vous créez la campagne annonce :


        // Parameters needed for authentication of API calls.
        // These are returned from the PowerShell script in the authentication tutorial. 
        // https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

        // The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
        // Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

        // New campaign id returned from creating the new campaign and used to activate and push the campaign
        // a set of devices.
        static String NewCampaignID = null;

        // This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. Ajoutez les deux méthodes suivantes pour la `Program` cours. Ils seront utilisées pour exécuter des API REST et afficher des réponses à la console.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. Ajoutez le code suivant à votre `Main` méthode permettant de générer un jeton d’authentification avec les paramètres d’authentification vous collectées :

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. Maintenant que nous avons un jeton d’authentification valide, nous pouvons créer une campagne accessibles à l’aide de l’API REST de [campagne de créer](https://msdn.microsoft.com/library/azure/mt683742.aspx) . La nouvelle campagne sera une simple **à tout moment** & **Notification seule** campagne annonce avec un titre et un message. Il s’agit une campagne push manuel comme le montre la capture d’écran suivante. Cela signifie qu’il sera uniquement de poussée à l’aide de l’API.


    ![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

    Ajoutez le code suivant à votre `Main` méthode pour créer la campagne annonce : 

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ \"name\": \"BirthdayCoupon\", " +
                                        "\"type\": \"only_notif\", " +
                                        "\"deliveryTime\": \"any\", " +
                                        "\"notificationType\": \"popup\", " +
                                        "\"pushMode\":\"manual\", " +
                                        "\"notificationTitle\": \"Happy Birthday ${user_name}\", " +
                                        "\"notificationMessage\": \"Take extra 10% off on your orders today!\"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. La campagne doit être activée avant qu’il peut être envoyé vers les périphériques. Nous avons enregistré l’ID de la nouvelle campagne dans le `NewCampaignID` variable. Nous utiliserons ce comme un paramètre de chemin d’accès URI pour activer la campagne à l’aide de l’API REST [campagne activer](https://msdn.microsoft.com/library/azure/mt683745.aspx) . Cela doit modifier l’état de la campagne à **planifiée** alors uniquement poussé manuellement avec l’API.

    Ajoutez le code suivant à votre `Main` méthode permettant d’activer la campagne annonce : 

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. Pour envoyer la campagne nous avons besoin fournir les ID de périphériques pour les utilisateurs que nous souhaitons recevoir une notification. Nous utiliserons l’API REST [périphériques de requête](https://msdn.microsoft.com/library/azure/mt683826.aspx) pour obtenir tous les ID de l’appareil. Nous allons ajouter chaque appareil Id à la liste si elle est associée à **nom_utilisateur** appInfo.

    Ajoutez le code suivant à votre `Main` méthode pour obtenir tous les ID de périphériques et remplir le deviceList :

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
            return;
        }


11. Enfin, nous fera la campagne marketing à tous les ID de périphériques dans notre liste à l’aide de l’API REST [campagne Push](https://msdn.microsoft.com/library/azure/mt683734.aspx) . Il s’agit d’une notification **dans l’application** . Si l’application doivent être en cours d’exécution sur l’appareil afin qu’il d’être reçue par l’utilisateur.

    Ajoutez le code suivant à votre `Main` méthode distribuer la campign sur les appareils dans le deviceList :

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{\"deviceIds\":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. Créer et exécuter votre application console. Votre sortie doit être semblable à ce qui suit :


        C:\Users\Wesley\AzME_Service_API_Rest\test.exe

        Getting authorization token...
        
        HTTP Status 200 : OK
        {
          "token_type": "Bearer",
          "expires_in": "3599",
          "expires_on": "1458085162",
          "not_before": "1458081262",
          "resource": "https://management.core.windows.net/",
          "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
        b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
        NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
        MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
        F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
        hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
        }
        
        Got authorization token...
        Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
        aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
        Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
        OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
        iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
        vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
        
        Creating new campaign...
        
        HTTP Status 201 : Created
        {
          "id": 24,
          "state": "draft"
        }
        
        Created new campaign...
        New Campaign Id    : 24
        
        Activating new campaign (ID : 24)...
        
        HTTP Status 200 : OK
        {
          "id": 24,
          "state": "scheduled"
        }
        
        Activated new campaign...
        New Campaign State : scheduled
        
        Getting device IDs...
        
        HTTP Status 200 : OK
        {
          "value": [
            {
              "meta": {
                "lastSeen": 1458080534371,
                "firstSeen": 1458080534371,
                "lastLocation": 1458081389617,
                "lastInfo": 1458080571603
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
            },
            {
              "meta": {
                "lastSeen": 1457990584698,
                "firstSeen": 1457949384025,
                "lastLocation": 1457990914895,
                "lastInfo": 1457990584597
              },
              "appInfo": {
                "user_name": "Wesley"
              },
              "deviceId": "302486644890e26045884ee5aa0619ec"
            }
          ]
        }
        
        Got devices.
        Adding device for push campaign...
        Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
        user_name    : Wesley
        Adding device for push campaign...
        Device ID    : 302486644890e26045884ee5aa0619ec
        user_name    : Wesley
        
        Triggering push for new campaign (ID : 24)...
        
        
        {"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
        
        HTTP Status 200 : OK
        {
          "invalidDeviceIds": []
        }
        
        Triggered push on new campaign.
        


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
