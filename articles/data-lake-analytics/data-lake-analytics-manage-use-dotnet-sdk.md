<properties 
   pageTitle="Gérer les Azure données Lake Analytique à l’aide du Kit de développement .NET Azure | Azure" 
   description="Découvrez comment gérer les travaux de données Lake Analytique, les sources de données, les utilisateurs. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gérer les Azure données Lake Analytique à l’aide du Kit de développement .NET Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Découvrez comment gérer les comptes Azure données Lake Analytique, des sources de données, des utilisateurs et des travaux en utilisant le Kit de développement .NET Azure. Pour afficher les rubriques gestion à l’aide d’autres outils, cliquez sur l’onglet, sélectionnez ci-dessus.

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Se connecter à des données Azure Lake Analytique

Vous devez les packages Nuget suivants :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


L’exemple de code suivant vous montre comment se connecter à Azure et répertorier les comptes données Lake Analytique existantes sous votre abonnement Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gérer les comptes

Avant l’exécution de toutes les tâches données Lake Analytique, vous devez disposer d’un compte de données Lake Analytique. Contrairement à Azure HDInsight, vous ne pas payer pour un compte Analytique lorsqu’elle s’exécute pas une tâche.  Vous payez uniquement l’heure lorsqu’il s’exécute une tâche.  Pour plus d’informations, voir [Vue d’ensemble du Analytique Lake données Azure](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Créer des comptes

Avant de pouvoir exécuter l’exemple suivant, vous devez disposer un groupe de gestion des ressources Azure et d’un compte de données Lake Store.

Le code suivant montre comment créer un groupe de ressources :

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Le code suivant montre comment créer un compte Data Lake Store :

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Le code suivant montre comment créer un compte de données Lake Analytique :

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Liste des comptes

Voir [se connecter à des données Azure Lake Analytique](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Rechercher un compte

Une fois que vous obtenez un objet d’une liste de comptes données Lake Analytique, vous pouvez utiliser ce qui suit pour rechercher un compte :

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Supprimer des comptes de données Lake Analytique

L’extrait de code suivant supprime un compte de données Lake Analytique :

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gérer les sources de données de compte

Données Lake Analytique prend actuellement en charge les sources de données suivantes :

- [Stockage des Lake données Azure](../data-lake-store/data-lake-store-overview.md)
- [Stockage Azure](../storage/storage-introduction.md)

Lorsque vous créez un compte Analytique, vous devez désigner un compte de stockage Lake des données Azure le compte de stockage par défaut. Le compte par défaut données Lake permet de stocker le travail métadonnées et tâche les journaux d’audit. Après avoir créé un compte Analytique, vous pouvez ajouter des comptes supplémentaires de stockage des données Lake et/ou compte de stockage Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Trouver le compte de données Lake Store par défaut

Voir rechercher un compte dans cet article pour trouver le compte données Lake Analytique. Utilisez la commande suivante :

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Utiliser des groupes de gestionnaire de ressources Azure

Applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et 3e partie services. Le Gestionnaire de ressources Azure permet de vous permettent de travailler avec les ressources dans votre application en tant que groupe, appelée un groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources pour votre application dans une seule opération coordonnée. Vous utilisez un modèle pour le déploiement et ce modèle peut utiliser pour les différents environnements telles que le test, intermédiaire et production. Vous pouvez clarifier facturation pour votre organisation en affichant les coûts reportées pour l’ensemble du groupe. Pour plus d’informations, voir [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). 

Un service de données Lake Analytique peut inclure les éléments suivants :

- Compte d’Analytique Lake de données Azure
- Compte de stockage Lake des données Azure requis par défaut
- Comptes de stockage supplémentaire Azure données Lake
- Autres comptes de stockage Azure

Vous pouvez créer tous ces composants dans un groupe de gestion des ressources pour les rendre plus facile à gérer.

![Stockage et compte Analytique de Lake de données azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte de données Lake Analytique et les comptes de stockage dépendant doivent être placés dans le centre de données Azure même.
Le groupe de gestion des ressources peut toutefois résider dans un centre de données différents.  

##<a name="see-also"></a>Voir aussi 

- [Vue d’ensemble de Microsoft Azure données Lake Analytique](data-lake-analytics-overview.md)
- [Prise en main données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Gérer les Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
- [Surveiller et résoudre les problèmes de travaux Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

