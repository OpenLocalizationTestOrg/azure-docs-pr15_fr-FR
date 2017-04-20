<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM à l’aide de PowerShell (responsable de ressources) et récupération de Site Azure | Microsoft Azure"
    description="Réplication machines virtuelles Hyper-V dans nuages VMM à l’aide de PowerShell et récupération de Site Azure"
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="rajanaki"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>Réplication machines virtuelles Hyper-V dans nuages VMM à Azure à l’aide de PowerShell et le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)



## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles dans un certain nombre de scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement consultez la [Présentation de la récupération de Site Azure](site-recovery-overview.md).

Cet article vous explique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez récupération de Site Azure répliquer machines virtuelles de Hyper-V dans nuages System Center VMM au stockage Azure.

L’article inclut les conditions préalables pour le scénario et vous montre

- Comment configurer un archivage sécurisé des Services de récupération
- Installer le fournisseur de récupération Azure Site sur le serveur VMM source
- Enregistrer le serveur dans l’archivage sécurisé, ajouter un compte de stockage Azure
- Installer l’agent de Services de récupération Azure sur vos serveurs hôtes Hyper-V
- Configurer les paramètres de protection pour les nuages VMM, qui seront appliquées à toutes les machines virtuelles protégées
- Activer la protection pour ces machines virtuelles.
- Tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour installer ce scénario, publiez vos questions sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article décrit l’utilisation du modèle de déploiement du Gestionnaire de ressources.

## <a name="before-you-start"></a>Avant de commencer

Vérifiez que vous respectez ces conditions préalables en place :

### <a name="azure-prerequisites"></a>Conditions préalables Azure

- Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Si vous n’en avez pas, commencez avec un [compte gratuit](https://azure.microsoft.com/free). En outre, vous pouvez lire sur le [Gestionnaire de récupération de Site Azure tarifs](https://azure.microsoft.com/pricing/details/site-recovery/).
- Vous devez un abonnement fournisseur si vous évaluez la réplication à un scénario d’abonnement fournisseur. En savoir plus sur le programme de fournisseur dans la [procédure d’inscription dans le programme de fournisseur](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Vous devez posséder un compte de stockage (responsable de ressources) v2 Azure pour stocker les données répliquées vers Azure. Le compte doit geo-réplication est activée. Il doit se trouver dans la même région en tant que le service de récupération de Site Azure et être associé le même abonnement ou l’abonnement fournisseur. Pour plus d’informations sur la configuration de stockage Azure, consultez [Présentation de Microsoft Azure stockage](../storage/storage-introduction.md) pour référence.
- Vous devez vous assurer que machines virtuelles à protéger respectez les [conditions préalables Azure machine virtuelle](site-recovery-best-practices.md#azure-virtual-machine-requirements).

> [AZURE.NOTE] Pour l’instant, seules les opérations de niveau machine virtuelle sont possibles via Powershell. Prise en charge pour les opérations de niveau de plan de récupération est bientôt.  Pour l’instant, vous êtes limité à la réalisation fail récupérations seulement à une précision « protégée machine virtuelle » et pas à un niveau de planifier la récupération.

### <a name="vmm-prerequisites"></a>Conditions préalables VMM
- Vous devez serveur VMM s’exécutant sur System Center 2012 R2.
- N’importe quel serveur VMM contenant les machines virtuelles à protéger doit exécuter le fournisseur de récupération de Site Azure. Il est installé au cours du déploiement d’Azure Site récupération.
- Vous devez au moins un nuage sur le serveur VMM à protéger. Le cloud doit contenir :
    - Un ou plusieurs groupes hôtes VMM.
    - Un ou plusieurs serveurs hôtes Hyper-V ou groupes dans chaque groupe hôte.
    - Une ou plusieurs machines virtuelles sur le serveur source Hyper-V.
- En savoir plus sur la configuration nuages VMM :
    - En savoir plus sur les nuages VMM privés dans [What's New dans le nuage privé avec System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) et [VMM 2012 et les nuages](http://go.microsoft.com/fwlink/?LinkId=324956).
    - En savoir plus sur la [configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
    - Une fois vos éléments TISSU cloud en place savoir comment créer des nuages privés dans [Création d’un nuage privé dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) et du [procédure pas à pas : création de nuages privés avec System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### <a name="hyper-v-prerequisites"></a>Conditions préalables Hyper-V

- Les serveurs hôtes Hyper-V doivent exécuter au minimum **Windows Server 2012** avec rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et ont installé les dernières mises à jour.
- Si vous exécutez Hyper-V dans une note cluster ce intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. Pour
- Pour obtenir des instructions, voir [comment configurer Hyper-V réplica intermédiaire](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
- Tous les serveurs de hôte Hyper-V ou cluster pour lequel vous souhaitez gérer la protection doit être inclus dans un nuage VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables mappage réseau
Lorsque vous protégez les machines virtuelles dans Azure, les cartes de mappage réseau les réseaux de la machine virtuelle sur le serveur VMM source et ciblez Azure réseaux pour activer ce qui suit :

- Tous les ordinateurs qui basculent sur le même réseau peuvent se connecter à d’autres, quel que soit le plan de récupération elles se trouvent dans.
- Si une passerelle réseau est configuré sur la réseau Azure cible, machines virtuelles peuvent se connecter aux autres machines virtuelles en local.
- Si vous ne configurez mappage réseau, uniquement les ordinateurs virtuels basculer dans le même plan de récupération ne pourrez pas vous connecter à l’autre après le basculement vers Azure.

Si vous voulez déployer mappage réseau, vous devez les éléments suivants :

- Machines virtuelles à protéger sur le serveur VMM source doit être connectés à un réseau machine virtuelle. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous pouvez de sélectionner ce réseau au moment de basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure Site récupération.

Pour plus d’informations sur le mappage réseau dans

- [Comment configurer des réseaux logiques dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Comment configurer des réseaux machine virtuelle et passerelles dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Comment configurer et surveiller des réseaux virtuels dans Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###<a name="powershell-prerequisites"></a>Conditions préalables PowerShell
Vérifiez que vous disposez de PowerShell Azure prêt à l’envoi. Si vous utilisez déjà PowerShell, vous devez mise à niveau vers la version 0.8.10 ou version ultérieure. Pour plus d’informations sur la configuration de PowerShell, consultez le [Guide pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Une fois que vous avez configuré et configuré PowerShell, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour connaître les conseils pour qu'utiliser les applets de commande, tels que des sorties, les entrées et les valeurs de paramètre sont généralement traitement dans Azure PowerShell, voir le [Guide de prise en main applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définir l’abonnement

1. À partir de powershell Azure, connectez-vous à votre compte Azure : à l’aide des applets de commande

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred


2. Obtenir une liste de vos abonnements. Cela permet de répertorier également la subscriptionIDs pour chacun des abonnements. Note vers le bas le subscriptionID de l’abonnement dans lequel vous voulez créer la récupération des services l’archivage sécurisé

        Get-AzureRmSubscription

3. Définir l’abonnement dans lequel l’archivage sécurisé des services de récupération doit être créé en mentionnant l’ID de l’abonnement

        Set-AzureRmContext –SubscriptionID <subscriptionId>


## <a name="step-2-create-a-recovery-services-vault"></a>Étape 2 : Créer un archivage sécurisé aux Services de récupération

1. Créer un groupe de ressources dans le Gestionnaire de ressources Azure si vous n’en avez pas déjà

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Créer un archivage sécurisé Services de récupération des nouveaux et enregistrer l’objet de l’archivage sécurisé de récupération automatique du système créé dans une variable (seront utilisés ultérieurement). Vous pouvez également récupérer la création d’un billet d’objet de l’archivage sécurisé récupération automatique du système à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de l’archivage sécurisé des Services de récupération

1.  Définir le contexte de l’archivage sécurisé en exécutant l’en dessous de la commande.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installer le fournisseur de récupération de Site Azure

1.  Sur l’ordinateur VMM, créez un répertoire en exécutant la commande suivante :

        New-Item c:\ASR -type directory

2.  Extraire les fichiers à l’aide de fournisseur téléchargé en exécutant la commande suivante

        pushd C:\ASR\
        .\AzureSiteRecoveryProvider.exe /x:. /q


3.  Installer le fournisseur à l’aide des commandes suivantes :

        .\SetupDr.exe /i
        $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
        do
        {
                        $isNotInstalled = $true;
                        if(Test-Path $installationRegPath)
                        {
                                        $isNotInstalled = $false;
                        }
        }While($isNotInstalled)

    Attendez que l’installation se termine.

4.  Inscrire le serveur dans l’archivage sécurisé à l’aide de la commande suivante :

        $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
        pushd $BinPath
        $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice


## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Créer un compte de stockage Azure

1. Si vous n’avez pas un compte de stockage Azure, créez un compte geo-réplication est activée dans la même geo en tant que l’archivage sécurisé en exécutant la commande suivante :

        $StorageAccountName = "teststorageacc1" #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"  
        $ResourceGroupName =  “myRG”            #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Notez que le compte de stockage doit se trouver dans la même région en tant que le service de récupération de Site Azure et être associés à la même abonnement.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installer l’Agent de Services de récupération Azure

1. Téléchargez l’agent de Services de récupération Azure [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) et installez-le sur chaque serveur hôte Hyper-V situé dans les nuages VMM à protéger.

2. Exécutez la commande suivante sur tous les hôtes VMM :

    marsagentinstaller.exe/q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configurez cloud paramètres de protection

1.  Créer une stratégie de réplication de Azure en exécutant la commande suivante :


        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.  Obtenir un conteneur de protection en exécutant les commandes suivantes :

        $PrimaryCloud = "testcloud"
        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

3.  Obtenir les détails de la stratégie à une variable à l’aide de la tâche qui a été créée et mentionner le nom convivial de stratégie :

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Démarrer l’association du conteneur protection avec la stratégie de réplication :

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.  Une fois la tâche terminée, exécutez la commande suivante :

        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
        $isJobLeftForProcessing = $true;
        }

6.  Une fois que la tâche est terminée, exécutez la commande suivante :

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)

Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configurer le mappage de réseau

Avant de commencer mappage réseau Vérifiez que machines virtuelles sur le serveur VMM source sont connectés à un réseau machine virtuelle. En outre, créez un ou plusieurs réseaux virtuels Azure.

Pour plus d’informations sur la création d’un réseau virtuel en utilisant le Gestionnaire de ressources Azure et PowerShell, [créer un réseau virtuel avec une connexion VPN site à site à l’aide du Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Notez que plusieurs réseaux Machine virtuelle peuvent être mappées à un seul réseau Azure. Si le réseau cible comporte plusieurs sous réseaux de ces sous-réseaux ait le même nom que sous-réseau sur lequel se trouve l’ordinateur virtuel source, la machine virtuelle réplica est connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.

1. La commande première Obtient des serveurs pour l’archivage sécurisé récupération de Site Azure active. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable de tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. La deuxième commande Obtient le réseau de récupération de site pour le premier serveur de la matrice $Servers. La commande stocke les réseaux dans la variable $Networks.


        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. La troisième commande obtient Azure réseaux virtuels, puis cette valeur dans la variable $AzureVmNetworks.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. L’applet de commande final crée un mappage entre le réseau principal et le réseau machine virtuelle Azure. L’applet de commande spécifie le réseau principal comme le premier élément de $Networks. L’applet de commande spécifie un réseau machine virtuelle comme le premier élément de $AzureVmNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activer la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection des machines virtuelles dans le cloud.

 Notez les points suivants :

 - Machines virtuelles doit répondre aux exigences Azure. Vérifier ces paramètres dans les [conditions préalables et prise en charge](site-recovery-best-practices.md) dans le guide de planification.

 - Pour activer la protection, le système d’exploitation et le système d’exploitation les propriétés d’un disque doivent être définies pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l’aide d’un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des propriétés machine virtuelle. Si vous ne définissez ces propriétés dans VMM vous pourrez les configurer dans le portail de récupération de Site Azure.


  1. Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :

            $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName

  2. Obtenir l’entité de protection (machine virtuelle) en exécutant la commande suivante :

            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer

  3. Activer la DR pour la machine virtuelle en exécutant la commande suivante :

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement test pour une seule machine virtuelle, ou créer un plan de récupération constituée de plusieurs machines virtuelles et exécuter un basculement test pour le plan. Basculement test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous voulez vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance après le basculement, activer connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le basculement de test.
- Après le basculement, vous allez utiliser une adresse IP publique pour vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance. Si vous voulez faire, assurez-vous que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.

Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).


### <a name="run-a-test-failover"></a>Exécuter un basculement de test

1.  Démarrer le basculement de test en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié

1. Démarrer le basculement planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

1. Démarrer le basculement non planifié en exécutant la commande suivante :

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  


## <a name=monitor></a>Surveiller l’activité

Utilisez les commandes suivantes pour surveiller l’activité. Notez que vous devez attendre entre les tâches pour le traitement terminer.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Étapes suivantes

[Pour en savoir plus](https://msdn.microsoft.com/library/azure/mt637930.aspx) sur la récupération de Site Azure avec les applets de commande PowerShell du Gestionnaire de ressources Azure.
