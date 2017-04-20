<properties
    pageTitle="Protéger les serveurs vers Azure à l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure | Microsoft Azure"
    description="Automatiser la protection des serveurs à Azure avec récupération de Site Azure à l’aide de PowerShell et Azure le Gestionnaire de ressources."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>

# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>Répliquer entre machines virtuelles de Hyper-V en local et Azure à l’aide de PowerShell et le Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-deploy-with-powershell-resource-manager.md)
- [Portail classique](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>Vue d’ensemble

Récupération de Site Azure contribue à votre stratégie de récupération de continuité et de reprise entreprise en organiser réplication, basculement et la restauration des machines virtuelles dans un certain nombre de scénarios de déploiement. Pour une liste complète des scénarios de déploiement, consultez la [Présentation de la récupération de Site Azure](site-recovery-overview.md).

PowerShell Azure est un module qui fournit des applets de commande pour gérer Azure à utiliser Windows PowerShell. Il peut fonctionner avec deux types de modules : le module Azure profil ou le module Azure le Gestionnaire de ressources.

Site récupération applets de commande PowerShell, disponibles avec Azure PowerShell pour le Gestionnaire de ressources Azure, vous aident à protéger et récupérer vos serveurs dans Azure.

Cet article décrit comment utiliser Windows PowerShell, ainsi que le Gestionnaire de ressources Azure, pour déployer récupération de Site pour configurer et organiser la protection des serveurs à Azure. L’exemple utilisée dans cet article vous montre comment protéger, basculer et restaurer des machines virtuelles sur un hôte Hyper-V vers Azure, à l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure.

> [AZURE.NOTE] Les applets de commande PowerShell de récupération de Site actuellement permettent de configurer les éléments suivants : un site du Gestionnaire de Machine virtuelle à l’autre, un site du Gestionnaire de Machine virtuelle vers Azure et un site de Hyper-V vers Azure.

Vous n’êtes pas obligé d’être un expert PowerShell pour utiliser cet article, mais vous n’avez pas besoin de comprendre les concepts de base, telles que les modules, les applets de commande et les sessions. Pour plus d’informations sur Windows PowerShell, voir [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Vous pouvez également en savoir plus sur [l’Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

> [AZURE.NOTE] Les partenaires Microsoft qui font partie du programme Cloud Solution fournisseur (fournisseur) peuvent configurer et gérer la protection des serveurs de leurs clients à respectifs fournisseur abonnements (client) leurs clients.

## <a name="before-you-start"></a>Avant de commencer

Vérifiez que vous respectez ces conditions préalables en place :

- Un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). En outre, vous pouvez lire sur [Gestionnaire de récupération de Site Azure tarifs](https://azure.microsoft.com/pricing/details/site-recovery/).
- Azure PowerShell 1.0. Pour plus d’informations sur cette version et comment l’installer, consultez [Azure PowerShell 1.0.](https://azure.microsoft.com/)
- Les modules [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) et [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) . Vous pouvez obtenir les dernières versions de ces modules dans la [Galerie de PowerShell](https://www.powershellgallery.com/)

Cet article montre comment utiliser Powershell Azure avec le Gestionnaire de ressources Azure pour configurer et gérer la protection de vos serveurs. L’exemple utilisée dans cet article vous montre comment protéger une machine virtuelle, en cours d’exécution sur un hôte Hyper-V, Azure. Les composants requis suivants sont spécifiques à cet exemple. Pour un ensemble plus complet de la configuration requise pour les différents scénarios de récupération de Site, reportez-vous à la documentation relative à ce scénario.

- Un hôte Hyper-V exécutant Windows Server 2012 R2 ou Microsoft Hyper-V Server 2012 R2 contenant un ou plusieurs machines virtuelles.
- Serveurs Hyper-V connecté à Internet, directement ou via un proxy.
- Machines virtuelles à protéger doivent être conformes avec les [conditions préalables Machine virtuelle](site-recovery-best-practices.md#virtual-machines).


## <a name="step-1-sign-in-to-your-azure-account"></a>Étape 1 : Se connecter à votre compte Azure


1. Ouvrez une console PowerShell et exécutez cette commande pour vous connecter à votre compte Azure. L’applet de commande pour faire apparaître une page web qui vous invite à entrer vos informations d’identification de compte.

        Login-AzureRmAccount

    Sinon, vous pouvez également inclure vos informations d’identification de compte en tant que paramètre à la `Login-AzureRmAccount` applet de commande, à l’aide de la `-Credential` paramètre.

    Si vous êtes partenaire fournisseur collaborant au nom d’un client, spécifiez le client comme un client, à l’aide de son nom de domaine principal tenantID ou client.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. Un compte peut avoir plusieurs abonnements, afin que vous devez associer l’abonnement que vous voulez utiliser avec le compte.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Vérifiez que votre abonnement est inscrit pour utiliser les Azure fournisseurs de Services de récupération et de restauration de Site, à l’aide des commandes suivantes :

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    Dans le résultat de ces commandes, si la **RegistrationState** est défini sur **inscrit**, vous pouvez passer à l’étape 2. Si ce n’est pas le cas, vous devez enregistrer le fournisseur manquant dans votre abonnement.

    Pour enregistrer le fournisseur Azure pour la récupération de Site et les Services de récupération, exécutez les commandes suivantes :

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    Vérifiez que les fournisseurs enregistré correctement à l’aide des commandes suivantes : `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` et `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.



## <a name="step-2-set-up-the-recovery-services-vault"></a>Étape 2 : Configurer l’archivage sécurisé aux Services de récupération

1. Créer un groupe de ressources Azure le Gestionnaire de ressources dans lequel vous allez créer l’archivage sécurisé, ou utiliser un groupe de ressources existant. Vous pouvez créer un nouveau groupe de ressources à l’aide de la commande suivante :

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    où la variable $ResourceGroupName contient le nom du groupe de ressources que vous voulez créer, et la variable $Geo contient la région Azure dans lequel vous souhaitez créer le groupe de ressources (par exemple, « Brésil Sud »).

    Vous pouvez obtenir une liste des groupes de ressources dans votre abonnement à l’aide de la `Get-AzureRmResourceGroup` applet de commande.

2. Créez un archivage sécurisé Azure récupération Services nouveau comme suit :

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Vous pouvez récupérer la liste de chambres fortes existants à l’aide de la `Get-AzureRmRecoveryServicesVault` applet de commande.

> [AZURE.NOTE] Si vous souhaitez effectuer des opérations sur chambres fortes récupération de Site créés à l’aide du portail classique ou le module Azure Service gestion PowerShell, vous pouvez récupérer une liste de ces chambres fortes en utilisant la `Get-AzureRmSiteRecoveryVault` applet de commande. Vous devez créer un archivage sécurisé aux Services de récupération nouveau pour toutes les opérations de nouveau. Les chambres fortes récupération de Site que vous avez créé précédemment sont prises en charge, mais n’ont pas les dernières fonctionnalités.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Étape 3 : Définir le contexte de l’archivage sécurisé aux Services de récupération

1.  Définir le contexte de l’archivage sécurisé en exécutant la commande suivante :

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>Étape 4 : Créer un site Hyper-V et générer une nouvelle clé d’inscription de l’archivage sécurisé pour le site.

1. Créer un site Hyper-V comme suit :

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Cette applet de commande démarre une tâche de récupération de Site pour créer le site et renvoie un objet de travail de récupération de Site. Attendez que la tâche se termine et vérifiez que la tâche est terminée avec succès.

    Vous pouvez récupérer l’objet de travail et ainsi vérifier l’état actuel de la tâche, à l’aide de l’applet de commande Get-AzureRmSiteRecoveryJob.
2. Générer et téléchargez une clé d’enregistrement pour le site, comme suit :

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Copiez la clé téléchargée à l’hôte Hyper-V. Vous devez la clé pour enregistrer l’hôte Hyper-V pour le site.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>Étape 5 : Installer le fournisseur de récupération de Site Azure et Agent de Services de récupération Azure sur votre hôte Hyper-V

1. Téléchargez le programme d’installation de la dernière version du fournisseur à partir de [Microsoft](https://aka.ms/downloaddra).

2. Exécuter le programme d’installation sur votre hôte Hyper-V et à la fin de l’installation passez à l’étape de l’enregistrement.

3. Lorsque vous y êtes invité, fournir la clé d’inscription site téléchargé et terminer l’inscription de l’hôte Hyper-V pour le site.

4. Vérifiez que l’hôte Hyper-V est inscrit sur le site à l’aide de la commande suivante :

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>Étape 6 : Créer une stratégie de réplication et associer avec le conteneur de protection

1. Créer une stratégie de réplication comme suit :

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Vérifier le travail renvoyé pour vous assurer que la création de stratégie de réplication a réussi.

    >[AZURE.IMPORTANT] Le compte de stockage spécifié doit se trouver dans la même région Azure que votre l’archivage sécurisé aux Services de récupération et doit avoir geo-réplication est activée.
    >
    > - Si le compte de stockage de récupération spécifié est de type stockage Azure (standard), basculement des ordinateurs protégées récupérer l’ordinateur pour Azure IaaS (standard).
    > - Si le compte de stockage de récupération spécifié est de type stockage Azure (Azure le Gestionnaire de ressources), basculement des ordinateurs protégées récupérer l’ordinateur pour Azure IaaS (Azure le Gestionnaire de ressources).

2. Obtenir le conteneur de protection correspondant au site, comme suit :

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  Démarrez l’association du conteneur protection avec la stratégie de réplication, comme suit :

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    Attendre la fin du travail association et vérifiez qu’elle a réussi.

##<a name="step-7-enable-protection-for-virtual-machines"></a>Étape 7 : Activer la protection des machines virtuelles

1. Obtenir l’entité de protection correspondant à la machine virtuelle à protéger, comme suit :

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Commencer à protéger la machine virtuelle, comme suit :

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] Le compte de stockage spécifié doit se trouver dans la même région Azure que votre l’archivage sécurisé aux Services de récupération et doit avoir geo-réplication est activée.
    >
    > - Si le compte de stockage de récupération spécifié est de type stockage Azure (standard), basculement des ordinateurs protégées récupérer l’ordinateur pour Azure IaaS (standard).
    > - Si le compte de stockage de récupération spécifié est de type stockage Azure (Azure le Gestionnaire de ressources), basculement des ordinateurs protégées récupérer l’ordinateur pour Azure IaaS (Azure le Gestionnaire de ressources).

    > Si la machine virtuelle vous protégez possède plusieurs disques est jointe à, spécifiez le disque système d’exploitation en utilisant le paramètre *OSDiskName* .

3. Attendez que les ordinateurs virtuels d’atteindre un état protégé après la réplication initiale. Cela peut prendre un certain temps, selon certains facteurs tels que la quantité de données doivent être répliquées et la bande passante disponible en amont vers Azure. L’état de la tâche et ÉtatDescription sont mis à jour comme suit, lors de la machine virtuelle atteindre un état protégé.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. Mettre à jour les propriétés de récupération, telles que la taille de rôle de mémoire virtuelle et le réseau Azure pour joindre cartes réseau la machine virtuelle pour en cas de basculement.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>Étape 8 : Exécuter un basculement de test

1. Exécuter une tâche de basculement test, comme suit :

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Vérifiez que le test machine virtuelle est créée dans Azure. (La tâche basculement d’essai est suspendue, après avoir créé le test machine virtuelle dans Azure. La tâche se termine par nettoyer les défauts au niveau créés lors de la reprise de la tâche, comme illustré dans l’étape suivante.)

3. Complétez le basculement de test, comme suit :

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>Étapes suivantes

[Pour en savoir plus](https://msdn.microsoft.com/library/azure/mt637930.aspx) sur la récupération de Site Azure avec les applets de commande PowerShell du Gestionnaire de ressources Azure.
