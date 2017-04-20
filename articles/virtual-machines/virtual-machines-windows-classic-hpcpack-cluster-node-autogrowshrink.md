<properties
 pageTitle="Les nœuds de cluster échelle HPC Pack | Microsoft Azure"
 description="Automatiquement agrandir et réduire le nombre de nœuds de calcul HPC Pack cluster dans Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automatiquement agrandir et réduire les ressources de cluster HPC Pack dans Azure en fonction de la charge de travail cluster




Si vous déployez nœuds Azure « rupture » dans votre cluster HPC Pack ou si vous créez un cluster HPC Pack dans Azure machines virtuelles, vous souhaiterez peut-être un moyen pour agrandir ou réduire le nombre de ressources cluster Azure tels que des nœuds ou cœurs en fonction de la charge de travail en cours sur le cluster automatiquement. Cela permet d’utiliser plus efficacement vos ressources Azure et contrôler leurs coûts.
Pour ce faire, configurez la propriété de cluster HPC Pack **AutoGrowShrink**. Vous pouvez également exécuter le script PowerShell HPC **AzureAutoGrowShrink.ps1** qui est installé avec HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]En outre, actuellement vous pouvez uniquement automatiquement agrandir et réduire les nœuds de calcul HPC Pack exécutant un système d’exploitation Windows Server.

## <a name="set-the-autogrowshrink-cluster-property"></a>Définissez la propriété cluster AutoGrowShrink

### <a name="prerequisites"></a>Conditions préalables

* **HPC Pack 2012 R2 mise à jour 2 ou version ultérieure cluster** - le nœud de tête cluster peut être déployé localement ou dans une machine virtuelle Azure. Voir [configurer un cluster hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) pour commencer avec un nœud principal local et nœuds Azure « rupture ». Consultez le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) déployer rapidement un cluster HPC Pack dans Azure machines virtuelles.


* **Pour un cluster avec un nœud de tête dans Azure** - si vous utilisez le script de déploiement HPC Pack IaaS pour créer le cluster, activez la propriété de cluster **AutoGrowShrink** en définissant l’option AutoGrowShrink dans le fichier de configuration de cluster. Pour plus d’informations, consultez la documentation qui accompagnent le [téléchargement du script](https://www.microsoft.com/download/details.aspx?id=44949). 

    Vous pouvez également activer la propriété cluster **AutoGrowShrink** une fois que vous déployez le cluster à l’aide des commandes PowerShell HPC décrites dans la section suivante. Pour préparer pour cela, tout d’abord effectuer les opérations suivantes :
    1. Configurer un certificat de gestion Azure sur le nœud de tête et dans l’abonnement Azure. Pour un déploiement de test, vous pouvez utiliser le certificat auto-signé par défaut Microsoft HPC Azure HPC Pack installée sur le nœud de tête et simplement télécharger ce certificat à votre abonnement Azure. Étapes et des options, voir les [instructions de la bibliothèque TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Exécutez **regedit** sur le nœud principal, accédez à HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo et ajouter une nouvelle valeur de chaîne. Définissez le nom de la valeur à « Empreinte » et données l’empreinte numérique du certificat à l’étape 1 de la valeur.


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Commandes PowerShell HPC pour définir la propriété AutoGrowShrink

Suivantes sont des exemples de commandes PowerShell HPC pour définir **AutoGrowShrink** et régler son comportement avec des paramètres supplémentaires. Voir [AutoGrowShrink paramètres](#AutoGrowShrink-parameters) plus loin dans cet article pour obtenir la liste complète des paramètres. 

Pour exécuter ces commandes, démarrez PowerShell HPC sur le nœud de tête cluster en tant qu’administrateur.

**Pour activer la propriété AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Pour désactiver la propriété AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Pour modifier l’intervalle agrandir en quelques minutes**

    Set-HpcClusterProperty –GrowInterval <interval>

**Pour modifier l’intervalle de réduction en quelques minutes**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Pour afficher la configuration actuelle de AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>Paramètres AutoGrowShrink

Voici les paramètres AutoGrowShrink que vous pouvez modifier à l’aide de la commande **Set-HpcClusterProperty** .

* **EnableGrowShrink** - commutateur pour activer ou désactiver la propriété **AutoGrowShrink** .
* **ParamSweepTasksPerCore** - nombre de tâches paramètres ranger pour développer un seul cœur. La valeur par défaut consiste à agrandir une carotte par tâche. 
 
    >[AZURE.NOTE] HPC Pack QFE KB3134307 devient **ParamSweepTasksPerCore** **TasksPerResourceUnit**. Il est basé sur le type de ressource de travail et peut être nœud, socket ou core.
    
* **GrowThreshold** - seuil de tâches en file d’attente pour déclencher la croissance automatique. La valeur par défaut est 1, ce qui signifie que s’il existe 1 ou plusieurs tâches dans l’état en file d’attente, croissance automatique nœuds.
* **GrowInterval** - intervalle en minutes pour déclencher la croissance automatique. L’intervalle par défaut est de 5 minutes.
* **ShrinkInterval** - intervalle en minutes pour déclencher un compactage automatique. L’intervalle par défaut est de 5 minutes. |
* **ShrinkIdleTimes** - nombre de chèques continues à réduire pour indiquer que les nœuds sont inactifs. La valeur par défaut est 3 reprises. Par exemple, si la **ShrinkInterval** est de 5 minutes, HPC Pack vérifie si le nœud est inactif 5 minutes. Si les nœuds sont dans l’état d’inactivité après que 3 continue vérifie (15 minutes), puis HPC Pack réduit ce nœud.
* **ExtraNodesGrowRatio** - pourcentage supplémentaire des nœuds pour agrandir pour les tâches en passant Interface MPI (Message). La valeur par défaut est 1, ce qui signifie que HPC Pack grandit nœuds de 1 % pour les travaux MPI. 
* **GrowByMin** - commutateur pour indiquer si la stratégie de croissance automatique est basée sur les ressources minimum requis pour la tâche. La valeur par défaut est false, ce qui signifie que HPC Pack grandit nœuds des projets en fonction des ressources maximales requises pour les tâches.
* **SoaJobGrowThreshold** - seuil des demandes entrantes de SOA pour déclencher la lecture automatique agrandir processus. La valeur par défaut est 50000.  
    
    >[AZURE.NOTE] Ce paramètre est pris en charge HPC Pack 2012 R2 mise à jour 3.
    
* **SoaRequestsPerCore** -nombre de SOA entrant demandes à agrandir un seul cœur. La valeur par défaut est 20 000.  

    >[AZURE.NOTE] Ce paramètre est pris en charge HPC Pack 2012 R2 mise à jour 3.

### <a name="mpi-example"></a>Exemple MPI

Par défaut HPC Pack grandit 1 % de nœuds supplémentaires pour les travaux MPI (**ExtraNodesGrowRatio** est défini sur 1). La raison est que MPI peut-être nécessiter plusieurs nœuds et le travail s’exécutent uniquement lorsque tous les nœuds sont prêts. Azure nœuds au démarrage, il peut arriver qu’un nœud devrez plus de temps pour commencer à d’autres personnes, à l’origine d’autres nœuds d’inactivité pendant l’attente pour ce nœud de préparer. Par croissante de nœuds supplémentaires, HPC Pack réduit cette fois en attente de ressources et potentiellement enregistre les coûts. Pour augmenter le nombre de nœuds supplémentaires pour les travaux MPI (par exemple, sur 10 %), exécutez une commande semblable à

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemple SOA

Par défaut, **SoaJobGrowThreshold** est définie par 50 000 et **SoaRequestsPerCore** est défini sur 200000. Si vous envoyez une tâche SOA avec 70000 demandes, il y ait une tâche en file d’attente et demandes entrantes sont 70000. Dans ce cas grandit HPC Pack 1 principaux de la tâche en file d’attente et des demandes entrantes, augmentera (70000 et 50 000) / 20000 = 1 de base, afin de total augmentera 2 cœurs pour cette tâche SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Exécuter le script AzureAutoGrowShrink.ps1

### <a name="prerequisites"></a>Conditions préalables

* **HPC Pack 2012 R2 mise à jour 1 ou version ultérieure cluster** - le script **AzureAutoGrowShrink.ps1** est installé dans le dossier % CCP_HOME emplacement. Le nœud de tête cluster peut être déployé localement ou dans une machine virtuelle Azure. Voir [configurer un cluster hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) pour commencer avec un nœud principal local et nœuds Azure « rupture ». Consultez le [script de déploiement HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) pour déployer rapidement un cluster HPC Pack dans Azure machines virtuelles, ou utiliser un [modèle de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12** - le script actuellement dépend de cette version spécifique de PowerShell Azure. Si vous exécutez une version ultérieure sur le nœud principal, vous devrez peut-être mettre à niveau vers la [version 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) pour exécuter le script PowerShell Azure. 

* **Pour un cluster avec les nœuds rupture Azure** - exécuter le script sur un ordinateur client où HPC Pack est installé, ou sur le nœud principal. Si en cours d’exécution sur un ordinateur client, assurez-vous que vous définissez la variable $env : CCP_SCHEDULER correctement pour pointer vers le nœud de tête. Les nœuds Azure « rupture » doivent déjà être ajoutés au cluster, mais ils peuvent être à l’état déployé non.


* **Pour un cluster déployée dans Azure machines virtuelles** - exécuter le script dans le nœud de tête machine virtuelle, car elle dépend du **Début HpcIaaSNode.ps1** et **Arrêter HpcIaaSNode.ps1** scripts qui y sont installés. En outre, ces scripts exiger un certificat de gestion Azure ou publier le fichier de paramètres (voir [Gérer les nœuds dans un cluster HPC Pack dans Azure de calcul](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Vérifiez que tout le nœud de calcul que vous avez besoin de machines virtuelles sont déjà ajoutés au cluster. Ils peuvent être à l’état arrêté.

### <a name="syntax"></a>Syntaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>Paramètres

 * **NodeTemplates** - noms des modèles nœud pour définir l’étendue des nœuds agrandir et réduire. Sauf indication contraire (la valeur par défaut est @()), tous les nœuds dans le groupe nœud **AzureNodes** sont dans la portée lorsque **NodeType** a la valeur AzureNodes, et tous les nœuds dans le groupe nœud **ComputeNodes** d’étendue lorsque **NodeType** a la valeur ComputeNodes.

 * **JobTemplates** - noms des modèles de tâche pour définir l’étendue des nœuds à agrandir.

 * **NodeType** - le type de nœud à agrandir et réduire. Valeurs prises en charge sont :

     * **AzureNodes** – pour les nœuds Azure PaaS (rupture) dans un local ou un cluster IaaS Azure.

     * **ComputeNodes** - cluster nœud machines virtuelles uniquement dans un cluster IaaS Azure.

* **NumOfQueuedJobsPerNodeToGrow** - nombre de travaux en file d’attente requis pour développer un nœud.

* **NumOfQueuedJobsToGrowThreshold** - le nombre de travaux en file d’attente pour démarrer le processus de croissance.

* **NumOfActiveQueuedTasksPerNodeToGrow** - le nombre de tâches en file d’attente actives requises pour développer un nœud. Si **NumOfQueuedJobsPerNodeToGrow** est spécifié avec une valeur supérieure à 0, ce paramètre est ignoré.

* **NumOfActiveQueuedTasksToGrowThreshold** - le nombre de tâches en file d’attente actives pour démarrer le processus de croissance.

* **NumOfInitialNodesToGrow** - le nombre minimal initial des nœuds pour agrandir si tous les nœuds dans la portée soient **Déployé Not** ou **arrêté (Deallocated)**.

* **GrowCheckIntervalMins** - l’intervalle en minutes entre les vérifications agrandir.

* **ShrinkCheckIntervalMins** - l’intervalle en minutes entre les vérifications à réduire.

* **ShrinkCheckIdleTimes** - le nombre de continue réduire tous les tests (séparés par **ShrinkCheckIntervalMins**) pour indiquer les nœuds sont inactifs.

* **UseLastConfigurations** - configurations précédentes enregistrées dans le fichier argument.

* **ArgFile**- le nom du fichier argument utilisé pour enregistrer et mettre à jour les configurations pour exécuter le script.

* **Préfixe_du_fichier_journal** - le nom de préfixe du fichier journal. Vous pouvez spécifier un chemin d’accès. Par défaut, le journal est écrit dans le répertoire de travail en cours.

### <a name="example-1"></a>Exemple 1

L’exemple suivant configure les nœuds rupture Azure déployés avec le modèle AzureNode par défaut pour agrandir et réduire automatiquement. Si tous les nœuds soient initialement dans un état **Déployé Not** , au moins 3 nœuds sont démarrées. Si le nombre de travaux en file d’attente dépasse 8, le script démarre nœuds jusqu'à ce que leur nombre dépasse le taux de travaux en file d’attente à **NumOfQueuedJobsPerNodeToGrow**. Si un nœud est identifié comme inactif dans 3 heures d’inactivité consécutives, il est arrêté.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemple 2

L’exemple suivant configure le nœud de calcul Azure machines virtuelles déployés avec le modèle ComputeNode par défaut pour agrandir et réduire automatiquement.
Les tâches configurés par le modèle de tâche par défaut définissent l’étendue de la charge de travail sur le cluster. Si tous les nœuds sont arrêtés initiale, au moins 5 nœuds sont démarrées. Si le nombre de tâches en file d’attente actives est supérieur à 15, le script démarre nœuds jusqu'à ce que leur nombre dépasse le rapport entre les tâches actives en file d’attente à **NumOfActiveQueuedTasksPerNodeToGrow**. Si un nœud est identifié comme inactif dans 10 fois inactifs consécutives, il est arrêté.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
