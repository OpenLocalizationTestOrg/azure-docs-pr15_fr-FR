<properties
    pageTitle="Gestion des SDK .NET pour flux Analytique | Microsoft Azure"
    description="Prise en main flux Analytique gestion .NET SDK. Découvrez comment configurer et exécuter des travaux analytique : créer un projet, les entrées, sorties et transformations."
    keywords=".NET SDK, analytique API"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Gestion du Kit de développement .NET : Configurer et exécuter des travaux analytique à l’aide de l’API Azure flux Analytique pour .NET

Découvrez comment configurer un tâches exécution analytique à l’aide de l’API Analytique flux pour .NET en utilisant le Kit de développement .NET gestion. Configurer un projet, créer des sources d’entrée et de sortie, transformations et démarrer et arrêter les tâches. Pour vos travaux analytique, vous pouvez diffuser des données depuis le stockage Blob ou d’un concentrateur de l’événement.

Consultez la [documentation de référence de gestion de l’API Analytique flux pour .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Analytique de flux de données est un service entièrement géré qui offre un traitement de l’événement faible latence, hautement disponible, format SVG, complexes sur diffusion en continu de données dans le cloud. Flux Analytique permet aux utilisateurs de définir la diffusion en continu de tâches pour analyser des flux de données et leur lecteur près analytique en temps réel permet.  


## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

- Installez Visual Studio 2012 ou 2013.
- Téléchargez et installez le [Kit de développement .NET Azure](https://azure.microsoft.com/downloads/).
- Créer un groupe de ressources Azure dans votre abonnement. Voici un exemple de script PowerShell Azure. Pour plus d’informations Azure PowerShell, voir [installer et configurer Azure PowerShell](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   Définissez une source d’entrée et la cible de sortie à utiliser. Pour plus d’informations instructions voir [Ajouter des entrées](stream-analytics-add-inputs.md) pour configurer un exemple d’entrée et [Ajouter des sorties](stream-analytics-add-outputs.md) pour configurer un exemple de sortie.


## <a name="set-up-a-project"></a>Configurer un projet

Pour créer une tâche analytique utilisez l’API Analytique flux pour .NET, configurez d’abord votre projet.

1. Créer une application de console Visual Studio c# .NET.
2. Dans la Console Gestionnaire de Package, exécutez les commandes suivantes pour installer les packages NuGet. Le premier est Azure flux Analytique gestion .NET SDK. Le second est le client Azure Active Directory qui est utilisé pour l’authentification.

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Ajoutez la section **appSettings** suivante au fichier App.config :

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    Remplacer les valeurs pour **SubscriptionId** et **ActiveDirectoryTenantId** avec votre abonnement Azure et ID du client. Vous pouvez obtenir ces valeurs en exécutant l’applet de commande PowerShell Azure suivant :

        Get-AzureAccount

5. Ajoutez les instructions suivantes **à l’aide** pour le fichier source (Program.cs) dans le projet :

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  Ajouter une méthode d’assistance d’authentification :

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
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


## <a name="create-a-stream-analytics-management-client"></a>Création d’un client de gestion des flux de données Analytique

Un objet **StreamAnalyticsManagementClient** permet de gérer le travail et les composants de travail, tels que transformation d’entrée et sortie.

Ajoutez le code suivant au début de la méthode **Main** :

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Valeur de la variable **resourceGroupName** doit être identique au nom du groupe de ressources, vous avez créé ou choisi dans ces étapes requises.

Pour automatiser l’aspect de présentation d’informations d’identification de la création d’une tâche, reportez-vous à [l’authentification un principal de service avec le Gestionnaire de ressources Azure](../resource-group-authenticate-service-principal.md).

Les sections restantes de cet article part du principe que ce code est au début de la méthode **Main** .

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

Le code suivant crée une tâche de flux de données Analytique sous le groupe de ressources que vous avez définis. Vous allez ajouter une entrée, de sortie et transformation à la tâche plus tard.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Créer une source d’entrée Analytique de flux de données

Le code suivant crée une source d’entrée Analytique de flux avec le type de source d’entrée blob et sérialisation CSV. Pour créer une source d’entrée concentrateur d’événements, utilisez **EventHubStreamInputDataSource** plutôt que **BlobStreamInputDataSource**. De même, vous pouvez personnaliser le type de numérotation de la source d’entrée.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Sources d’entrée, de stockage d’objets Blob ou un concentrateur événement, sont liés à une tâche spécifique. Pour utiliser la même source d’entrée pour différentes tâches, vous devez rappeler la méthode et spécifiez un nom de travail différent.


## <a name="test-a-stream-analytics-input-source"></a>Tester une source d’entrée Analytique de flux de données

La méthode **TestConnection** vérifie si la tâche de flux de données Analytique est en mesure de vous connecter à la source d’entrée, ainsi que d’autres aspects spécifiques au type de source d’entrée. Par exemple, dans la source d’entrée blob que vous avez créé dans une étape précédente, la méthode vérifie que le nom de compte de stockage et paire de clés peut servir à se connecter pour le compte de stockage ainsi que pour vérifier que le conteneur spécifié existe.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Créer une cible de sortie Analytique de flux de données

Créer une cible de sortie est très similaire à la création d’une source d’entrée Analytique de flux de données. Comme sources d’entrée, sortie cibles sont liés à une tâche spécifique. Pour utiliser la même cible de sortie pour différentes tâches, vous devez rappeler la méthode et spécifiez un nom de travail différent.

Le code suivant crée une cible de sortie (base de données SQL Azure). Vous pouvez personnaliser le type de données de la cible de sortie et/ou le type de numérotation.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Tester une cible de la sortie du flux Analytique

Flux Analytique sortie cible est également la méthode **TestConnection** pour tester les connexions.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Créer une transformation Analytique de flux de données

Le code suivant crée une transformation Analytique de flux de données avec la requête « sélectionnez * d’entrée » et spécifie pour allouer une unité de diffusion en continu pour la tâche de flux de données Analytique. Pour plus d’informations sur l’ajustement des unités de diffusion en continu, voir [tâches échelle Azure flux Analytique](stream-analytics-scale-jobs.md).


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Comme entrée et sortie, un transformations est également liée à la tâche de flux Analytique spécifique, il a été créé sous.

## <a name="start-a-stream-analytics-job"></a>Démarrage d’une tâche de flux de données Analytique
Après avoir créé une tâche de flux de données Analytique et ses niveaux, sorties et de transformation, vous pouvez commencer le travail en appelant la méthode **Start** .

L’exemple suivant code commence une tâche de flux Analytique avec une heure de début de sortie personnalisée la valeur 12 décembre 2012, 12:12:12 UTC :

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>Arrêter une tâche de flux de données Analytique
Vous pouvez arrêter une tâche de flux de données Analytique en cours d’exécution en appelant la méthode **Arrêter** .

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Supprimer une tâche de flux de données Analytique
La méthode **Delete** supprime le travail ainsi que les ressources sous-adresse sous-jacentes, y compris les niveaux, sorties et la transformation de la tâche.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

Vous avez les concepts de base de l’utilisation d’un kit de développement .NET pour créer et exécuter des travaux analytique. Pour plus d’informations, voir les rubriques suivantes :

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Kit de développement logiciel gestion des flux de données azure Analytique .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
