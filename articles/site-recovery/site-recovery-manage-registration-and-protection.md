<properties
    pageTitle="Supprimer des serveurs et désactiver la protection | Microsoft Azure" 
    description="Cet article décrit comment faire pour annuler l’enregistrement des serveurs à partir d’un archivage sécurisé récupération de Site et désactiver la protection pour machines virtuelles et des serveurs physiques." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="remove-servers-and-disable-protection"></a>Supprimer des serveurs et désactiver la protection

Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment annuler l’enregistrement des serveurs de l’archivage sécurisé récupération de Site et comment faire pour désactiver la protection des machines virtuelles protégé par la récupération de Site. 

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="unregister-a-vmm-server"></a>Annuler l’inscription d’un serveur VMM

Annuler l’enregistrement d’un serveur VMM à partir d’un archivage sécurisé en supprimant le serveur sous l’onglet **serveurs** dans le portail de récupération de Site Azure. Notez que :

-  **Serveur VMM connecté**: nous vous recommandons d’annuler l’enregistrement de serveur VMM lorsqu’il est connecté à Azure. Cela garantit que les paramètres sur le serveur VMM sur site et les serveurs VMM associés (serveurs VMM qui contiennent des nuages qui sont mappés aux nuages sur le serveur que vous voulez supprimer) sont correctement nettoyés. Nous vous recommandons de que vous supprimez uniquement un serveur déconnectée s’il existe un problème avec une connectivité permanent.
- **Serveur VMM déconnectée**: si le serveur VMM n’est pas connecté lorsque vous supprimez il vous devez exécuter un script manuellement pour effectuer le nettoyage. Le script est disponible dans la [Galerie de Microsoft](http://aka.ms/asr-cleanup-script-vmm). Notez l’ID VMM du serveur pour terminer le processus de nettoyage manuel.
- **Serveur VMM cluster**: Si vous avez besoin annuler l’enregistrement d’un serveur VMM qui est déployé dans un cluster, procédez comme suit :

    - Si le serveur est connecté, supprimez le serveur VMM connecté sous l’onglet **serveurs** . Pour désinstaller le fournisseur sur le serveur, connectez-vous sur chaque nœud de cluster et désinstaller à partir du panneau. Exécuter le script de nettoyage référencé dans la section précédente sur tous les nœuds passifs dans le cluster pour supprimer les entrées d’inscription.
    - Si le serveur n’est pas connecté, vous devez exécuter le script de nettoyage de disque sur tous les nœuds du cluster.

### <a name="unregister-an-unconnected-vmm-server"></a>Annuler l’inscription d’un serveur VMM non connecté

Sur le serveur VMM que vous voulez supprimer :

1. Annuler l’enregistrement du serveur VMM à partir du portail Azure.
2. Sur le serveur VMM, téléchargez le script de nettoyage de disque.
3. Ouvrez PowerShell à l’exécuter en tant qu’option administrateur pour modifier la stratégie d’exécution de l’étendue par défaut (LocalMachine).
4. Suivez les instructions dans le script. 

Sur des serveurs VMM ayant nuages qui sont associés à nuages sur le serveur que vous voulez supprimer :

1. Exécuter le script de nettoyage et suivez les étapes 2 à 4.
2. Spécifier l’identificateur VMM pour le serveur VMM qui a été supprimée. 
3. Ce script supprime les informations d’enregistrement pour le serveur VMM et nuage informations sur l’association.


## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Annuler l’inscription d’un serveur Hyper-V dans un site Hyper-V

Lors de la récupération de Site Azure est déployée pour protéger des machines virtuelles situés sur un serveur Hyper-V dans un site Hyper-V (avec aucun serveur VMM) vous pouvez annuler l’enregistrement un serveur Hyper-V à partir d’un archivage sécurisé comme suit :

1. Désactiver la protection des machines virtuelles situé sur le serveur Hyper-V.
2. Sous l’onglet **serveurs** dans le portail de récupération de Site Azure, sélectionnez le serveur > Supprimer. Le serveur ne doit être connecté à Azure lorsque vous effectuez cette opération.
3. Exécuter le script suivant pour effacer les paramètres sur le serveur et unregister de l’archivage sécurisé. 

        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
    
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
        
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
        
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
        
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
        
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
    
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
    
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
    
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd


## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Arrêter de protéger une machine virtuelle Hyper-V

Si vous souhaitez arrêter de protéger une machine virtuelle Hyper-V, vous devez supprimer la protection de celui-ci. Selon la façon dont vous supprimez la protection par vous devrez peut-être nettoyer les paramètres de protection manuellement à partir de l’ordinateur. 

### <a name="remove-protection"></a>Supprimer la protection

1. Dans l’onglet **Machines virtuelles** des propriétés cloud, sélectionnez la machine virtuelle > **Supprimer**.
2. Dans la page **Confirmer la suppression de Virtual Machine** vous disposez de deux options :

    - **Désactiver la protection**— si vous activez et enregistrez cette option la machine virtuelle sera n’est plus protégée par la récupération de Site. Paramètres de protection de la machine virtuelle seront automatiquement nettoyés.
    - **Supprimer de l’archivage sécurisé**— si vous sélectionnez cette option, la machine virtuelle sont supprimée uniquement à partir de l’archivage sécurisé récupération de Site. Paramètres de protection en local pour la machine virtuelle ne seront pas affectées. Vous devez effacer les paramètres manuellement pour supprimer les paramètres de protection et supprimer la machine virtuelle de l’abonnement Azure et supprimer des paramètres de protection que vous devez les nettoyer manuellement en suivant les instructions ci-dessous.

Si vous choisissez de supprimer la machine virtuelle et ses disques durs ils sont été supprimés de l’emplacement de destination.

### <a name="clean-up-protection-settings-manually-between-vmm-sites"></a>Nettoyer les paramètres de protection manuellement (entre sites VMM)

Si vous avez sélectionné **Arrêter la gestion de la machine virtuelle**, effacer manuellement les paramètres :

1. Sur le serveur principal exécuter ce script à partir de la console pour effacer les paramètres de la machine virtuelle principale. Dans la console cliquez sur le bouton PowerShell pour ouvrir la console VMM PowerShell. Remplacez SQLVM1 par le nom de votre machine virtuelle.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Sur le serveur VMM secondaire exécuter ce script pour nettoyer les paramètres de la machine virtuelle secondaire :

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force

3. Sur le serveur VMM secondaire, après avoir exécuté le script actualiser les machines virtuelles sur le serveur hôte Hyper-V afin que la machine virtuelle secondaire obtient détecter de nouveau dans la console.
4. Les étapes ci-dessus efface le serveur VMM réplication les paramètres uniquement. Si vous voulez supprimer la réplication de machine virtuelle pour la machine virtuelle. Vous devez effectuer les étapes sur les deux le principal et secondaire ci-dessous machines virtuelles. Exécuter le script ci-dessous pour supprimer la réplication et remplacez SQLVM1 par le nom de votre machine virtuelle.

        Remove-VMReplication –VMName “SQLVM1”


### <a name="clean-up-protection-settings-manually-between-on-premises-vmm-sites-and-azure"></a>Nettoyer les paramètres de protection manuellement (entre sites VMM en local et Azure)

1. Sur le serveur VMM source exécuter ce script pour nettoyer les paramètres de la machine virtuelle principale :

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Les étapes ci-dessus efface le serveur VMM réplication les paramètres uniquement. Une fois que vous avez supprimé la réplication de serveur VMM garantir pour supprimer la réplication de la machine virtuelle en cours d’exécution sur le serveur hôte Hyper-V avec ce script. Remplacez SQLVM1 par le nom de votre machine virtuelle et host01.contoso.com avec le nom du serveur hôte Hyper-V.

        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-between-hyper-v-sites-and-azure"></a>Nettoyer les paramètres de protection manuellement (entre sites Hyper-V et Azure)

1. Sur le serveur de hôte Hyper-V source, pour supprimer la réplication de la machine virtuelle Utilisez ce script. Remplacez SQLVM1 par le nom de votre machine virtuelle.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>Arrêter de protéger une machine virtuelle VMware ou un serveur physique

Si vous souhaitez arrêter de protéger une machine virtuelle VMware ou un serveur physique, vous devez supprimer la protection de celui-ci. Selon la façon dont vous supprimez la protection par vous devrez peut-être nettoyer les paramètres de protection manuellement à partir de l’ordinateur. 

### <a name="remove-protection"></a>Supprimer la protection

1. Dans l’onglet **Machines virtuelles** des propriétés cloud, sélectionnez la machine virtuelle > **Supprimer**.
2. Sur la **Supprimer un ordinateur virtuel** , sélectionnez une des options :

    - **Désactiver la protection (utilisez pour redimensionner une exploration des niveaux et le volume de récupération)**, vous seulement voyez et activez cette option si vous avez :
        - **Le volume de machine virtuelle Resized**— lorsque vous redimensionnez un volume de la machine virtuelle est mis dans un état critique. Dans ce cas, sélectionnez cette option. Il désactive la protection tout en conservant les points de récupération dans Azure. Lorsque vous réactivez la protection de la machine les données pour le volume redimensionnée sera en Azure.
        - **Exécuter un basculement**: une fois que vous avez testé votre environnement en exécutant un basculement à partir des machines virtuelles VMware locaux ou serveurs physiques vers Azure, sélectionnez cette option pour commencer à protéger vos machines virtuelles en local à nouveau. Cette option désactive chaque machine virtuelle et puis vous devez réactiver la protection pour eux. Notez que :
            - Désactivation de la machine virtuelle avec ce paramètre n’affecte pas la machine virtuelle réplica dans Azure.
            - Vous ne désinstallez le service mobilité de la machine virtuelle.
    
    - **Désactiver la protection**— si vous activez et enregistrez cette option l’ordinateur sera n’est plus protégé par la récupération de Site. Paramètres de protection de la machine seront automatiquement nettoyés.
    - **Supprimer de l’archivage sécurisé**— si vous sélectionnez cette option, l’ordinateur a été supprimé uniquement de l’archivage sécurisé récupération de Site. Paramètres de protection en local pour l’ordinateur ne seront pas affectées. Pour supprimer les paramètres de l’ordinateur et supprimer la machine virtuelle à partir de la Azure abonnement et que vous devez nettoyer les paramètres en désinstallant le service mobilité.
    
        ![Supprimer les options](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

















