<properties 
    pageTitle="Didacticiel : Créer un pipeline avec une activité copie à l’aide de l’API .NET | Microsoft Azure" 
    description="Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API .NET." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Didacticiel : Créer un pipeline avec une activité copie à l’aide de l’API .NET
> [AZURE.SELECTOR]
- [Vue d’ensemble et conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Ce didacticiel montre comment créer et surveiller une usine de données Azure à l’aide de l’API .NET. Le pipeline en usine données utilise une activité copier pour copier des données depuis le stockage Blob Azure dans la base de données SQL Azure.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. L’activité est optimisée par un service globalement disponible qui peut copier des données entre différentes banques de données de façon sécurisée, fiable et format SVG. Consultez l’article des [Activités de déplacement des données](data-factory-data-movement-activities.md) pour plus d’informations sur l’activité de copie.   

> [AZURE.NOTE] 
> Cet article n’aborde pas toutes les API .NET usine les données. Pour plus d’informations sur les données usine .NET SDK, voir [Référence de l’API .NET Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) . 

## <a name="prerequisites"></a>Conditions préalables
- Parcourez [didacticiel vue d’ensemble et les conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir une vue d’ensemble du didacticiel et effectuez les étapes **composant requis** . 
- Visual Studio 2012 ou 2013 ou 2015
- Téléchargez et installez le [Kit de développement .NET Azure](http://azure.microsoft.com/downloads/)
- PowerShell Azure. Suivez les instructions de l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour installer PowerShell Azure sur votre ordinateur. Azure PowerShell vous permet de créer une application Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Créer une application dans Azure Active Directory
Créer une application Azure Active Directory, créer une entité de service pour l’application et attribuer le rôle de **Collaborateur Factory** .  

1. Lancez **PowerShell**. 
1. Exécutez la commande suivante, puis entrez le nom d’utilisateur et mot de passe que vous utilisez pour vous connecter au portail Azure.
    
        Login-AzureRmAccount   
2. Exécutez la commande suivante pour afficher tous les abonnements pour ce compte.

        Get-AzureRmSubscription 
3. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacer ** &lt;NameOfAzureSubscription** &gt; avec le nom de votre abonnement Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Remarque vers le bas **SubscriptionId** et **TenantId** à partir du résultat de cette commande. 
4. Créer un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans la session PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Si le groupe de ressources existe déjà, vous spécifiez si vous souhaitez mettre à jour (Y) ou conserver comme (N). 

    Si vous utilisez un groupe de ressources différent, vous devez utiliser le nom de votre groupe de ressources à la place ADFTutorialResourceGroup dans ce didacticiel.
5. Créer une application Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Si vous obtenez l’erreur suivante, spécifier une autre URL et exécutez la commande Nouveau. 

        Another object with the same value for property identifierUris already exists.

6. Créer l’annonce principal du service. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Ajouter principal du service pour le rôle de **Collaborateur d’usine de données** . 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Obtenir l’ID d’application.

        $azureAdApplication

    Notez l’ID de l’application (**applicationID** à partir du résultat).

Vous devez avoir suivi les quatre valeurs à partir de ces étapes : 

- ID de client
- ID de l’abonnement
- ID de l’application 
- Mot de passe (spécifié dans la première commande)   

## <a name="walkthrough"></a>Procédure pas à pas
1. À l’aide de Visual Studio 2012/2013/2015, créez une application console c# .NET.
    1. Lancez **Visual Studio** 2012/2013/2015.
    2. Cliquez sur **fichier**, pointez sur **Nouveau**, puis cliquez sur **projet**.
    3. Développez **modèles**, puis sélectionnez **Visual c#**. Dans cette procédure pas à pas, vous utilisez c#, mais vous pouvez utiliser n’importe quel langage .NET.
    4. Sélectionnez **Application Console** dans la liste des types de projets à droite.
    5. Entrez **DataFactoryAPITestApp** pour le nom.
    6. Sélectionnez **C:\ADFGetStarted** à l’emplacement.
    7. Cliquez sur **OK** pour créer le projet.
2. Cliquez sur **Outils**, pointez sur **Gestionnaire de Package Nuget**, puis cliquez sur **Gestionnaire de Package Console**.
3.  Dans la **Console du Gestionnaire de Package**, procédez comme suit : 
    1.  Exécutez la commande suivante pour installer le package d’usine de données :`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Exécutez la commande suivante pour installer le package Azure Active Directory (vous utilisez Active Directory API dans le code) :`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Ajoutez la section suivante **appSetttings** au fichier **App.config** . Ces paramètres sont utilisés par la méthode d’assistance : **GetAuthorizationHeader**. 

    Remplacer les valeurs pour ** &lt;ID de l’Application&gt;**, ** &lt;mot de passe&gt;**, ** &lt;ID de l’abonnement&gt;**, et ** &lt;ID du client&gt; ** avec vos propres valeurs. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Ajoutez les instructions suivantes **à l’aide** pour le fichier source (Program.cs) dans le projet.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Ajoutez le code suivant qui crée une instance de classe **DataPipelineManagementClient** à la méthode **Main** . Cet objet vous permet de créer une usine de données, un service lié, jeux de données d’entrée et de sortie et un pipeline. Cet objet permet également de surveiller les sections d’un dataset en cours d’exécution.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Remplacez la valeur de **resourceGroupName** par le nom de votre groupe de ressources Azure. 
    > 
    > Mettre à jour de nom de la factory de données (**dataFactoryName**) unique. Nom de la factory de données doit être globalement unique. Consultez la rubrique de [Données Factory - règles d’appellation](data-factory-naming-rules.md) pour les règles d’appellation pour les objets de données par défaut. 

7. Ajoutez le code suivant qui crée une **usine de données** à la méthode **Main** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Ajoutez le code suivant qui crée un **stockage Azure liées service** à la méthode **Main** . 

    > [AZURE.IMPORTANT] Remplacez **storageaccountname** et **accountkey** par nom et la clé de votre compte de stockage Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Ajoutez le code suivant qui crée une **SQL Azure lié service** à la méthode **Main** .
 
    > [AZURE.IMPORTANT] Remplacez le **nom du serveur**, **databasename**, **nom d’utilisateur**et **mot de passe** dont le nom de votre serveur SQL Azure, base de données, utilisateur et mot de passe.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Ajoutez le code suivant qui crée **des groupes de données d’entrée et de sortie** à la méthode **Main** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Ajoutez le code suivant qui **crée et Active un pipeline** à la méthode **Main** . Ce pipeline a un **CopyActivity** qui vous permet d’accéder **BlobSource** en tant que source et **BlobSink** comme un récepteur.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Ajouter le code suivant à la méthode **Main** pour obtenir le statut d’un secteur de données du jeu de données de sortie. Il est uniquement secteur prévu dans cet exemple.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Ajoutez le code suivant pour obtenir des détails de l’exécution d’un secteur de données à la méthode **Main** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Ajoutez la méthode d’assistance suivante utilisée par la méthode **Main** à la classe **Program** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


15. Dans l’Explorateur de solutions, développez le projet (**DataFactoryAPITestApp**), cliquez sur **références**, puis cliquez sur **Ajouter une référence**. Case à cocher pour assembly «**System.Configuration**», puis cliquez sur **OK**. 
16. Créer l’application console. Cliquez sur **créer** dans le menu, puis cliquez sur **Générer la Solution**. 
16. Vérifiez qu’est au moins un fichier dans le conteneur **adftutorial** dans votre espace de stockage blob Azure. Dans le cas contraire, créez le fichier **Emp.txt** dans le bloc-notes avec le contenu suivant et téléchargez-le sur le conteneur adftutorial.

        John, Doe
        Jane, Doe
     
17. Exécuter l’exemple en cliquant sur **Déboguer** -> **Démarrer le débogage** dans le menu. Lorsque vous voyez les **détails d’une section de données l’obtention de l’exécution**, patientez quelques minutes, puis appuyez sur **entrée**. 
18. Utiliser le portail Azure pour vérifier que le factory de données **APITutorialFactory** est créé avec les objets suivants : 
    - Lié service : **LinkedService_AzureStorage** 
    - Jeu de données : **DatasetBlobSource** et **DatasetBlobDestination**.
    - Pipeline : **PipelineBlobSample** 
18. Vérifiez que les deux enregistrements sont créés dans la table «**emp**» dans la base de données SQL Azure spécifié.

## <a name="next-steps"></a>Étapes suivantes

- Lire l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) , qui fournit des informations détaillées sur l’activité de copie que vous avez utilisées dans le didacticiel.
- Pour plus d’informations sur les données usine .NET SDK, voir [Référence de l’API .NET Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) . Cet article n’aborde pas toutes les API .NET usine les données. 

 
