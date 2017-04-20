<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM sur un site VMM secondaire à l’aide de PowerShell (responsable de ressources) | Microsoft Azure"
    description="Décrit comment déployer récupération de Site Azure pour organiser réplication, le basculement et récupération machines virtuelles Hyper-V en nuages VMM à un site VMM secondaire à l’aide de PowerShell (responsable de ressources)"
    services="site-recovery"
    documentationCenter=""
    authors="sujaytalasila"
    manager="rochakm"
    editor="raynew"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="sutalasi"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Réplication machines virtuelles Hyper-V dans nuages VMM à un site VMM secondaire à l’aide de PowerShell (responsable de ressources)

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bienvenue dans récupération de Site Azure ! Utilisation de cet article si vous souhaitez répliquer machines virtuelles de Hyper-V local géré dans nuages System Center Virtual Machine Manager (VMM) vers un site secondaire. 

Cet article vous explique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez récupération de Site Azure répliquer machines virtuelles de Hyper-V dans nuages System Center VMM à nuages de System Center VMM dans sites secondaire.

L’article inclut les conditions préalables pour le scénario et vous montre 

- Comment configurer un archivage sécurisé des Services de récupération
- Installer le fournisseur de récupération Azure Site sur le serveur VMM source et le serveur VMM cible
- Enregistrer l’ou les serveurs VMM dans l’archivage sécurisé
- Configurer la stratégie de réplication pour le nuage VMM. Les paramètres de réplication de la stratégie est appliquées à toutes les machines virtuelles protégées 
- Activer la protection pour les ordinateurs virtuels. 
- Tester le basculement d’ordinateurs virtuels individuellement ou dans le cadre d’un plan de récupération pour vous assurer que tout fonctionne comme prévu.
- Effectuer un planifiée ou un basculement non planifié de machines virtuelles individuellement ou dans le cadre d’un plan de récupération pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour installer ce scénario, publiez vos questions sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Cet article décrit le modèle de déploiement du Gestionnaire de ressources.



## <a name="on-premises-prerequisites"></a>Conditions préalables pour les locaux

Voici ce que vous devez dans les sites principaux et secondaires en local pour déployer ce scénario :

**Conditions préalables** | **Plus d’informations** 
--- | ---
**VMM** | Nous vous recommandons de que vous déployez un serveur VMM dans le site principal et un serveur VMM dans le site secondaire.<br/><br/> Vous pouvez également [répliquer entre nuages sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous devez au moins deux nuages configurés sur le serveur VMM.<br/><br/> Serveurs VMM doivent s’exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit avoir un ou plusieurs nuages configurés et tous les nuages doivent avoir le profil de capacité Hyper-V. <br/><br/>Nuages doivent contenir un ou plusieurs groupes hôtes VMM.<br/><br/>En savoir plus sur la configuration nuages VMM lors de la [configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), et [procédure pas à pas : création de nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Serveurs VMM doivent avoir accès à internet. 
**Hyper-V** | Serveurs Hyper-V doivent exécuter au minimum Windows Server 2012 avec le rôle Hyper-V et ont installé les dernières mises à jour.<br/><br/> Un serveur Hyper-V doit contenir un ou plusieurs ordinateurs virtuels.<br/><br/>  Serveurs hôte Hyper-V doivent être placés dans des groupes hôtes dans les nuages VMM principales et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012 R2 vous devez installer la [mise à jour 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012 Remarque Ce intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. [Pour en savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fournisseur** | Pendant le déploiement de récupération de Site que vous installez le fournisseur de récupération de Site Azure sur VMM serveurs. Le fournisseur communique avec récupération de Site via HTTPS 443 pour harmoniser réplication. Réplication de données se produise entre les serveurs Hyper-V principales et secondaires sur le réseau local ou une connexion VPN.<br/><br/> Le fournisseur en cours d’exécution sur le serveur VMM a besoin d’accéder à ces URL : *. hypervrecoverymanager.windowsazure.com ; *. AccessControl.Windows.NET ; *. backup.windowsazure.com ; *. BLOB.Core.Windows.NET ; *. store.core.windows.net.<br/><br/> En outre autoriser communications pare-feu à partir des serveurs VMM vers les [plages d’adresses IP centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autoriser le protocole HTTPS (443).

### <a name="network-mapping-prerequisites"></a>Conditions préalables mappage réseau
Cartes de mappage réseau entre les réseaux VMM VM sur les serveurs VMM principales et secondaires pour :

- Placez optimale machines virtuelles réplica sur secondaires hôtes Hyper-V après le basculement.
- Connecter réplica machines virtuelles aux réseaux de machine virtuelle appropriés.
- Si vous ne configurez réplica de mappage réseau que machines virtuelles ne sont pas connectés à un réseau après le basculement.
- Si vous voulez configurer réseau mappage lors de la récupération de Site déploiement ici est ce que vous devez :

    - Assurez-vous que machines virtuelles sur le serveur source Hyper-V hôte sont connectés à un réseau VMM VM. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
    - Vérifiez qu’un réseau machine virtuelle correspondant configuré sur le cloud secondaire que vous utiliserez pour la récupération. Ce réseau machine virtuelle doit être lié à un réseau logique associée le cloud secondaire.


En savoir plus sur la configuration des réseaux VMM dans les articles suivants

- [Comment configurer des réseaux logiques dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Comment configurer des réseaux machine virtuelle et passerelles dans VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[En savoir plus](site-recovery-network-mapping.md) sur le fonctionne de mappage réseau.

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

1. Créer un groupe de ressources du Gestionnaire de ressources Azure si vous n’en avez pas déjà

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Créer un archivage sécurisé Services de récupération des nouveaux et enregistrer l’objet de l’archivage sécurisé de récupération automatique du système créé dans une variable (seront utilisés ultérieurement). Vous pouvez également récupérer la création d’un billet d’objet de l’archivage sécurisé récupération automatique du système à l’aide de l’applet de commande Get-AzureRMRecoveryServicesVault :-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de l’archivage sécurisé des Services de récupération

1.  Si vous avez un archivage sécurisé déjà créé, exécutez l’en dessous de commande pour obtenir l’archivage sécurisé.

        $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname

2.  Définir le contexte de l’archivage sécurisé en exécutant l’en dessous de la commande.

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


## <a name="step-5-create-and-associate-a-replication-policy"></a>Étape 5 : Créer et associer une stratégie de réplication

1.  Créer une stratégie de réplication Hyper-V 2012 R2 en exécutant la commande suivante :

    
        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod 

    > [AZURE.NOTE] Le nuage VMM peut contenir des hôtes Hyper-V exécutant différentes versions de Windows Server (comme indiqué dans les conditions préalables Hyper-V), mais la stratégie de réplication est la version du système d’exploitation spécifique. Si vous avez hôtes en cours d’exécution sur différents systèmes d’exploitation, puis créer des stratégies de réplication distincte pour chaque type de version du système d’exploitation. Pour, par exemple : Si vous avez cinq hôtes s’exécutant sur Windows serveurs 2012 et trois sur Windows Server 2012 R2, créez deux règles réplication – un pour chaque type de versions des systèmes d’exploitation.

2.  Obtenir le conteneur principale protection (primaire VMM Cloud) et le conteneur de protection récupération (récupération nuage VMM) en exécutant les commandes suivantes :
    
        $PrimaryCloud = "testprimarycloud"
        $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

        $RecoveryCloud = "testrecoverycloud"
        $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
  
3.  Extraire la stratégie que vous avez créé à l’étape 1 en utilisant le nom convivial de la stratégie

        $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.  Démarrer l’association du conteneur protection (VMM Cloud) avec la stratégie de réplication :

        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer

5.  Attendre la fin du travail association stratégie. Vous pouvez vérifier si la tâche est terminée à l’aide de l’extrait de PowerShell suivant.
   
        $job = Get-AzureRmSiteRecoveryJob -Job $associationJob
        if($job -eq $null -or $job.StateDescription -ne "Completed")
         {
            $isJobLeftForProcessing = $true;
        }

    Une fois que la tâche est terminée, exécutez la commande suivante :

        if($isJobLeftForProcessing)
        {
        Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)


Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).

## <a name="step-5-configure-network-mapping"></a>Étape 5 : Configurer le mappage de réseau

1. La commande première Obtient des serveurs pour l’archivage sécurisé récupération de Site Azure active. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable de tableau $Servers.

        $Servers = Get-AzureRmSiteRecoveryServer

2. La sous commandes obtenir le réseau de récupération de site pour le serveur VMM source et le serveur VMM cible.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    
    > [AZURE.NOTE] Le serveur VMM source peut être la première partie ou l’autre dans le tableau de serveurs. Vérifiez les noms des serveurs VMM et obtenir les réseaux correctement


4. L’applet de commande final crée un mappage entre le réseau principal et la récupération. L’applet de commande spécifie le réseau principal comme le premier élément de $PrimaryNetworks et le réseau de récupération comme le premier élément de $RecoveryNetworks.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-6-configure-storage-mapping"></a>Étape 6 : Configurer le mappage de stockage

1. La sous commande Obtient la liste des classifications de stockage dans $storageclassifications variable.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification


2. La sous commandes obtenir la classification source en classification variable et cibles $SourceClassificaion dans $TargetClassification variable. 

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    
    > [AZURE.NOTE] Les classifications des sources et cibles peuvent être n’importe quel élément dans le tableau. Référence à la sortie de l’en dessous de commande pour déterminer l’index de classifications source et cible dans la matrice $storageclassifications. 
    
    > Get-AzureRmSiteRecoveryStorageClassification | Sélectionnez-Object - propriété FriendlyName, Id | Format du tableau


3. L’en dessous de l’applet de commande crée un mappage entre la classification source et la classification cible. 

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-7-enable-protection-for-virtual-machines"></a>Étape 7 : Activer la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection des machines virtuelles dans le cloud. 


  1. Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :
    
            $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
    
  2. Obtenir l’entité de protection (machine virtuelle) en exécutant la commande suivante :
    
            $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
        
  3. Activer la réplication de la machine virtuelle en exécutant la commande suivante :

            $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy


## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle, ou créer un plan de récupération constituée de plusieurs machines virtuelles et exécuter un basculement de test pour le plan. Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. 

> [AZURE.NOTE] Vous pouvez créer un plan de récupération pour votre application dans Azure portail.

Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).


### <a name="run-a-test-failover"></a>Exécuter un basculement de test

1.  Exécuter la ci-dessous pour accéder au réseau de machine virtuelle à laquelle vous voulez tester le basculement vos ordinateurs virtuels pour les applets de commande.

        $Servers = Get-AzureRmSiteRecoveryServer
        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2.  Effectuer un test de basculement d’un ordinateur virtuel en procédant comme suit :
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1] 

2.  Effectuer le basculement d’un plan de test en procédant comme suit :
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1] 

### <a name="run-a-planned-failover"></a>Exécuter un basculement planifié

1. Effectuez un basculement planifié d’un ordinateur virtuel en procédant comme suit :
    
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. Effectuez un basculement planifié d’un plan de récupération en procédant comme suit :
    
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié

1. Effectuez un basculement non planifié d’un ordinateur virtuel en procédant comme suit :
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 

2.Cliquez effectuer un basculement non planifié d’un plan de récupération en procédant comme suit :
        
        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity 
    
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

