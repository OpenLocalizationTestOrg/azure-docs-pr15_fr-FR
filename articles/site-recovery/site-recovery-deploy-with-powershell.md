<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM à l’aide de PowerShell et récupération de Site Azure | Microsoft Azure"
    description="Découvrez comment automatiser la réplication des machines virtuelles de Hyper-V dans nuages VMM à l’aide de PowerShell et récupération de Site."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Réplication machines virtuelles Hyper-V dans nuages VMM à Azure à l’aide de Powershell - classique

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portail classique](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classique](site-recovery-deploy-with-powershell.md)


## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles dans un certain nombre de scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement consultez la [Présentation de la récupération de Site Azure](site-recovery-overview.md).

Cet article vous explique comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez récupération de Site Azure répliquer machines virtuelles de Hyper-V dans nuages System Center VMM au stockage Azure.

L’article inclut les conditions préalables pour le scénario et explique comment configurer un archivage sécurisé récupération de Site, installez le fournisseur de récupération de Site Azure sur le serveur VMM source, inscrire le serveur dans l’archivage sécurisé, ajouter un compte de stockage Azure, installer l’agent de Services de récupération Azure sur vos serveurs hôtes Hyper-V, configurer les paramètres de protection pour nuages VMM qui seront appliquées à toutes les machines virtuelles protégées , puis activez la protection pour ces machines virtuelles. Terminer la configuration en testant le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour installer ce scénario, publiez vos questions sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article décrit l’utilisation du modèle de déploiement classique.



## <a name="before-you-start"></a>Avant de commencer

Vérifiez que vous respectez ces conditions préalables en place :

### <a name="azure-prerequisites"></a>Conditions préalables Azure

- Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- Vous devez posséder un compte de stockage Azure pour stocker les données dupliquées. Le compte doit geo-réplication est activée. Il doit se trouver dans la même région en tant que l’archivage sécurisé récupération de Site Azure et être associés à la même abonnement. [En savoir plus sur le stockage Azure](../storage/storage-introduction.md).
- Vous devez vous assurer que machines virtuelles à protéger répondent aux [conditions préalables Azure machine virtuelle](site-recovery-best-practices.md#virtual-machines).

### <a name="vmm-prerequisites"></a>Conditions préalables VMM
- Vous devez serveur VMM s’exécutant sur System Center 2012 R2.
- Vous devez au moins un nuage sur le serveur VMM à protéger. Le cloud doit contenir :
    - Un ou plusieurs groupes hôtes VMM.
    - Un ou plusieurs serveurs hôtes Hyper-V ou groupes dans chaque groupe hôte.
    - Une ou plusieurs machines virtuelles sur le serveur source Hyper-V.

### <a name="hyper-v-prerequisites"></a>Conditions préalables Hyper-V

- Les serveurs hôtes Hyper-V doivent exécuter au minimum **Windows Server 2012** avec rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et ont installé les dernières mises à jour.
- Si vous exécutez Hyper-V dans une note cluster ce intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. Pour ce faire, dans le Gestionnaire de serveur > Gestionnaire de Cluster de basculement, connectez-vous au cluster, cliquez sur **Configurer le rôle** et sélectionnez **Hyper-V réplica intermédiaire** dans l’écran **Sélectionnez le rôle** de l’Assistant haute disponibilité.
- Tous les serveurs de hôte Hyper-V ou cluster pour lequel vous souhaitez gérer la protection doit être inclus dans un nuage VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables mappage réseau
Lorsque vous protégez les machines virtuelles dans les cartes de mappage réseau Azure entre les réseaux machine virtuelle sur le serveur VMM source et ciblez Azure réseaux pour activer ce qui suit :

- Tous les ordinateurs qui basculent sur le même réseau peuvent se connecter à d’autres, quel que soit le plan de récupération elles se trouvent dans.
- Si une passerelle réseau est configuré sur la réseau Azure cible, machines virtuelles peuvent se connecter aux autres machines virtuelles en local.
- Si vous ne configurez pas réseau mappage uniquement machines virtuelles basculer dans le même plan de récupération sera peuvent se connecter à l’autre après le basculement vers Azure.

Si vous voulez déployer mappage réseau, vous devez les éléments suivants :

- Machines virtuelles à protéger sur le serveur VMM source doit être connectés à un réseau machine virtuelle. Ce réseau doit être lié à un réseau logique est associé avec le cloud.
- Un réseau Azure auquel les machines virtuelles dupliquées peuvent se connecter après le basculement. Vous pouvez de sélectionner ce réseau au moment de basculement. Le réseau doit être dans la même région en tant que votre abonnement Azure Site récupération.
- [En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau :

###<a name="powershell-prerequisites"></a>Conditions préalables PowerShell
Vérifiez que vous disposez de PowerShell Azure prêt à l’envoi. Si vous utilisez déjà PowerShell, vous devez mise à niveau vers la version 0.8.10 ou version ultérieure. Pour plus d’informations sur la configuration de PowerShell, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Une fois que vous avez configuré et configuré PowerShell, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour connaître les conseils pour qu'utiliser les applets de commande, tels que des sorties, les entrées et les valeurs de paramètre sont généralement traitement dans Azure PowerShell, voir [Prise en main applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définir l’abonnement

PowerShell, exécutez ces applets de commande :

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Remplacer les éléments dans le « < > » par vos informations spécifiques.

## <a name="step-2-create-a-site-recovery-vault"></a>Étape 2 : Créer un archivage sécurisé récupération de Site

Dans PowerShell, remplacez les éléments dans le « < > » par vos informations spécifiques et exécuter ces commandes :

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Étape 3 : Générer une clé d’inscription de l’archivage sécurisé

Générer une clé d’enregistrement dans l’archivage sécurisé. Après avoir téléchargé le fournisseur de récupération de Site Azure et installez-le sur le serveur VMM, vous allez utiliser cette clé pour inscrire le serveur VMM dans l’archivage sécurisé.

1.  Obtenir le fichier de configuration de l’archivage sécurisé et définir le contexte :

    ```

    $VaultName = "<testvault123>"
    $VaultGeo  = "<Southeast Asia>"
    $OutputPathForSettingsFile = "<c:\>"

    $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

    ```

2.  Définir le contexte de l’archivage sécurisé en exécutant les commandes suivantes :

    ```

    $VaultSettingFilePath = $vaultSetingsFile.FilePath
    $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

    ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installer le fournisseur de récupération de Site Azure

1.  Sur l’ordinateur VMM, créez un répertoire en exécutant la commande suivante :

    ```

    pushd C:\ASR\

    ```

2.  Extraire les fichiers à l’aide de fournisseur téléchargé en exécutant la commande suivante

    ```

    AzureSiteRecoveryProvider.exe /x:. /q

    ```

3.  Installer le fournisseur à l’aide des commandes suivantes :

    ```

    .\SetupDr.exe /i

    ```

    ```

    $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
    do
    {
        $isNotInstalled = $true;
        if(Test-Path $installationRegPath)
        {
            $isNotInstalled = $false;
        }
    }While($isNotInstalled)

    ```

    Attendez que l’installation se termine.

4.  Inscrire le serveur dans l’archivage sécurisé à l’aide de la commande suivante :

    ```

    $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
    pushd $BinPath
    $encryptionFilePath = "C:\temp\"
    .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

    ```

## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Créer un compte de stockage Azure

Si vous n’avez pas un compte de stockage Azure, créez un compte geo-réplication est activée en exécutant la commande suivante :

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Notez que le compte de stockage doit se trouver dans la même région en tant que le service de récupération de Site Azure et être associés à la même abonnement.


## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installer l’Agent de Services de récupération Azure

À partir du portail Azure, installez l’agent de Services de récupération Azure sur chaque serveur hôte Hyper-V situé dans les nuages VMM à protéger.

Exécutez la commande suivante sur tous les hôtes VMM :

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configurez cloud paramètres de protection

1.  Créer un profil de protection cloud et Azure en exécutant la commande suivante :

    ```

    $ReplicationFrequencyInSeconds = "300";
    $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds  $ReplicationFrequencyInSeconds;

    ```

2.  Obtenir un conteneur de protection en exécutant les commandes suivantes :

    ```

    $PrimaryCloud = "testcloud"
    $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

    ```

3.  Démarrer l’association du conteneur protection avec le cloud :

    ```

    $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

    ```

4.  Une fois la tâche terminée, exécutez la commande suivante :


        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


5.  Une fois que la tâche est terminée, exécutez la commande suivante :


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



Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configurer le mappage de réseau
Avant de commencer mappage réseau Vérifiez que machines virtuelles sur le serveur VMM source sont connectés à un réseau machine virtuelle. En outre, créez un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux machine virtuelle peuvent être mappées à un seul réseau Azure.

Notez que si le réseau cible a plusieurs sous réseaux et un de ces sous-réseaux a le même nom que sous-réseau sur lequel se trouve l’ordinateur virtuel source, puis la machine virtuelle réplica sera connectée à ce sous-réseau cible après le basculement. Si il n’est pas un sous-réseau cible ayant un nom correspondant, la machine virtuelle est connectée au premier sous-réseau dans le réseau.

La commande première Obtient des serveurs pour l’archivage sécurisé récupération de Site Azure active. La commande stocke les serveurs de récupération de Site Microsoft Azure dans la variable de tableau $Servers.

    $Servers = Get-AzureSiteRecoveryServer


La deuxième commande Obtient le réseau de récupération de site pour le premier serveur de la matrice $Servers. La commande stocke les réseaux dans la variable $Networks.


    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

La troisième commande obtient vos abonnements Azure à l’aide de l’applet de commande Get-AzureSubscription, puis stocke cette valeur dans la variable $Subscriptions.

    $Subscriptions = Get-AzureSubscription



La commande quatrième obtient Azure réseaux virtuels à l’aide de l’applet de commande Get-AzureVNetSite, puis cette valeur dans la variable $AzureVmNetworks.


    $AzureVmNetworks = Get-AzureVNetSite



L’applet de commande final crée un mappage entre le réseau principal et le réseau machine virtuelle Azure. L’applet de commande spécifie le réseau principal comme le premier élément de $Networks. L’applet de commande spécifie un réseau machine virtuelle comme le premier élément de $AzureVmNetworks à l’aide de son ID. La commande inclut votre ID d’abonnement Azure.


    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activer la protection des machines virtuelles

Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection des machines virtuelles dans le cloud. Notez les points suivants :

Machines virtuelles doit répondre aux [conditions préalables Azure machine virtuelle](site-recovery-best-practices.md#virtual-machines).

Pour activer la protection du système d’exploitation et le système d’exploitation les propriétés d’un disque doivent être définies pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l’aide d’un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et la **Configuration matérielle** des propriétés machine virtuelle. Si vous ne définissez ces propriétés dans VMM vous pourrez les configurer dans le portail de récupération de Site Azure.



1.  Pour activer la protection, exécutez la commande suivante pour obtenir le conteneur de protection :

        $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName



2. Obtenir l’entité de protection (machine virtuelle) en exécutant la commande suivante :


        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



3. Activer la DR pour la machine virtuelle en exécutant la commande suivante :



        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity  -Protection Enable -Force



## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle, ou créer un plan de récupération constituée de plusieurs machines virtuelles et exécuter un basculement de test pour le plan. Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez que :

- Si vous voulez vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance après le basculement, activer connexion Bureau à distance sur l’ordinateur virtuel avant d’exécuter le basculement de test.
- Après le basculement, vous allez utiliser une adresse IP publique pour vous connecter à la machine virtuelle dans Azure à l’aide de bureau à distance. Si vous voulez faire, assurez-vous que vous n’avez pas toutes les stratégies de domaine que vous empêchent de se connecter à une machine virtuelle à l’aide d’une adresse publique.

Pour vérifier la fin de l’opération, suivez les étapes de [Surveiller l’activité](#monitor).

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Créer un fichier .xml comme modèle à votre plan de récupération en utilisant les données ci-dessous et enregistrez-le en tant que « C:\RPTemplatePath.xml ».
2. Modifier l’Id de nœud RecoveryPlan, nom, PrimaryServerId et SecondaryServerId.
3. Modifier le nœud ProtectionEntity PrimaryProtectionEntityId (vmid à partir de VMM).
4. Vous pouvez ajouter davantage d’ordinateurs virtuels en ajoutant des nœuds ProtectionEntity plus.



        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"  PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"  SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03- cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"  Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"  After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



4. Renseignez les données dans le modèle :


        $TemplatePath = "C:\RPTemplatePath.xml";



5. Créer la RecoveryPlan :

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;



### <a name="run-a-test-failover"></a>Exécuter un basculement de test

1.  Obtenir l’objet RecoveryPlan en exécutant la commande suivante :

        $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;


2.  Démarrer le basculement de test en exécutant la commande suivante :


        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


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

[Pour en savoir plus](https://msdn.microsoft.com/library/dn850420.aspx) sur les applets de commande PowerShell de récupération de Site Azure. </a>.
