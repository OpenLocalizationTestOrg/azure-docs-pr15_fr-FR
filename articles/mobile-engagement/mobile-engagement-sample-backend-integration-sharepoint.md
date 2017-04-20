<properties 
    pageTitle="Azure Engagement Mobile - intégration principale" 
    description="Se connecter Azure Mobile Engagement avec un serveur principal SharePoint pour créer des campagnes à partir de SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Azure Engagement Mobile - intégration de l’API

Dans un système de marketing automatisé, création et l’activation les campagnes marketing également se produisent automatiquement. À cet effet - Azure Mobile Engagement permet de créer ces campagnes marketing automatisés ainsi que l’utilisation d’API. 

En règle générale clients utilisent l’interface frontal Engagement Mobile pour créer des annonces/sondages etc. dans le cadre de leurs campagnes marketing. Toutefois que les campagnes marketing devient évolués, il est nécessaire d’exploiter les données verrouillées dans les systèmes de serveur principal (par exemple, un système de CRM ou CMS comme SharePoint) afin qu’un pipeline entièrement automatisé peut être créé qui crée des campagnes lors de l’Engagement Mobile dynamique basé sur les données s’étalant dans des systèmes principal. 

![][5]

Ce didacticiel passe par un scénario où un utilisateur professionnel SharePoint remplit une liste SharePoint avec des données marketing et un processus automatisé capte des éléments dans la liste et se connecte avec le système d’Engagement Mobile à l’aide de l’API REST disponibles pour créer une campagne marketing à partir des données SharePoint. 
 
> [AZURE.IMPORTANT] En règle générale, vous pouvez utiliser cet exemple comme point de départ pour comprendre comment appeler n’importe quelle API Mobile Engagement reste comme il décrit les deux principaux aspects d’appel des API - paramètres d’authentification et que vous passez. 

## <a name="sharepoint-integration"></a>Intégration à SharePoint
1. Voici à quoi ressemble l’exemple de liste SharePoint. **Titre**, la **catégorie**, **NotificationTitle**, **Message** et **URL** sont utilisés pour la création de l’annonce. Il existe une colonne appelée **IsProcessed** qui est utilisé par le processus d’automatisation exemple sous la forme d’un programme de console. Vous pouvez soit exécuter cette console programmer comme un WebJob Azure afin que vous pouvez planifier ou vous pouvez utiliser directement le flux de travail SharePoint au programme création et l’activation de l’annonce lorsqu’un élément est inséré dans la liste SharePoint. Dans cet exemple, nous utilisons le programme de console qui accède à travers les éléments dans la liste SharePoint et créer annonce dans Azure Mobile Engagement pour chacun d’eux et enfin marque ensuite l’indicateur **IsProcessed** pour avoir la valeur true sur la création d’une annonce réussie.

    ![][1]

2. Nous utilisons le code à partir de *l’authentification à distance dans SharePoint Online* à l’aide de modèle d’objet Client [ici](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) pour vous authentifier avec la liste SharePoint.
 
3. Une fois authentifié, nous parcourir les éléments de liste pour déterminer les éléments nouvellement créés (qui aura **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Intégration d’Engagement mobile
1.  Une fois que nous avons trouvé un élément qui nécessite un traitement - nous extraire les informations requises pour créer une annonce à partir de l’élément de liste et l’appel `CreateAzMECampaign` créer votre document et par la suite `ActivateAzMECampaign` pour l’activer. Il s’agit essentiellement les appels API REST appelant pour procéder à la version serveur Mobile Engagement. 

2.  L’API REST de Engagement Mobile requièrent un **en-tête HTTP de base auth jeu d’autorisation** qui se compose de la `ApplicationId` et la `ApiKey` que vous ouvrez à partir du portail Azure. Vérifiez que vous utilisez la clé à partir de la section **api clés** et *non* à partir de la section **sdk clés** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Pour la création de l’annonce type campagne - reportez-vous à la [documentation](https://msdn.microsoft.com/library/azure/mt683750.aspx). Vous devez vous assurer que vous spécifiez la campagne `kind` comme *annonce* et la [charge utile](https://msdn.microsoft.com/library/azure/mt683751.aspx) et en passant en tant que FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Une fois que l’annonce créé, vous verrez quelque chose à ce qui suit dans le portail d’Engagement Mobile (Notez que l’état = Brouillon et activé = n/a)

    ![][3]

5. `CreateAzMECampaign`Crée une campagne d’annonce et retourne son Id à l’appelant. `ActivateAzMECampaign`nécessite cet identifiant en tant que le paramètre pour activer la campagne. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Une fois que l’annonce activé, vous verrez quelque chose à ce qui suit dans le portail d’Engagement Mobile :

    ![][4]

7. Dès que la campagne est activée, les périphériques qui remplissent le critère sur la campagne commencerez à voir les notifications. 

8. Vous remarquerez également que l’élément de liste marqué avec IsProcessed = false a été défini sur True une fois que la campagne annonce est créée.  

Cet exemple créé une campagne simple annonce spécifiant principalement les propriétés requises. Vous pouvez le personnaliser autant que possible à partir du portail en utilisant les informations [ici](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
