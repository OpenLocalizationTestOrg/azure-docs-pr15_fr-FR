<properties 
   pageTitle="Configurer la réplication HBase entre deux centres de données | Microsoft Azure" 
   description="Découvrez comment configurer la réplication HBase entre deux centres de données, ainsi que sur les cas d’utilisation de la réplication cluster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurer la réplication geo HBase HDInsight

> [AZURE.SELECTOR]
- [Configurer la connectivité VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le système DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication HBase](hdinsight-hbase-geo-replication.md) 
 
Découvrez comment configurer la réplication HBase entre deux centres de données. Certaines utilisent pour réplication cluster incluent :

- Sauvegarde et restauration
- Agrégation de données
- Distribution des données géographiques
- Réception de données en ligne combiné avec analytique de données hors connexion

Réplication cluster utilise une méthodologie source push. Un cluster HBase peut être une source et une destination, ou peut répondre à ces deux rôles en même temps. La réplication est asynchrone, et l’objectif de la réplication est la cohérence éventuelle. Lorsque la source reçoit une modification pour une famille de colonne avec réplication est activée, cette modification est propagée à tous les clusters de destination. Lorsque les données sont répliquées à partir d’un cluster vers un autre, le cluster source et tous les clusters qui ont déjà utilisé les données sont suivies pour éviter les boucles de réplication. Pour plus d’informations, dans ce didacticiel, vous allez configurer une réplication de destination à la source.  Pour les autres topologies cluster, voir [Guide de référence HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Il s’agit de la troisième partie de la série :

- [Configurer une connectivité VPN entre deux réseaux virtuels][hdinsight-hbase-replication-vnet]
- [Configurer le système DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns]
- Configurer la réplication geo HBase (ce didacticiel)

Le diagramme suivant illustre les deux réseaux virtuels et la connectivité réseau que vous avez créé à [configurer une connectivité VPN entre deux réseaux virtuels] [ hdinsight-hbase-geo-replication-vnet] et [Configurer le DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns]: 

![Diagramme de réseau virtuel la réplication HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail avec Azure PowerShell**.

    Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définissez la stratégie d’exécution sur *RemoteSigned*. Reportez-vous à l’aide de l’applet de commande Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Réseau virtuel Azure deux avec connectivité VPN et DNS configuré**.  Pour plus d’informations, voir [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet]et [Configurer DNS entre deux réseaux virtuels Azure][hdinsight-hbase-replication-dns].


    Avant d’exécuter des scripts PowerShell, vérifiez que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements Azure, utilisez l’applet de commande suivante pour définir l’abonnement actif :

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Mise en service de groupes de HBase dans un HDInsight

Dans [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-replication-vnet], vous avez créé un réseau virtuel dans une Europe centre de données et un réseau virtuel dans un centre de données des États-Unis. Le réseau virtuel deux sont connectés via VPN. Dans cette session, vous configurera un cluster HBase dans chacun des réseaux virtuels. Plus loin dans ce didacticiel, vous effectuerez parmi les groupes HBase répliquer l’autre cluster HBase.

Le portail classique Azure non prises en charge mise à disponibilité clusters HDInsight avec les options de configuration personnalisée. Par exemple, définissez *hbase.replication* sur *true*. Si vous définissez la valeur dans le fichier de configuration après qu’un cluster est configuré, vous perdrez le paramètre une fois que le cluster est en cours que. Pour plus d’informations, voir [groupes de mise en service Hadoop dans un HDInsight][hdinsight-provision]. Une des options de mise en service cluster HDInsight avec les options personnalisées est à l’aide de PowerShell Azure.


**Mise en service un HBase Cluster dans Contoso-VNet-UE** 

1. À partir de votre poste de travail, ouvrez Windows PowerShell ISE.
2. Définissez les variables au début du script, puis exécutez le script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Mise en service un HBase Cluster dans Contoso-VNet-US** 

- Utiliser le même script avec les valeurs suivantes :

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Étant donné que vous êtes déjà connecté à votre compte Azure, vous n’avez pas besoin de plus exécuter la comlets suivants :

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configurer les redirecteurs conditionnelle DNS

Dans le [Configurer le système DNS pour les réseaux virtuels][hdinsight-hbase-replication-dns], vous avez configuré les serveurs DNS pour les deux réseaux. Les groupes HBase ont suffixes de domaine différent. Ce cas, vous devez configurer les redirecteurs DNS supplémentaires.

Pour configurer les redirecteurs conditionnelle, vous devez savoir suffixes des deux groupes HBase domaine. 

**Pour rechercher les suffixes de domaine des deux groupes HBase**

1. RDP dans **Contoso-HBase-Union européenne**.  Pour plus d’informations, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail classique Azure][hdinsight-manage-portal]. Il est en réalité headnode0 du cluster.
2. Ouvrez une console Windows PowerShell, ou une invite de commande.
3. Exécutez **ipconfig**et notez le **suffixe DNS spécifique à la connexion**.
4. Veuillez ne fermez pas la session RDP.  Vous aurez besoin ultérieurement pour tester la résolution du nom de domaine.
5. Répétez ces étapes pour déterminer le **suffixe DNS spécifique à la connexion** de **Contoso-HBase-US**.


**Pour configurer des redirecteurs DNS**
 
1.  RDP dans **Contoso-DNS-Union européenne**. 
2.  Cliquez sur la touche Windows dans le coin inférieur gauche.
2.  Cliquez sur **Outils d’administration**.
3.  Cliquez sur **DNS**.
4.  Dans le volet gauche, développez **DSN**, **Contoso-DNS-UE**.
5.  Avec le bouton droit **Redirecteurs conditionnels**, puis cliquez sur **Nouvelle redirecteurs conditionnelle**. 
5.  Entrez les informations suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de Contoso-HBase-US. Par exemple : Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-États-Unis. Vérifiez que l’adresse IP. Votre serveur DNS peut avoir une adresse IP différente.
6.  Appuyez sur **entrée**, puis cliquez sur **OK**.  Maintenant que vous ne pourrez pas résoudre l’adresse IP de Contoso-DNS-États-Unis de Contoso-DNS-Union européenne.
7.  Répétez les étapes pour ajouter un redirecteurs conditionnelle DNS du service DNS sur l’ordinateur virtuel Contoso-DNS-US avec les valeurs suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de l’UE de HBase de Contoso. 
    - **Adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-l’Union européenne.

**Pour tester la résolution du nom de domaine**

1. Basculer vers la fenêtre de Contoso-HBase-UE RDP.
2. Ouvrez une invite de commandes.
3. Exécutez la commande ping :

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    Le protocole ICM est activé sur les nœuds du travail des groupes HBase

4. Ne fermez pas la session RDP. Vous en aurez toujours besoin plus loin dans le didacticiel.
5. Répétez ces étapes pour faire un ping le headnode0 de l’UE HBase Contoso de Contoso-HBase-US.

>[AZURE.IMPORTANT] DNS doit travailler avant de procéder aux étapes suivantes.

## <a name="enable-replication-between-hbase-tables"></a>Activer la réplication entre les tables HBase

À présent, vous pouvez créer un exemple de tableau HBase, activer la réplication et tester puis avec des données. L’exemple de tableau que vous utiliserez comporte deux familles de colonne : personnel et Office. 

Dans ce didacticiel, vous effectuerez le cluster HBase Europe comme le cluster source et le cluster US HBase que le cluster de destination.

Créer des tableaux HBase avec les mêmes noms et familles de colonne sur les clusters source et cible, indiquer à l’emplacement de stockage des données, qu'il reçoit le cluster de destination. Pour plus d’informations sur l’utilisation de l’interface HBase, voir [prise en main HBase Apache dans HDInsight][hdinsight-hbase-get-started].

**Pour créer une table HBase sur Contoso-HBase-UE**

1. Basculer vers la fenêtre RDP **Contoso-HBase-UE** .
2. À partir du bureau, cliquez sur **ligne de commande Hadoop**.
2. Modifier le dossier sur le répertoire HBase :

        cd %HBASE_HOME%\bin
3. Ouvrez le shell HBase :

        hbase shell
4. Créer un tableau HBase :

        create 'Contacts', 'Personal', 'Office'
5. Ne fermez pas la session RDP ni la fenêtre de ligne de commande Hadoop. Vous aurez besoin toujours plus loin dans le didacticiel.
    
**Pour créer une table HBase sur Contoso-HBase-US**

- Répétez ces étapes pour créer la même table sous Contoso-HBase-US.


**Pour ajouter Contoso-HBase-US comme un homologue réplication**

1. Basculer vers la fenêtre RDP **Contso HBase_EU** .
2. Dans la fenêtre de shell HBase, ajoutez le cluster de destination (Contoso-HBase-US) en tant qu’homologue, par exemple :

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    Dans l’exemple, le suffixe de domaine est *contoso-hbase-us.d4.internal.cloudapp.net*. Vous devez mettre à jour pour qu’il corresponde votre suffixe de domaine du cluster nous HBase. Vérifiez qu’il n’y a aucuns espace entre les noms d’hôte.

**Pour configurer chaque famille de colonne doivent être répliquées sur le cluster source**

1. Dans la fenêtre de shell HBase de la session RDP **Contso-HBase-UE** , configurez chaque famille de colonne doivent être répliquées :

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Pour télécharger des données à la table HBase en bloc**

Un exemple de fichier de données a été téléchargé dans un conteneur Blob Azure public avec l’URL suivante :

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Le contenu du fichier :

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

Vous pouvez télécharger le même fichier de données dans votre cluster HBase et importer les données à partir de là.

1. Basculer vers la fenêtre RDP **Contoso-HBase-UE** .
2. À partir du bureau, cliquez sur **ligne de commande Hadoop**.
3. Modifier le dossier sur le répertoire HBase :

        cd %HBASE_HOME%\bin

4. charger les données :

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Vérifiez que la réplication de données est en cours

Vous pouvez vérifier que la réplication a lieu en analysant les tables des deux cluster avec les commandes de shell HBase suivantes :

        Scan 'Contacts'


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer la réplication HBase sur deux centres de données. Pour en savoir plus sur HDInsight et HBase, voir :

- [Page du service HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Prise en main Apache HBase dans HDInsight][hdinsight-hbase-get-started]
- [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview]
- [Mise en service de clusters HBase sur réseau virtuel Azure][hdinsight-hbase-provision-vnet]
- [Analyser opinion Twitter en temps réel avec HBase][hdinsight-hbase-twitter-sentiment]
- [Analyse des données de capteur avec vague d’et HBase dans HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
