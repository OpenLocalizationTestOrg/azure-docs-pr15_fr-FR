<properties
 pageTitle="Cluster HPC Pack pour Excel et SOA | Microsoft Azure"
 description="Prise en main à grande échelle Excel et SOA charges de travail en cours d’exécution sur un cluster HPC Pack dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Prise en main Excel et SOA charges de travail en cours d’exécution sur un cluster HPC Pack dans Azure

Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur Azure machines virtuelles à l’aide d’un modèle de démarrage rapide Azure, ou vous pouvez également un script de déploiement d’Azure PowerShell. Le cluster utilise images Azure Marketplace machine virtuelle conçus pour exécuter Microsoft Excel ou charges de travail architecture orientée services (SOA) avec HPC Pack. Vous pouvez utiliser le cluster pour exécuter HPC Excel simple et services SOA à partir d’un ordinateur client local. Les services Excel HPC incluent décharger de classeur Excel et les fonctions définies par l’utilisateur Excel ou UDF.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

À un niveau élevé, le diagramme suivant illustre le cluster HPC Pack que vous créez.

![Cluster HPC avec nœuds exécutant les charges de travail Excel][scenario]

## <a name="prerequisites"></a>Conditions préalables

*   **Ordinateur client** - vous avez besoin d’un ordinateur fonctionnant sous Windows client à soumettre des tâches Excel et SOA exemple pour le cluster. Vous avez également besoin d’un ordinateur Windows pour exécuter le script PowerShell Azure cluster déploiement (si vous choisissez cette méthode de déploiement) et

*   **Abonnement azure** - si vous n’avez pas un abonnement Azure, vous pouvez créer un [compte de libérer](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

*   **Quota de cœurs** - vous devrez peut-être augmenter le quota de cœurs, surtout si vous déployez plusieurs nœuds de cluster dans plusieurs cœurs tailles de machine virtuelle. Si vous utilisez un modèle de démarrage rapide Azure, le quota de cœurs dans le Gestionnaire de ressources est par région Azure. Dans ce cas, vous devrez peut-être augmenter le quota d’une région spécifique. Voir [la limite d’abonnements Azure, les quotas et les contraintes](../azure-subscription-service-limits.md). Pour augmenter un quota, [Ouvrez une demande de support client en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sans frais.

*   **Licence Microsoft Office** - si vous déployez des nœuds de calcul à l’aide d’une image Marketplace HPC Pack machine virtuelle avec Microsoft Excel, une version d’évaluation de 30 jours de Microsoft Excel Professional Plus 2013 est installée. Après la période d’évaluation, vous devez fournir une licence Microsoft Office valide pour activer Excel pour continuer à exécuter les charges de travail. Voir [activation Excel](#excel-activation) plus loin dans cet article. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Étape 1. Configurer un cluster HPC Pack dans Azure

Nous afficher deux options pour définir le cluster : tout d’abord, à l’aide d’un modèle de démarrage rapide Azure et le portail Azure ; et les secondes, à l’aide d’un script de déploiement d’Azure PowerShell.


### <a name="option-1-use-a-quickstart-template"></a>Option 1. Utiliser un modèle de démarrage rapide
Utiliser un modèle de démarrage rapide Azure pour déployer rapidement et facilement un cluster HPC Pack dans le portail Azure. Lorsque vous ouvrez le modèle dans le portail, vous obtenez une interface utilisateur simple dans laquelle vous entrez les paramètres pour votre cluster. Voici les étapes. 

>[AZURE.TIP]Si vous le souhaitez, utilisez un [modèle Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) qui crée un cluster similaire spécialement pour les charges de travail Excel. Les étapes différer légèrement dans la liste suivante.

1.  Visitez la [page Créer un Cluster HPC modèle GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si vous le souhaitez, consultez les informations sur le modèle et le code source.

2.  Cliquez sur **déployer vers Azure** pour commencer un déploiement avec le modèle dans le portail Azure.

    ![Déployer un modèle sur Azure][github]

3.  Dans le portail, procédez comme suit pour entrer les paramètres pour le modèle de cluster HPC.

    un. Dans la page **paramètres** , entrez ou modifiez les valeurs pour les paramètres de modèle. (Cliquez sur l’icône en regard de chaque paramètre pour les informations d’aide). Exemples de valeurs sont représentées dans l’écran suivant. Cet exemple crée un cluster nommé *hpc01* dans le domaine *hpc.local* composée d’un nœud de tête et 2 nœuds de calcul. Les nœuds de calcul sont créés à partir d’une image de machine virtuelle HPC Pack incluant Microsoft Excel.

    ![Entrer les paramètres][parameters]

    >[AZURE.NOTE]Le nœud de tête machine virtuelle est créée automatiquement à partir de [dernière image Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 sur Windows Server 2012 R2. Actuellement l’image est basé sur HPC Pack 2012 R2 mise à jour 3.
    >
    >Machines virtuelles cluster nœud sont créées à partir de la dernière image de la famille de nœud cluster sélectionné. Sélectionnez l’option **ComputeNodeWithExcel** pour la dernière image nœud cluster HPC Pack qui inclut une version d’évaluation de Microsoft Excel Professionnel Plus 2013. Pour déployer un cluster pour les sessions SOA générales ou décharger UDF Excel, choisissez l’option **ComputeNode** (sans Excel installé).

    b. Sélectionnez l’abonnement.

    c. Créer un groupe de ressources pour le cluster, par exemple *hpc01RG*.

    d. Choisissez un emplacement pour le groupe de ressources, tels que des États-Unis centre.

    e. Dans la page **conditions juridiques** , passez en revue les termes du contrat. Si vous êtes d’accord, cliquez sur **acheter**. Puis, lorsque vous avez terminé de définir les valeurs pour le modèle, cliquez sur **créer**.

4.  Lorsque le déploiement est terminé (il faut généralement environ 30 minutes), exporter le fichier de certificat cluster du cluster nœud de tête. Dans une étape ultérieure, vous importez ce certificat public sur l’ordinateur client pour permettre l’authentification côté serveur pour la liaison HTTP sécurisée.

    un. Se connecter pour le nœud de tête Bureau à distance à partir du portail Azure.

     ![Se connecter au nœud de tête][connect]

    b. Utilisez les procédures standard dans le Gestionnaire de certificats pour exporter le certificat de nœud de tête (situé sous certificat : \LocalMachine\My) sans la clé privée. Dans cet exemple, exporter *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exportez le certificat][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Option 2. Utilisez le script HPC Pack IaaS déploiement

Le script de déploiement HPC Pack IaaS offre un autre moyen polyvalent déployer un cluster HPC Pack. Il crée un cluster dans le modèle de déploiement classique, alors que le modèle utilise le modèle de déploiement d’Azure le Gestionnaire de ressources. En outre, le script est compatible avec un abonnement dans le service d’Azure globale ou Azure Chine.

**Autres conditions préalables**

* **PowerShell Azure** - [installer et configurer Azure PowerShell](../powershell-install-configure.md) (version 0.8.10 ou version ultérieure) sur votre ordinateur client.

* **Script de déploiement HPC Pack IaaS** - télécharger et décompresser la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vérifiez la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur version 4.5.0 ou version ultérieure du script.

**Créer le fichier de configuration**

 Le script de déploiement HPC Pack IaaS utilise un fichier de configuration XML comme entrée qui décrit l’infrastructure du cluster HPC. Pour déployer un cluster constitué d’un nœud de tête et 18 nœuds cluster créés à partir de l’image de nœud de calcul incluant Microsoft Excel, remplacer les valeurs pour votre environnement dans l’exemple de fichier de configuration suivant. Pour plus d’informations sur le fichier de configuration, consultez le fichier Manual.rtf dans le dossier de script et [créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Remarques sur le fichier de configuration**

* **VMName** du nœud principal **doit** être identique à la **ServiceName%**ou travaux SOA ne s’exécutent pas.

* Vérifiez que vous spécifiez **EnableWebPortal** afin que le certificat de nœud principal est généré et exporté.

* Le fichier désigne le script PowerShell postérieures à la configuration PostConfig.ps1 qui s’exécute sur le nœud principal. Exemple de script suivant configure la chaîne de connexion de stockage Azure, supprime le rôle de nœud de cluster du nœud de tête et affiche tous les nœuds en ligne lorsqu’ils sont déployés. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Exécuter le script**

1.  Ouvrez la console PowerShell sur l’ordinateur client en tant qu’administrateur.

2.  Basculez vers le dossier de script (E:\IaaSClusterScript dans cet exemple).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Pour déployer le cluster HPC Pack, exécutez la commande suivante. Cet exemple suppose que le fichier de configuration se trouve dans E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

Le script de déploiement HPC Pack s’exécute depuis un certain temps. Le seul endroit que le script effectue consiste à exporter et téléchargez le certificat cluster et enregistrez celui-ci au dossier Documents de l’utilisateur actuel sur l’ordinateur client. Le script génère un message semblable à ce qui suit. Dans une étape suivante, vous importez le certificat dans le magasin de certificats approprié.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Étape 2. Décharger des classeurs Excel et exécuter UDF à partir d’un client local

### <a name="excel-activation"></a>Activation d’Excel

Lorsque vous utilisez l’image ComputeNodeWithExcel VM pour les charges de travail, vous devez fournir une clé de licence Microsoft Office valide pour activer Excel sur les nœuds de calcul. Dans le cas contraire, la version d’évaluation d’Excel expire après 30 jours, et en cours d’exécution des classeurs Excel échoueront avec COMException (0x800AC472). 

Vous pouvez réarmer Excel pour un autre 30 jours de la période d’évaluation : ouvrez une session sur le nœud de tête et clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` sur Excel tous les nœuds via HPC Cluster Manager de calcul. Vous pouvez réarmer un maximum de deux fois. Après cela, vous devez fournir une clé de licence Office valide.

Le Office Professionnel Plus 2013 est installé sur l’image de la mémoire virtuelle est une édition en volume avec une clé de licence en Volume générique (GVLK). Vous pouvez l’activer via le Service de gestion de clés (KMS) / Active Directory-Based Activation (AD-BA) ou clé d’Activation Multiple (MAK). 

    * Pour utiliser KMS/AD-BA, utiliser un serveur KMS existant ou configurer un nouvel identifiant en utilisant le module de licence en Volume de Microsoft Office 2013. (Si vous le souhaitez, configurez le serveur sur le nœud de tête.) Ensuite, activer la clé d’hôte KMS via Internet ou par téléphone. Puis clusrun `ospp.vbs` pour définir le serveur KMS et le port et activer Office sur tous les nœuds de calcul Excel. 
    
    * Utiliser la clé d’activation multiple, première clusrun `ospp.vbs` d’entrer la clé et puis activer toutes les nœuds via Internet ou par téléphone de calcul Excel. 

>[AZURE.NOTE]Clés de produit de vente au détail pour Office Professionnel Plus 2013 ne peuvent pas être utilisés avec cette image machine virtuelle. Si vous avez clés valides et support d’installation pour les éditions Office ou Excel que cette édition en volume Office Professionnel Plus 2013, vous pouvez les utiliser à la place. Commencez par désinstaller cette édition en volume et installez l’édition dont vous disposez. Le nœud de calcul Excel réinstallez peut être capturé comme une image de machine virtuelle personnalisée à utiliser dans un déploiement à l’échelle.

### <a name="offload-excel-workbooks"></a>Décharger des classeurs Excel

Suivez ces étapes pour décharger un classeur Excel afin qu’elle s’exécute sur le cluster HPC Pack dans Azure. Pour ce faire, vous devez disposer d’Excel 2010 ou 2013 est déjà installé sur l’ordinateur client.

1. Utilisez une des options à l’étape 1 pour déployer un cluster HPC Pack avec Excel calculer l’image nœud. Obtenir le fichier de certificat cluster (.cer) et le cluster nom d’utilisateur et le mot de passe.

2. Sur l’ordinateur client, importez le certificat cluster sous certificat : \CurrentUser\Root.

3. Assurez-vous qu’elle est installée. Créer un fichier Excel.exe.config avec le contenu suivant dans le même dossier que Excel.exe sur l’ordinateur client. Cette étape garantit que le complément COM de HPC Pack 2012 R2 Excel se charge avec succès.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configurer le client à soumettre des tâches au cluster HPC Pack. Une option consiste à télécharger l' [installation HPC Pack 2012 R2 mise à jour 3](http://www.microsoft.com/download/details.aspx?id=49922) complète et installer le client HPC Pack. Par ailleurs, téléchargez et installez les [utilitaires client HPC Pack 2012 R2 mise à jour 3](https://www.microsoft.com/download/details.aspx?id=49923) et approprié Visual C++ 2010 redistribuable pour votre ordinateur ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  Dans cet exemple, nous utilisons un exemple de classeur Excel nommé ConvertiblePricing_Complete.xlsb. Vous pouvez le télécharger [ici](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copier le classeur Excel dans un dossier de travail comme D:\Excel\Run.

7.  Ouvrez le classeur Excel. Dans le ruban **développer** , cliquez sur **Compléments COM** et vérifiez que le complément COM d’Excel HPC Pack est chargé avec succès.

    ![Macro complémentaire Excel pour HPC Pack][addin]

8.  Modifier la macro VBA HPCControlMacros dans Excel en modifiant les lignes commentées comme indiqué dans le script suivant. Remplacer les valeurs appropriées pour votre environnement.

    ![Macro Excel pour HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copier le classeur Excel dans un répertoire de téléchargement tels que D:\Excel\Upload. Ce répertoire spécifié dans la constante HPC_DependsFiles dans la macro VBA.

10. Pour exécuter le classeur sur le cluster dans Azure, cliquez sur le bouton **Cluster** dans la feuille de calcul.

### <a name="run-excel-udfs"></a>Exécuter Excel UDF

Pour exécuter Excel UDF, suivez les étapes précédentes 1 à 3 pour configurer l’ordinateur client. Pour Excel UDF, vous n’avez pas besoin pour que l’application Excel est installée sur les nœuds de calcul. Par conséquent, lors de la création de votre cluster des nœuds de calcul, vous pouvez choisir une normale calculer l’image nœud au lieu de l’image de nœud de calcul avec Excel.

>[AZURE.NOTE] Il existe une limite de 34 caractères dans la Excel 2010 et boîte de dialogue 2013 cluster connecteur. Cette boîte de dialogue vous permet de spécifier le cluster qui s’exécute à l’UDF. Si le nom du cluster complète est plus long (par exemple, hpcexcelhn01.southeastasia.cloudapp.azure.com), il ne tient pas dans la boîte de dialogue. La solution consiste à définir une variable d’échelle de l’ordinateur tels que *CCP_IAASHN* avec la valeur du nom du cluster long. Entrez ensuite *CCP_IAASHN %* dans la boîte de dialogue en tant que le nom du nœud de tête cluster. 

Une fois que le cluster est déployé avec succès, poursuivez les étapes suivantes pour exécuter un exemple intégré UDF Excel. Pour UDF Excel personnalisée, consultez ces [ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour générer les XLL et les déployer sur le cluster IaaS.

1.  Ouvrez un nouveau classeur Excel. Dans le ruban **développer** , cliquez sur **Compléments**. Puis, dans la boîte de dialogue, cliquez sur **Parcourir**, accédez au dossier %CCP_HOME%Bin\XLL32, puis sélectionnez l’échantillon ClusterUDF32.xll. Si le ClusterUDF32 n’existe pas de l’ordinateur client, copiez-le dans le dossier %CCP_HOME%Bin\XLL32 sur le nœud de tête.

    ![Sélectionnez le fichier UDF][udf]

2.  Cliquez sur **fichier** > **Options** > **avancées**. Sous **formules**, consultez **Autoriser les fonctions XLL définies par l’utilisateur pour exécuter un cluster de calcul**. Cliquez sur **Options** , puis entrez le nom complet dans la zone **nom de nœud de tête Cluster**. (Comme indiqué précédemment cette zone d’entrée est limitée à 34 caractères, pour un nom de cluster long peut s’intègrent pas. Vous pouvez utiliser une variable d’échelle de l’ordinateur ici pour un nom de cluster long.)

    ![Configurer le fichier UDF][options]

3.  Pour exécuter le calcul UDF sur le cluster, cliquez sur la cellule contenant la valeur =XllGetComputerNameC() et appuyez sur ENTRÉE. La fonction extrait simplement le nom du nœud cluster sur lequel s’exécute le fichier UDF. Pour la première exécution, une boîte de dialogue informations d’identification vous invite au nom d’utilisateur et mot de passe pour vous connecter au cluster IaaS.

    ![Exécuter des UDF][run]

    Lorsqu’il y a pour calculer le nombre de cellules, appuyez sur Ctrl-Maj-Alt + F9 pour exécuter le calcul sur toutes les cellules.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Étape 3. Exécuter une charge de travail SOA à partir d’un client local

Pour exécuter les applications SOA générales sur le cluster HPC Pack IaaS, tout d’abord d’une des méthodes utiliser à l’étape 1 pour déployer le cluster. Spécifiez un générique calculer l’image nœud dans ce cas, car vous n’avez pas besoin d’Excel sur les nœuds de calcul. Puis suivez ces étapes.

1. Après avoir récupéré le certificat cluster, importez-le sur l’ordinateur client sous certificat : \CurrentUser\Root.

2. Installer la [Mise à jour 3 SDK HPC Pack 2012 R2](http://www.microsoft.com/download/details.aspx?id=49921) et [utilitaires de client HPC Pack 2012 R2 mise à jour 3](https://www.microsoft.com/download/details.aspx?id=49923). Ces outils permettent de développer et exécuter des applications clientes SOA.

3. Téléchargez le HelloWorldR2 [exemples de code](https://www.microsoft.com/download/details.aspx?id=41633). Ouvrez la HelloWorldR2.sln dans Visual Studio 2010 ou 2012.

4. Générez le projet EchoService tout d’abord. Ensuite, déployez le service au cluster IaaS de la même façon que vous déployez un cluster local. Pour plus d’informations, voir le fichier Lisezmoi.doc HelloWordR2. Modifier et créer le HellWorldR2 et autres projets comme décrit dans la section suivante pour générer les applications clientes SOA qui s’exécutent sur un cluster IaaS Azure.

### <a name="use-http-binding-with-azure-storage-queue"></a>Utiliser la liaison Http avec file d’attente stockage Azure

Pour utiliser une liaison Http avec une file d’attente de stockage Azure, apporter quelques modifications à l’exemple de code.

* Mettre à jour le nom du cluster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Vous pouvez également utiliser la valeur par défaut TransportScheme dans SessionStartInfo ou explicitement définie sur Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Utiliser la liaison par défaut pour le BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Ou définir explicitement à l’aide de basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Vous pouvez également définir l’indicateur UseAzureQueue sur true dans SessionStartInfo. Si ne pas définie, elle est définie true par défaut lorsque le nom du cluster a suffixes de domaine Azure et la TransportScheme est Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Utiliser la liaison Http sans file d’attente stockage Azure

Pour utiliser une liaison Http sans une file d’attente de stockage Azure, définis explicitement l’indicateur UseAzureQueue sur false dans le SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utiliser la liaison NetTcp

Pour utiliser une liaison NetTcp, la configuration est similaire à la connexion à un cluster en local. Vous devez l’ouvrir quelques points de terminaison sur le nœud de tête machine virtuelle. Si vous avez utilisé le script de déploiement HPC Pack IaaS pour créer le cluster, par exemple, définissez les points de terminaison dans le portail classique Azure comme suit.


1. Arrêter la machine virtuelle.

2. Ajouter les ports TCP 9090, 9087, 9091, 9094 pour la Session, Broker, Broker collaborateur et les services de données, respectivement

    ![Configurer les points de terminaison][endpoint]

3. Démarrer la machine virtuelle.

L’application cliente SOA nécessite aucune modification à l’exception de la modification du nom de tête au nom complet cluster IaaS.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [ces ressources](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) pour plus d’informations sur l’exécution de charges de travail Excel avec HPC Pack.

* Pour plus d’informations sur le déploiement et la gestion des services SOA avec Pack HPC, voir [Gestion des Services SOA dans Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) .

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
