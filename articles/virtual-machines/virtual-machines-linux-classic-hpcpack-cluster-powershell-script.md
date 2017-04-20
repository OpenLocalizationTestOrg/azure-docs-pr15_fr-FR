<properties
   pageTitle="Script PowerShell pour déployer Linux HPC cluster | Microsoft Azure"
   description="Exécuter un script PowerShell pour déployer un cluster Linux HPC Pack dans des machines virtuelles Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Créer un Linux haute performance computing cluster (HPC) avec le script de déploiement HPC Pack IaaS

Exécuter le script PowerShell pour déployer un cluster HPC complète pour les charges de travail Linux dans Azure machines virtuelles du déploiement HPC Pack IaaS. Le cluster se compose de nœud de tête rejoint Active Directory exécutant Windows Server et Microsoft HPC Pack et nœuds de calcul qui s’exécutent une des versions de Linux pris en charge par HPC Pack. Si vous voulez déployer un cluster HPC Pack charges de travail Azure pour Windows, voir [créer un cluster HPC Windows avec le script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Vous pouvez également utiliser un modèle Azure le Gestionnaire de ressources pour déployer un cluster HPC Pack. Pour obtenir un exemple, voir [créer un cluster HPC avec Linux nœuds de calcul](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Exemple de fichier de configuration

Le fichier de configuration suivant crée un nouveau contrôleur de domaine et forêt de domaines et déploie un cluster HPC Pack c'est-à-dire 1 nœud principal avec les bases de données locales et 10 nœuds de calcul Linux. Tous les services de cloud sont créés directement dans l’emplacement de l’Asie de l’est. Les nœuds de calcul Linux sont créés dans les services en nuage 2 et 2 comptes de stockage (c'est-à-dire _MyLnxCN 0001_ à _MyLnxCN-0005_ dans _MyLnxCNService01_ et _mylnxstorage01_) et _MyLnxCN-0006_ à _MyLnxCN-0010_ dans _MyLnxCNService02_ et _mylnxstorage02_. Les nœuds de calcul sont créés à partir d’une image OpenLogic CentOS version 7.0 Linux. 

Entrer vos propres valeurs pour nom de votre abonnement et que les noms de compte et le service.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Résolution des problèmes

* **Erreur « VNet n’existe pas »** - si vous exécutez le script de déploiement HPC Pack IaaS pour déployer plusieurs groupes dans un Azure simultanément dans le cadre d’un abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « VNet *VNet\_nom* n’existe pas ».
Si cette erreur se produit, réexécuter le script pour l’échec du déploiement.

* **Problèmes d’accès à Internet à partir du réseau virtuel Azure** - si vous créez un cluster HPC Pack avec un contrôleur de domaine en utilisant le script de déploiement, ou vous promouvez manuellement un nœud de tête machine virtuelle à contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion les ordinateurs virtuels dans le réseau virtuel Azure à Internet. Cela peut se produire si un serveur DNS redirecteurs est configuré automatiquement sur le contrôleur de domaine et que ce serveur DNS redirecteurs n’est pas résolu correctement.

    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimer le paramètre de configuration redirecteurs ou configurer un serveur DNS redirecteurs valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** >
    **DNS** à ouvrir le Gestionnaire DNS, puis double-cliquez sur **redirecteurs**.
    
## <a name="next-steps"></a>Étapes suivantes

* Voir [prise en main Linux cluster nœuds dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour plus d’informations sur la prise en charge Linux distribution, déplacement de données et soumettre des tâches à un cluster HPC Pack avec Linux nœuds de calcul.
* Pour des didacticiels qui utilisent le script pour créer un cluster et exécuter une charge de travail Linux HPC, voir :
    * [Exécutez NAMD avec Microsoft HPC Pack sur nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Exécutez OpenFOAM avec Microsoft HPC Pack sur nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Exécuter étoile-CCM + avec Microsoft HPC Pack sur Linux nœuds dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
