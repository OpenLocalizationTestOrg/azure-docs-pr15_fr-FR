<properties
   pageTitle="Utiliser des données Lake Analytique Java SDK pour développer des applications | Azure"
   description="Utiliser Azure données Lake Analytique Java SDK pour développer des applications"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-java-sdk"></a>Didacticiel : Prise en main Azure données Lake Analytique à l’aide du SDK Java

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser le SDK Azure Data Lake Analytique Java pour créer un compte Azure données Lake et effectuer des opérations de base telles que créez des dossiers, charger et téléchargement des fichiers de données, supprimer votre compte et utilisation des tâches. Pour plus d’informations sur les données Lake, voir [Azure données Lake Analytique](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer une application de console Java qui contient des exemples de tâches administratives ainsi que de création des données de test et de l’envoi d’une tâche.  Pour ouvrir le même didacticiel à l’aide d’autres outils pris en charge, cliquez sur les onglets situés en haut de cette section.

## <a name="prerequisites"></a>Conditions préalables

* Kit de développement de Java (JDK) 8 (à l’aide de Java version 1.8).
* IntelliJ ou un autre environnement de développement Java approprié. Ceci est facultative, mais recommandée. Les instructions ci-dessous utilisent IntelliJ.
* **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* **Activer votre abonnement Azure** pour la présentation publique de données Lake Analytique. Consultez les [instructions](data-lake-analytics-get-started-portal.md#signup).
* Créer une application Azure Active Directory (DAS) et de récupérer ses **ID Client**, **l’ID de client**et **clé**. Pour plus d’informations sur les applications AAD et des instructions sur l’obtention d’un ID de client, voir [créer un Active Directory application et service principal à l’aide du portail](../resource-group-create-service-principal-portal.md). La réponse URI et la clé seront également disponibles à partir du portail une fois que l’application créée et clé générée.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?

L’extrait de code ci-dessous fournit un code pour **non interactif** authentification, où l’application fournit ses propres informations d’identification.

Vous avez besoin donner à votre application l’autorisation de créer des ressources dans Azure pour ce didacticiel pour l’utiliser. Il est **vivement recommandé** que vous uniquement autoriser cette application collaborateur à un groupe de ressources nouvelle et inutilisée vide dans votre abonnement Azure dans le cadre de ce didacticiel.

## <a name="create-a-java-application"></a>Créer une application Java

1. Ouvrez IntelliJ et créer un projet Java à l’aide du modèle **d’Application de ligne de commande** .

2. Avec le bouton droit sur le projet sur le côté gauche de votre écran, cliquez sur **Ajouter la prise en charge Framework**. Choisissez **Maven** et cliquez sur **OK**.

3. Ouvrez le fichier nouvellement créé **« pom.xml »** et ajouter l’extrait suivant du texte entre la ** \</version >** balise et la ** \</project >** balise :

    Remarque : Cette étape est temporaire jusqu'à ce que le SDK Azure Data Lake Analytique n’est disponible dans Maven. Cet article est mise à jour une fois que le Kit de développement est disponible dans Maven. Toutes les mises à jour ultérieures ce SDK sera disponible via Maven.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-20160513.000802-24</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-runtime</artifactId>
                <version>1.0.0-20160513.000812-28</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.rest</groupId>
                <artifactId>client-runtime</artifactId>
                <version>1.0.0-20160513.000825-29</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-analytics</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Accédez au **fichier**, puis sur **paramètres**, puis sur **déploiement**de **créer**, **l’exécution**. Sélectionnez **Outils de génération**, **Maven**, **l’importation**. Vérifiez ensuite **Maven importer des projets automatiquement**.

5. Ouvrez **Main.java** et remplacez le bloc de code existant par le code suivant. En outre, indiquez les valeurs de paramètres entourées dans l’extrait de code, telles que **localFolderPath**, **_adlaAccountName**et **_adlsAccountName**, **_resourceGroupName** et remplacer des espaces réservés pour **ID CLIENT**, **SECRET CLIENT**, **ID de client**et **ID de l’abonnement**.

    Ce code effectue le processus de création de comptes Data Lake Store et données Lake Analytique, création de fichiers dans le magasin, exécution d’une tâche, l’obtention de statut de la tâche, le téléchargement de sortie des tâches et pour terminer la suppression du compte.

    >[AZURE.NOTE] Il existe actuellement un problème connu avec le Service de Lake données Azure.  Si l’application exemple est interrompue ou rencontre une erreur, vous devrez peut-être supprimer manuellement les comptes données Lake Store et données Lake Analytique qui crée le script.  Si vous ne connaissez pas le portail, le guide de [Gestion des Analytique des Lake Azure données à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md) obtiendrez mise en route.


        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.analytics.*;
        import com.microsoft.azure.management.datalake.analytics.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.nio.file.Files;
        import java.nio.file.Paths;
        import java.util.ArrayList;
        import java.util.UUID;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _adlaAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store and Analytics accounts
                WaitForNewline("Authenticated.", "Creating NEW accounts.");
                CreateAccounts();
                WaitForNewline("Accounts created.", "Displaying accounts.");
        
                // List Data Lake Store and Analytics accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
                List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
                for (DataLakeAnalyticsAccount acct : adlaListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Accounts displayed.", "Creating files.");
        
                // Create a file in Data Lake Store: input1.csv
                // TODO: these change order in the next patch
                byte[] bytesContents = "123,abc".getBytes();
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, "/input1.csv", bytesContents, true);
        
                WaitForNewline("File created.", "Submitting a job.");
        
                // Submit a job to Data Lake Analytics
                UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();", "testJob");
                WaitForNewline("Job submitted.", "Getting job status.");
        
                // Wait for job completion and output job status
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                System.out.println("Waiting for job completion.");
                WaitForJob(jobId);
                System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
                WaitForNewline("Job completed.", "Downloading job output.");
        
                // Download job output from Data Lake Store
                DownloadFile("/output1.csv", localFolderPath + "output1.csv");
                WaitForNewline("Job output downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/output1.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
                _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
                _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
                _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
                _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
                _adlsClient.setSubscriptionId(_subId);
                _adlaClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
        
                System.out.println(reason + "\r\nPress ENTER to continue...");
                try{System.in.read();}
                catch(Exception e){}
        
                if (!nextAction.isEmpty())
                {
                    System.out.println(nextAction);
                }
            }
        
            // Create Data Lake Store and Analytics accounts
            public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
                // Create ADLS account
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
        
                // Create ADLA account
                DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
                adlsInfo.setName(_adlsAccountName);
        
                DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
                adlsInfo.setProperties(adlsInfoProperties);
        
                List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
                adlsInfoList.add(adlsInfo);
        
                DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
                adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
                adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
        
                DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
                adlaParameters.setLocation(_location);
                adlaParameters.setName(_adlaAccountName);
                adlaParameters.setProperties(adlaProperties);
        
                    /* If this line generates an error message like "The deep update for property 'DataLakeStoreAccounts' is not supported", please delete the ADLS and ADLA accounts via the portal and re-run your script. */
        
                _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
            }
        
            //todo: this changes in the next version of the API
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Submit a U-SQL job by providing script contents.
            // Returns the job ID
            public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
                UUID jobId = java.util.UUID.randomUUID();
                USqlJobProperties properties = new USqlJobProperties();
                properties.setScript(script);
                JobInformation parameters = new JobInformation();
                parameters.setName(jobName);
                parameters.setJobId(jobId);
                parameters.setType(JobType.USQL);
                parameters.setProperties(properties);
        
                JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
        
                return jobId;
            }
        
            // Wait for job completion
            public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                while (jobInfo.getState() != JobState.ENDED)
                {
                    jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
                }
                return jobInfo.getResult();
            }
        
            // Get job status
            public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
                JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
                return jobInfo.getState().toValue();
            }
        }

6. Suivez les invites pour exécuter et procéder à l’application.


## <a name="see-also"></a>Voir aussi

- Pour afficher le même didacticiel à l’aide d’autres outils, cliquez sur les sélecteurs onglet en haut de la page.
- Pour plus d’une requête plus complexe, voir [journaux d’analyse Web à l’aide de Azure données Lake Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, voir [scripts U-SQL développer à l’aide de données Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’U-SQL, voir [prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md)et [référence du langage SQL-U](http://go.microsoft.com/fwlink/?LinkId=691348).
- Pour les tâches de gestion, voir [Gérer des Analytique des Lake Azure données à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).
