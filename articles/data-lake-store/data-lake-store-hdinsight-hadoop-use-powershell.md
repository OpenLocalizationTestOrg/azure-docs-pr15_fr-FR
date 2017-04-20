<properties
   pageTitle="Créer des clusters HDInsight avec Azure données Lake Store à l’aide de PowerShell | Azure"
   description="Utiliser PowerShell Azure pour créer et utiliser des clusters HDInsight avec Lake de données Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Créer un cluster HDInsight avec données Lake Store à l’aide de PowerShell Azure

> [AZURE.SELECTOR]
- [À l’aide du portail](data-lake-store-hdinsight-hadoop-use-portal.md)
- [À l’aide de PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [À l’aide du Gestionnaire de ressources](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Découvrez comment utiliser PowerShell Azure pour configurer un cluster HDInsight (Hadoop, HBase ou vague de) avec accès au magasin Lake des données Azure. Certaines considérations importantes pour cette version :

* **Explosion pour clusters (Linux) et clusters/vague d’Hadoop (Windows et Linux)**, le magasin de Lake données uniquement utilisable comme un compte de stockage supplémentaire. Le compte de stockage par défaut pour les groupes ces seront toujours Azure stockage BLOB (WASB).

* **Pour HBase clusters (Windows et Linux)**, le magasin de Lake données peut être utilisé comme un espace de stockage par défaut ou un espace de stockage supplémentaire.

> [AZURE.NOTE] Quelques points importants à noter. 
> 
> * Option permettant de créer des clusters HDInsight avec accès aux données Lake Store est disponible uniquement pour les versions HDInsight 3.2 et 3.4 (pour les clusters Hadoop, HBase et vague de sous Windows, ainsi que Linux). Pour clusters explosion sous Linux, cette option n’est disponible sur les clusters HDInsight 3.4.
>
> * Comme indiqué ci-dessus, Data Lake Store est disponible sous forme de stockage par défaut pour certains types de cluster (HBase) et d’espace de stockage supplémentaire pour les autres types de cluster (Hadoop, explosion, vague de). À l’aide de données Lake Store en tant que compte de l’espace de stockage supplémentaire n’influe pas sur les performances ou la fonctionnalité permettant d’en lecture/écriture à l’espace de stockage du cluster. Dans un scénario où les données Lake Store est utilisé comme espace de stockage supplémentaire, les fichiers liés au cluster (par exemple, les journaux, etc.) sont écrits sur le stockage par défaut (BLOB Azure), tandis que les données que vous souhaitez traiter peuvent être stockées dans un compte de données Lake Store.


Dans cet article, nous mise en service un cluster Hadoop avec données Lake Store comme espace de stockage supplémentaire.

Configuration HDInsight pour travailler avec les données Lake Store à l’aide de PowerShell comprend les étapes suivantes :

* Créer un magasin Lake de données Azure
* Configurer l’authentification pour l’accès basé sur un rôle au magasin Lake des données
* Créer le cluster HDInsight avec une authentification au magasin Lake des données
* Exécuter une tâche de test sur le cluster

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 ou version ultérieure**. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

- **Kit de développement Windows**. Vous pouvez l’installer à partir [d’ici](https://dev.windows.com/en-us/downloads). Vous permet de créer un certificat de sécurité.

- **Azure Active Directory Service Principal**. Étapes de ce didacticiel fournissent des instructions sur la façon de créer une entité de service dans Azure AD. Toutefois, vous devez être un administrateur Azure AD doit être en mesure de créer une entité de service. Si vous êtes un administrateur Azure AD, vous pouvez ignorer ces conditions préalables et poursuivre le didacticiel.
    
    **Si vous n’êtes pas administrateur Azure AD**, vous ne serez pas en mesure d’effectuer les étapes nécessaires pour créer une entité de service. Dans ce cas, votre administrateur Azure AD devez d’abord créer une entité de service avant de pouvoir créer un cluster HDInsight avec données Lake Store. En outre, l’entité de service doit être créée à l’aide d’un certificat, tels que décrits sur la [Création d’un service principal avec le certificat](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Créer un magasin Lake de données Azure

Suivez ces étapes pour créer un magasin Lake de données.

1. À partir de votre bureau, ouvrez une nouvelle fenêtre PowerShell Azure et entrez l’extrait de code suivante. Lorsque vous êtes invité à se connecter, vérifiez que vous ouvrez une session dans l’un des admininistrators/propriétaire de l’abonnement :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Si vous recevez une erreur semblable à `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` lorsque vous enregistrez le fournisseur de ressources de données Lake Store, il est possible que votre subsrcription n’est pas autorisés pour Azure données Lake Store. Veillez à qu'activer votre abonnement Azure pour présentation publique de données Lake Store en suivant ces [instructions](data-lake-store-get-started-portal.md#signup).

3. Un compte Azure données Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créez un compte Azure données Lake Store. Le nom du compte que vous spécifiez doit contenir uniquement les lettres minuscules et numéros.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Créer un compte Azure données Lake] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure données Lake")

3. Vérifiez que le compte est créé.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    La sortie pendant ce doit être **vraie**.

4. Téléchargez des exemples de données sur Lake de données Azure. Nous utiliserons ce plus loin dans cet article pour vérifier que les données sont accessibles à partir d’un cluster HDInsight. Si vous cherchez des exemples de données à télécharger, vous pouvez obtenir le dossier **Ambulance données** à partir du [Référentiel de Git Azure données Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurer l’authentification pour l’accès basé sur un rôle au magasin Lake des données

Chaque abonnement Azure est associé à un serveur Azure Active Directory. Les utilisateurs et services qui accèdent aux ressources de l’abonnement à l’aide du portail classique Azure ou Azure Gestionnaire de ressources API doivent tout d’abord s’authentifier avec cette Azure Active Directory. L’accès aux services et abonnements Azure en les affectant le rôle approprié sur une ressource d’Azure.  Pour les services, un principal de service identifie le service dans Azure Active Directory (DAS). Cette section illustre l’octroi d’un service d’application, tel que HDInsight, accéder à une ressource Azure (compte Azure données Lake magasin que vous avez créée) en créant un principal de service pour l’application et attribution de rôles à celle via PowerShell Azure.

Pour configurer l’authentification Active Directory pour Lake de données Azure, vous devez effectuer les tâches suivantes.

* Créer un certificat auto-signé
* Créer une application dans Azure Active Directory et un Principal de Service

### <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Vérifiez que vous disposez de [Windows SDK](https://dev.windows.com/en-us/downloads) installé avant d’effectuer les étapes décrites dans cette section. Vous devez également créer un annuaire, par exemple, **C:\mycertdir**, où le certificat sera créé.

1. Dans la fenêtre PowerShell, accédez à l’emplacement où vous avez installé le Kit de développement Windows (en règle générale, `C:\Program Files (x86)\Windows Kits\10\bin\x86` et utiliser la [MakeCert] [ makecert] utilitaire permettant de créer un certificat auto-signé et une clé privée. Utilisez les commandes suivantes.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Vous devrez entrer le mot de passe de clé privée. Après l’exécution de la commande, vous devez voir un **CertFile.cer** et **mykey.pvk** dans l’annuaire de certificat que vous avez spécifié.

4. Utiliser la [Pvk2Pfx] [ pvk2pfx] utilitaire pour convertir les fichiers .pvk et .cer MakeCert créé vers un fichier .pfx. Exécutez la commande suivante.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Lorsque vous êtes invité permet d’entrer le mot de passe de clé privée que vous avez spécifié précédemment. La valeur que vous spécifiez pour le paramètre de **commande** est le mot de passe est associé au fichier .pfx. Une fois la commande terminée, vous devez également voir une CertFile.pfx dans le répertoire de certificat que vous avez spécifié.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Créer un Azure Active Directory et un principal de service

Dans cette section, vous exécutez les étapes pour créer un service principal pour une application Azure Active Directory, attribuer un rôle au service principal et authentifier comme l’entité de service en fournissant un certificat. Exécutez les commandes suivantes pour créer une application dans Azure Active Directory.

1. Collez les applets de commande dans la fenêtre de la console PowerShell. Vérifiez que la valeur que vous spécifiez pour la propriété **- DisplayName** est unique. En outre, les valeurs de la **Page d’accueil -** et **- IdentiferUris** sont des valeurs d’espace réservé et ne sont pas vérifiés.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Créer une entité de service à l’aide de l’ID d’application.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Accorder l’accès principal service vers le fichier/dossier de données Lake Store vous accéderez à partir du cluster HDInsight. L’extrait de code ci-dessous permet d’accéder à la racine du compte données Lake Store.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    À l’invite, entrez **Y** pour confirmer.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Créer un cluster HDInsight avec une authentification au magasin Lake des données

Dans cette section, nous créons un cluster HDInsight Hadoop. Pour cette version, le cluster HDInsight et le magasin de Lake données doivent être au même endroit (États-Unis Extrême-Orient 2).

1. Commencer avec l’extraction de l’ID de client abonnement. Vous devrez qui ultérieurement.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Pour cette version, pour un cluster Hadoop, données Lake magasin peut uniquement être utilisé comme un espace de stockage supplémentaire pour le cluster. Le stockage par défaut sera toujours les objets BLOB Azure stockage (WASB). Par conséquent, nous allons tout d’abord créer le compte de stockage et les conteneurs de stockage requis pour le cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Créez le cluster HDInsight. Utiliser les applets de commande.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Une fois l’applet de commande terminée, vous devriez voir un résultat comme suit :

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Exécution de tâches de test sur le cluster HDInsight à utiliser le magasin de Lake de données

Une fois que vous avez configuré un cluster de HDInsight, vous pouvez exécuter des tâches d’essai sur le cluster pour vérifier que le cluster HDInsight peut accéder à des données Lake Store. Pour ce faire, nous allons exécuter une tâche Hive exemple qui crée une table avec les données que vous avez précédemment téléchargée vers votre Boutique Lake de données.

### <a name="for-a-linux-cluster"></a>Pour un cluster Linux

Dans cette section, vous allez SSH dans le cluster et exécuter l’exemple de requête Hive. Windows ne fournit pas un clientSSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Une fois connecté, démarrez l’infrastructure du langage commun ruche en utilisant la commande suivante :

        hive

2. À l’aide de l’infrastructure du langage commun, entrez les instructions suivantes pour créer une table nommée **véhicules** en utilisant les exemples de données du magasin de données Lake :

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Vous devriez voir un résultat semblable à ce qui suit :

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### <a name="for-a-windows-cluster"></a>Pour un cluster de Windows

Utiliser les applets de commande pour exécuter la requête Hive. Dans cette requête nous créer une table à partir des données dans le magasin de Lake de données, puis exécutez une requête sélection dans la table créée.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Cela aura le résultat suivant. **ExitValue** de 0 dans le résultat suggère que le travail a réussi.

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

Extraire le résultat de la tâche à l’aide de l’applet de commande suivante :

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

La sortie des tâches ressemble à ceci :

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>Accès aux données Lake Store à l’aide des commandes HADOOP

Une fois que vous avez configuré le cluster HDInsight pour utiliser des données Lake Store, vous pouvez utiliser les commandes de shell HADOOP pour accéder au magasin.

### <a name="for-a-linux-cluster"></a>Pour un cluster Linux

Dans cette section vous sera SSH dans le cluster et exécuter les commandes HADOOP. Windows ne fournit pas un clientSSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Une fois connecté, utilisez la commande de système de fichiers HADOOP suivante pour répertorier les fichiers du magasin de données Lake.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Cette opération doit répertorier le fichier que vous avez téléchargée précédemment au magasin Lake de données.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Vous pouvez également utiliser la `hdfs dfs -put` commande pour télécharger des fichiers au magasin Lake de données, puis utilisez `hdfs dfs -ls` pour vérifier si les fichiers téléchargés avec succès.


### <a name="for-a-windows-cluster"></a>Pour un cluster de Windows

1. Ouverture de session au nouveau [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir**et cliquez sur **clusters HDInsight**, puis cliquez sur le cluster HDInsight que vous avez créé.

3. Dans la carte cluster, cliquez sur **Bureau à distance**et puis, dans la carte de **Bureau à distance** , cliquez sur **se connecter**.

    ![À distance en cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Créer un groupe de ressources Azure")

    Lorsque vous y êtes invité, entrez les informations d’identification que vous avez fourni pour l’utilisateur de bureau à distance.

4. Dans la session distante, démarrez Windows PowerShell et utilisez les commandes de système de fichiers HADOOP pour répertorier les fichiers dans le magasin de Lake données Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Cette opération doit répertorier le fichier que vous avez téléchargée précédemment au magasin Lake de données.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    Vous pouvez également utiliser la `hdfs dfs -put` commande pour télécharger des fichiers au magasin Lake de données, puis utilisez `hdfs dfs -ls` pour vérifier si les fichiers téléchargés avec succès.

## <a name="see-also"></a>Voir aussi

* [Portail : Créez un cluster HDInsight pour utiliser des données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
