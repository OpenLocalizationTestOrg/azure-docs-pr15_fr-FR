<properties
   pageTitle="Script PowerShell pour déployer Windows HPC cluster | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster HPC Pack de Windows Azure machines virtuelles"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Création d’un Windows cluster High performance computing (HPC) avec le script de déploiement HPC Pack IaaS

Exécuter le script PowerShell pour déployer un cluster HPC complète pour les charges de Windows Azure machines virtuelles du déploiement HPC Pack IaaS. Le cluster se compose de nœud de tête rejoint Active Directory exécutant Windows Server et Microsoft HPC Pack et fenêtres supplémentaires calculent les ressources que vous spécifiez. Si vous voulez déployer un cluster HPC Pack dans Azure pour Linux les charges de travail, voir [créer un cluster HPC Linux avec le script de déploiement HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Vous pouvez également utiliser un modèle Azure le Gestionnaire de ressources pour déployer un cluster HPC Pack. Pour obtenir des exemples, voir [créer un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) et [créer un cluster HPC avec un personnalisé image de nœud de calcul](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Exemples de fichiers de configuration

Dans les exemples suivants, remplacez-le par vos propres valeurs pour votre abonnement Id ou nom et les noms de compte et le service.

### <a name="example-1"></a>Exemple 1

Le fichier de configuration suivant déploie un cluster HPC Pack qui comporte un nœud de tête avec les bases de données locales et cinq nœuds exécutant le système d’exploitation Windows Server 2012 R2 de calcul. Tous les services de cloud sont créés directement dans l’emplacement des États-Unis Ouest. Le nœud de tête joue le contrôleur de domaine de la forêt de domaine.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemple 2

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster a 1 nœud principal avec les bases de données locales et 12 nœuds de calcul avec l’extension BGInfo VM appliquée.
Installation automatique des mises à jour Windows est désactivée pour tous les ordinateurs virtuels dans la forêt de domaines. Tous les services de cloud sont créés directement dans l’emplacement de l’Asie de l’est. Les nœuds de calcul sont créés dans trois services cloud et trois comptes de stockage : _MyHPCCN-0001_ à _MyHPCCN-0005_ dans _MyHPCCNService01_ et _mycnstorage01_; _MyHPCCN-0006_ à _MyHPCCN0010_ dans _MyHPCCNService02_ et _mycnstorage02_; et _MyHPCCN-0011_ en _MyHPCCN-0012_ dans _MyHPCCNService03_ et _mycnstorage03_). Les nœuds de calcul sont créés à partir d’une image existante privée capturée à partir d’un nœud de calcul. Les liens automatiquement agrandir et réduire le service est activé par défaut agrandir et réduire les intervalles.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemple 3

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster contient un nœud principal, serveur d’une base de données avec un disque de données 500 Go, 2 nœuds intermédiaire exécutant le système d’exploitation Windows Server 2012 R2 et cinq nœuds cluster exécutant le système d’exploitation Windows Server 2012 R2. Le service de nuage MyHPCCNService est créé dans le groupe affinité *MyIBAffinityGroup*et les autres services de cloud sont créés dans le groupe affinité *MyAffinityGroup*. L’API REST de HPC travail planificateur et le portail web HPC sont activées sur le nœud principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemple 4

Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaine existante. Le cluster possède deux nœud principal avec les bases de données locales, deux Azure nœud modèles sont créés et trois taille moyenne Azure nœuds sont créés pour le modèle de nœud Azure _AzureTemplate1_. Un fichier de script s’exécute sur le nœud de tête après avoir configuré le nœud de tête.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Résolution des problèmes


* **Erreur « VNet n’existe pas »** - si vous exécutez le script pour déployer plusieurs groupes dans un Azure simultanément dans le cadre d’un abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « VNet *VNet\_nom* n’existe pas ».
Si cette erreur se produit, exécutez le script rouvrir pour que l’échec du déploiement.

* **Problèmes d’accès à Internet à partir du réseau virtuel Azure** - si vous créez un cluster avec un contrôleur de domaine en utilisant le script de déploiement, ou vous promouvez manuellement un nœud de tête machine virtuelle à contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion les ordinateurs virtuels à Internet. Ce problème peut se produire si un serveur DNS redirecteurs est configuré automatiquement sur le contrôleur de domaine et que ce serveur DNS redirecteurs n’est pas résolu correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimer le paramètre de configuration redirecteurs ou configurer un serveur DNS redirecteurs valide. Pour configurer ce paramètre, dans le Gestionnaire de serveur, cliquez sur **Outils** >
    **DNS** à ouvrir le Gestionnaire DNS, puis double-cliquez sur **redirecteurs**.

* **Problèmes d’accès réseau RDMA à partir d’ordinateurs virtuels cluster accrue** - si vous ajoutez cluster Windows Server ou intermédiaire financier nœud machines virtuelles à l’aide d’une taille RDMA compatibles avec comme A8 ou A9, vous pouvez rencontrer des problèmes de connexion ces machines virtuelles au réseau application RDMA. Ce problème se produit est si l’extension HpcVmDrivers n’est pas installée correctement lorsque les ordinateurs virtuels sont ajoutés au cluster. Par exemple, l’extension peut être bloquée dans l’état de l’installation.

    Pour contourner ce problème, commencez par consulter l’état de l’extension sur les ordinateurs virtuels. Si l’extension n’est pas installée correctement, essayez de supprimer les nœuds de cluster HPC, puis ajoutez les nœuds à nouveau. Par exemple, vous pouvez ajouter le nœud de calcul machines virtuelles en exécutant le script ajouter HpcIaaSNode.ps1 sur le nœud principal.
    
## <a name="next-steps"></a>Étapes suivantes

* Essayez d’exécuter une charge de travail de test sur le cluster. Pour obtenir un exemple, voir le HPC Pack [guide de mise en route](https://technet.microsoft.com/library/jj884144).

* Pour un didacticiel pour le déploiement de cluster de script et exécuter une charge de travail HPC, voir [prise en main avec un cluster HPC Pack dans Azure pour exécuter les charges de travail Excel et SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Essayez d’outils du Pack HPC pour démarrer, arrêter, ajouter et supprimer des nœuds de calcul à partir d’un cluster que vous créez. Voir [Gérer les nœuds dans un cluster HPC Pack dans Azure de calcul](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Pour préparer à soumettre des tâches au cluster depuis un ordinateur local, voir [Soumettre HPC des travaux à partir d’un ordinateur local à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
