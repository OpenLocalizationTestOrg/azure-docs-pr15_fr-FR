<properties
    pageTitle="par programme surveiller les tâches de flux Analytique | Microsoft Azure"
    description="Découvrez comment contrôler par programme les tâches de flux Analytique créées via des API REST, Azure SDK ou Powershell."
    keywords="Moniteur de .net, l’Analyseur de travail, application de surveillance"
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


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Créer par programme un moniteur travail Analytique de flux de données
 Cet article explique comment activer le contrôle d’une tâche de flux de données Analytique. Analytique flux tâches créées à l’aide des API REST, Azure SDK ou Powershell ne pas avoir surveillance activée par défaut.  Vous pouvez activer manuellement ce dans le portail Azure en accédant à la page du Moniteur du travail, en cliquant sur le bouton Activer ou automatiser ce processus en suivant les étapes décrites dans cet article. Les données d’analyse apparaît dans l’onglet « Moniteur » dans le portail Azure pour votre tâche de flux de données Analytique.

![Analyseur de travail onglet tâches](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer cet article, vous devez disposer des éléments suivants :

- Visual Studio 2012 ou 2013.
- Téléchargez et installez le [Kit de développement .NET Azure](https://azure.microsoft.com/downloads/).
- Une tâche de flux Analytique existante nécessitant l’analyse activé.

## <a name="setup-a-project"></a>Configurer un projet

1.  Créer une application console Visual Studio c# .net.
2.  Dans la Console Gestionnaire de Package, exécutez les commandes suivantes pour installer les packages NuGet. Le premier est Azure flux Analytique gestion .NET SDK. Le second est le Kit de développement du moniteur Azure qui sera utilisé pour activer le contrôle. Il est le client Azure Active Directory qui est utilisé pour l’authentification.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Ajoutez la section appSettings suivante au fichier App.config.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Remplacer les valeurs pour *SubscriptionId* et *ActiveDirectoryTenantId* avec votre abonnement Azure et ID du client. Vous pouvez obtenir ces valeurs en exécutant l’applet de commande PowerShell suivante :

    ```
    Get-AzureAccount
    ```
4.  Ajoutez les éléments suivants à l’aide des instructions pour le fichier source (Program.cs) dans le projet.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Ajouter une méthode d’assistance d’authentification.

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

## <a name="create-management-clients"></a>Créer des Clients de gestion
Le code suivant définira les variables nécessaires et les clients de gestion.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Activer le contrôle d’un projet Analytique flux existant

Le code suivant permettra de surveillance pour une tâche de flux de données Analytique **existante** . La première partie du code effectue une requête GET auprès du service de flux Analytique pour récupérer des informations sur la tâche de flux de données Analytique particulier. Il utilise la propriété de « Id » (extraite de la requête GET) en tant que paramètre de la méthode de place dans la deuxième moitié du code qui envoie une place demander au service perspectives pour activer le contrôle pour la tâche de flux de données Analytique.

> [AZURE.WARNING]
> Si vous avez précédemment activé surveillance pour une autre tâche de flux de données Analytique, via le portail Azure ou par programme via la sous code, **il est recommandé de fournir le même nom de compte de stockage que vous avez utilisé lorsque vous avez activé précédemment surveillance.**
>
> Le compte de stockage est lié à la région que vous avez créé votre tâche de flux Analytique dans, ne sont pas spécifiquement pour le travail lui-même.
>
> Tous les Analytique de flux de travail (et toutes les autres ressources Azure) dans cette zone même partagent ce compte de stockage pour stocker les données d’analyse. Si vous fournissez un compte de stockage différents, il peut entraîner des effets inattendus côté pour le suivi de vos autres tâches de flux de données Analytique et/ou d’autres ressources Azure.
>
> Le nom du compte de stockage permet de remplacer ```“<YOUR STORAGE ACCOUNT NAME>”``` ci-dessous doit être un compte de stockage qui se trouve dans le même abonnement comme l’Analytique de flux de travail que vous souhaitez activer surveillance pour.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obtenir une assistance technique
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
