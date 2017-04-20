<properties
    pageTitle="Toujours chiffrées : Protection des données sensibles dans la base de données SQL Azure avec chiffrement de base de données | Microsoft Azure"
    description="Protection des données sensibles dans votre base de données SQL en minutes."
    keywords="chiffrement des données, clé de chiffrement, le chiffrement du cloud"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Toujours chiffrés : Protection des données sensibles dans la base de données SQL et stocker vos clés de chiffrement dans l’archivage sécurisé de clé Azure

> [AZURE.SELECTOR]
- [Archivage sécurisé clé Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Magasin de certificats Windows](sql-database-always-encrypted.md)


Cet article vous explique comment protéger des données sensibles dans une base de données SQL avec chiffrement des données à l’aide de l' [Toujours chiffrées Assistant](https://msdn.microsoft.com/library/mt459280.aspx) dans [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Il inclut également des instructions qui vous montrent comment stocker chaque clé de chiffrement dans l’archivage sécurisé de clé Azure.

Toujours chiffré est une nouvelle technologie de chiffrement de données dans la base de données SQL Azure et SQL Server qui permet de protéger les données sensibles au reste sur le serveur, pendant le mouvement entre client et serveur, et tant que les données sont en cours d’utilisation. Toujours chiffré garantit que des données sensibles s’affiche jamais en texte brut dans le système de base de données. Après avoir configuré le chiffrement des données, uniquement des applications clientes ou des serveurs d’application qui ont accès à l’aide des touches peuvent accéder aux données de texte brut. Pour plus d’informations, voir [Toujours chiffrées (moteur de base de données)](https://msdn.microsoft.com/library/mt163865.aspx).


Après avoir configuré la base de données pour utiliser toujours chiffrées, vous allez créer une application cliente en c# avec Visual Studio pour travailler avec les données chiffrées.

Suivez les étapes décrites dans cet article et découvrez comment configurer toujours chiffrées pour une base de données SQL Azure. Dans cet article, vous allez apprendre à effectuer les tâches suivantes :

- Utilisez l’Assistant toujours chiffrées dans SSMS pour créer des [clés toujours chiffrées](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Créer une [clé de masque des colonnes (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Créer une [clé de chiffrement de colonne (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Créer une table de base de données et chiffrer les colonnes.
- Créer une application qui insère, sélectionne et affiche les données à partir des colonnes chiffrés.


## <a name="prerequisites"></a>Conditions préalables

Pour ce didacticiel, vous devez :

- Un compte Azure et abonnement. Si vous n’en avez pas, inscrivez-vous pour une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) version 13.0.700.242 ou version ultérieure.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou version ultérieure (sur l’ordinateur client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](../powershell-install-configure.md), version 1.0 ou version ultérieure. Type de **(Get-Module azure - ListAvailable). Version** pour voir quelle est la version de PowerShell que vous exécutez.



## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Permettre à votre application cliente accéder au service de base de données SQL

Vous devez activer votre application cliente accéder au service de base de données SQL en configurant l’authentification requise et acquisition de *l’identifiant du client* et le *code Secret* dont vous aurez besoin d’authentifier votre application dans le code suivant.

1. Ouvrez le [portail classique Azure](http://manage.windowsazure.com).
2. Sélectionnez **Active Directory** , puis cliquez sur l’instance Active Directory que votre application utilisera.
3. Cliquez sur **Applications**, puis cliquez sur **Ajouter**.
4. Tapez un nom pour votre application (par exemple : *myClientApp*), sélectionnez **APPLICATION WEB**, puis cliquez sur la flèche pour continuer.
5. Pour **l’URL de session** et **Application ID URI** , vous pouvez taper une URL valide (par exemple, *http://myClientApp*) et continuer.
6. Cliquez sur **configurer**.
7. Copiez votre **ID CLIENT**. (Vous devez cette valeur dans votre code plus tard.)
8. Dans la section **clés** , sélectionnez **1 an** dans la liste déroulante **Sélectionner durée** . (Vous allez copier la clé après avoir enregistré à l’étape 14.)
11. Faites défiler vers le bas et cliquez sur **Ajouter l’application**.
12. Laissez **Afficher** ensemble aux **Applications Microsoft** et sélectionnez **Gestion des services Microsoft Azure**. Cliquez sur la coche en regard de continuer.
13. Sélectionnez **Gestion des services Azure Access** dans la liste déroulante **Autorisations délégués** .
14. Cliquez sur **Enregistrer**.
15. Une fois l’enregistrement terminé, copiez la valeur de clé dans la section **clés** . (Vous devez cette valeur dans votre code plus tard.)



## <a name="create-a-key-vault-to-store-your-keys"></a>Créer un archivage sécurisé clé pour stocker vos clés

À présent que votre application client est configurée et que vous avez votre ID client, il est temps pour créer un archivage sécurisé clé et configurer sa stratégie d’accès pour que vous et votre application puissent accéder secrets de l’archivage sécurisé (les clés toujours chiffrées). Les autorisations *créer*, *obtenir*, de *liste*, *se*, *Vérifier*, *wrapKey*et *unwrapKey* sont requises pour la création d’une nouvelle clé principale de colonne et de configuration de chiffrement avec SQL Server Management Studio.

Vous pouvez créer rapidement un archivage sécurisé clé en exécutant le script suivant. Pour une explication détaillée de ces applets de commande et des informations supplémentaires sur la création et la configuration d’un archivage sécurisé clé, voir [prise en main l’archivage sécurisé de clé Azure](../Key-Vault/key-vault-get-started.md).



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Créer une nouvelle base de données SQL
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Accédez à **Nouveau** > **données + stockage** > **base de données SQL**.
3. Créer une base de données **vide** appelé **clinique** sur un serveur nouveau ou existant. Pour obtenir des instructions détaillées sur la création d’une base de données dans le portail Azure, voir [créer une base de données SQL en minutes](sql-database-get-started.md).

    ![Créer une base de données vide](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Vous avez besoin de la connexion de chaîne plus loin dans le didacticiel, donc après avoir créé la base de données, accédez à la nouvelle base de données stage et copiez la chaîne de connexion. Vous pouvez obtenir la chaîne de connexion à tout moment, mais il est facile de copier dans le portail Azure.

1. Accédez à des **bases de données SQL** > **Stage** > **afficher des chaînes de connexion de base de données**.
2. Copiez la chaîne de connexion pour **ADO.NET**.

    ![Copiez la chaîne de connexion](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Se connecter à la base de données avec SSMS

Ouvrez SSMS et connectez-vous au serveur avec la base de données de stage.


1. Ouvrez SSMS. (Accédez à **se connecter** > **Moteur de base de données** pour ouvrir la fenêtre de **connexion au serveur** si elle n’est pas ouvert.)
2. Entrez votre nom de serveur et les informations d’identification. Vous pouvez trouver le nom du serveur sur la carte de base de données SQL et la chaîne de connexion que vous avez copiée précédemment. Tapez le nom complet du serveur, y compris *database.windows.net*.

    ![Copiez la chaîne de connexion](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Si la fenêtre **Nouvelle règle de pare-feu** s’ouvre, connectez-vous à Azure et laissez SSMS créer une nouvelle règle de pare-feu pour vous.


## <a name="create-a-table"></a>Créer une table

Dans cette section, vous allez créer une table pour stocker les données des patients. Il n’est pas initialement chiffré, vous allez configurer le chiffrement de la section suivante.

1. Développez **bases de données**.
1. Avec le bouton droit de la base de données **Stage** et cliquez sur **Nouvelle requête**.
2. Coller la (T-SQL) Transact-SQL suivante dans la nouvelle fenêtre de requête et **d’exécution** dessus.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Chiffrer les colonnes (configurer toujours chiffrées)

SSMS fournit un Assistant qui vous permet de configurer facilement toujours chiffrées en définissant la clé de masque des colonnes, clé de chiffrement de colonne et colonnes chiffrés pour vous.

1. Développez **bases de données** > **Stage** > **Tables**.
2. Avec le bouton droit de la table **Patients** et sélectionnez **Chiffrer les colonnes** pour ouvrir l’Assistant toujours chiffrées :

    ![Chiffrer les colonnes](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

L’Assistant toujours chiffrées inclut les sections suivantes : **Sélection de la colonne**, la **Configuration de la clé principale**, **Validation**et **Résumé**.

### <a name="column-selection"></a>Sélection d’une colonne##

Cliquez sur **suivant** dans la page **d’Introduction** pour ouvrir la page de **Sélection de la colonne** . Dans cette page, vous allez sélectionner les colonnes que vous voulez chiffrer, [le type de chiffrement et quelle clé de chiffrement de colonne (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) à utiliser.

Chiffrer les informations de **numéro de sécurité sociale** et **date de naissance** pour les patients. La colonne numéro de sécurité sociale utilise le chiffrement déterministe, qui prend en charge l’égalité des recherches, des jointures et regrouper par. La colonne date de naissance utilise le chiffrement aléatoire, qui ne prend pas en charge les opérations.

Définir le **Type de chiffrement** pour la colonne numéro de sécurité sociale **Deterministic** et la colonne date de naissance à **Randomized**. Cliquez sur **suivant**.

![Chiffrer les colonnes](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuration de la clé principale###

La page **Configuration de la clé principale** est l’endroit où vous avez configuré votre CMK et sélectionnez le fournisseur de magasin de clés où la CMK sera stocké. Pour l’instant, vous pouvez stocker un CMK dans le magasin de certificats Windows, l’archivage sécurisé de clé Azure ou un module de sécurité matériel (HSM).

Ce didacticiel montre comment stocker vos clés dans l’archivage sécurisé de clé Azure.

1.     Sélectionnez **l’archivage sécurisé clé Azure**.
1.     Sélectionnez l’archivage sécurisé clé souhaité dans la liste déroulante.
1.     Cliquez sur **suivant**.

![Configuration de la clé principale](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### <a name="validation"></a>Validation###

Vous pouvez chiffrer les colonnes maintenant ou enregistrer un script PowerShell pour exécuter ultérieurement. Pour ce didacticiel, sélectionnez **Continuer pour terminer maintenant** , puis cliquez sur **suivant**.

### <a name="summary"></a>Résumé ###

Vérifiez que les paramètres sont tous les corriger et cliquez sur **Terminer** pour terminer l’installation de toujours chiffrées.


![Résumé](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### <a name="verify-the-wizards-actions"></a>Vérifiez les actions de l’Assistant

Une fois l’Assistant terminé, votre base de données a été configurée pour toujours chiffrées. L’Assistant effectuer les actions suivantes :

- Créé une clé de masque des colonnes et stockées dans l’archivage sécurisé de clé Azure.
- Créé une clé de chiffrement de colonne et stocké dans l’archivage sécurisé de clé Azure.
- Configurer les colonnes sélectionnées pour le chiffrement. La table Patients ne propose actuellement aucune donnée, mais les données existantes dans les colonnes sélectionnées sont désormais chiffrées.

Vous pouvez vérifier la création des clés dans SSMS en développement **Stage** > **sécurité** > **Toujours chiffrées clés**.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Créer une application cliente qui fonctionne avec les données chiffrées

À présent que toujours chiffrées est configurée, vous pouvez créer une application qui exécute *insère* et *sélectionne* sur les colonnes chiffrés.  

> [AZURE.IMPORTANT] Votre application doit utiliser des objets [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) lors de la transmission des données en texte brut au serveur avec colonnes toujours chiffrées. Transmission des valeurs littérales sans l’utilisation d’objets SqlParameter provoquera une exception.

1. Ouvrez Visual Studio et créez une nouvelle application console c#. Vérifiez que votre projet est défini à **.NET Framework 4.6** ou version ultérieure.
2. Nommez le projet **AlwaysEncryptedConsoleAKVApp** et cliquez sur **OK**.
![Nouvelle application de console](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. Installer les packages NuGet suivants en accédant à **Outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de Package**.

Exécuter ces deux lignes de code dans la Console Gestionnaire de Package.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifier votre chaîne de connexion pour activer toujours chiffrées

Cette section explique comment activer toujours chiffrées dans votre chaîne de connexion de base de données.


Pour activer toujours chiffrées, vous devez ajouter le **Paramètre de chiffrement de colonne** mot-clé à votre chaîne de connexion et défini sur **activé**.

Vous pouvez définir directement dans la chaîne de connexion, ou vous pouvez le configurer à l’aide de [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). L’exemple d’application dans la section suivante montre comment utiliser **SqlConnectionStringBuilder**.



### <a name="enable-always-encrypted-in-the-connection-string"></a>Activer toujours chiffrées dans la chaîne de connexion

Ajoutez le mot clé suivant à votre chaîne de connexion.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Activer toujours chiffré avec SqlConnectionStringBuilder

Le code suivant montre comment activer toujours chiffrées en définissant [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) sur [activé](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Enregistrer le fournisseur de l’archivage sécurisé de clé Azure

Le code suivant montre comment enregistrer le fournisseur de l’archivage sécurisé de clé Azure avec le pilote ADO.NET.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Toujours chiffré exemple console d’application

Cet exemple montre comment :

- Modifier votre chaîne de connexion pour activer toujours chiffrées.
- Enregistrer l’archivage sécurisé de clé Azure en tant que fournisseur de magasin de clés de l’application.  
- Insérer des données dans les colonnes chiffrés.
- Sélection d’un enregistrement en filtrant une valeur spécifique dans une colonne chiffrée.

Remplacez le contenu de **Program.cs** par le code suivant. Remplacez la chaîne de connexion pour la variable connectionString général dans la ligne qui précède directement la méthode Main avec votre chaîne de connexion valide à partir du portail Azure. Il s’agit de la seule modification que vous souhaitez apporter à ce code.

Exécutez l’application pour voir toujours chiffrées en action.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Vérifiez que les données sont chiffrées

Vous pouvez rapidement vérifier que les données réelles sur le serveur sont chiffrées par interroger les données Patients avec SSMS (à l’aide de votre connexion actuelle où **Paramètre de chiffrement de colonne** n’est pas encore activé).

Exécuter la requête suivante sur la base de données de stage.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Vous pouvez voir que les colonnes chiffrés ne contiennent pas toutes les données en texte brut.

   ![Nouvelle application de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Pour utiliser SSMS pour accéder aux données en texte brut, vous pouvez ajouter la *paramètre de chiffrement de colonne = activé* paramètre à la connexion.

1. Dans SSMS, avec le bouton droit de votre serveur dans **l’Explorateur d’objets** , puis sélectionnez **se déconnecter**.
2. Cliquez sur **se connecter** > **Moteur de base de données** pour ouvrir la fenêtre de **connexion au serveur** et cliquez sur **Options**.
3. Cliquez sur **Paramètres de connexion supplémentaires** et tapez **paramètre de chiffrement de colonne = activé**.

    ![Nouvelle application de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Exécuter la requête suivante sur la base de données de stage.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Vous pouvez désormais voir les données en texte brut dans les colonnes chiffrés.


    ![Nouvelle application de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Étapes suivantes
Après avoir créé une base de données qui utilise toujours chiffrées, vous souhaiterez peut-être effectuer les opérations suivantes :

- [Faire pivoter et nettoyer vos clés](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrer des données qui sont déjà chiffrées avec toujours chiffrées](https://msdn.microsoft.com/library/mt621539.aspx).


## <a name="related-information"></a>Informations connexes

- [Toujours chiffrées (développement client)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Chiffrement de données transparent](https://msdn.microsoft.com/library/bb934049.aspx)
- [Chiffrement de SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Toujours chiffré Assistant](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog toujours chiffré](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
