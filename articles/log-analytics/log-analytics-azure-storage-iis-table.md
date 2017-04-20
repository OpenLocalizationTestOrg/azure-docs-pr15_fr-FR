<properties
    pageTitle="À l’aide de stockage d’objets blob pour le stockage IIS et table pour les événements | Microsoft Azure"
    description="Journal Analytique peut lire les journaux pour les services Azure écrire diagnostics au stockage de tables ou les journaux IIS écrites dans stockage blob."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>À l’aide de stockage d’objets blob pour IIS et le stockage de table pour les événements

Journal Analytique peut lire les journaux pour les services suivants écrire diagnostics au stockage de tables ou les journaux IIS écrites dans stockage blob :

+ Service TISSU clusters (Preview)
+ Machines virtuelles
+ Rôles Web/collaborateur

Avant de journal Analytique recueille des données de ces ressources, les diagnostics de Windows Azure doit être activé.

Une fois que diagnostics sont activées, vous pouvez utiliser le portail Azure ou PowerShell configurer Analytique journal pour collecter les journaux.

Diagnostics de Windows Azure est une extension Azure qui vous permet de collecter les données de diagnostic d’un rôle de collaborateur, rôle web ou machine virtuelle en cours d’exécution dans Azure. Les données sont stockées dans un compte de stockage Azure et peuvent ensuite être collectées par journal Analytique.

Pour journal Analytique collecter les journaux de Diagnostics Azure, les journaux doivent être aux emplacements suivants :

| Type de journal | Type de ressource | Emplacement |
| -------- | ------------- | -------- |
| Journaux IIS | Machines virtuelles <br> Rôles Web <br> Rôles de travail | WAD-iis-fichiers journaux (stockage d’objets Blob) |
| Journal système | Machines virtuelles | LinuxsyslogVer2v0 (stockage de Table) |
| Service TISSU opérationnelles événements | Nœuds tissu de service | WADServiceFabricSystemEventTable |
| Service TISSU acteur fiable événements | Nœuds tissu de service | WADServiceFabricReliableActorEventTable |
| Événements de Service fiable tissu de service | Nœuds tissu de service | WADServiceFabricReliableServiceEventTable |
| Journaux d’événements Windows | Nœuds tissu de service <br> Machines virtuelles <br> Rôles Web <br> Rôles de travail | WADWindowsEventLogsTable (stockage de tables) |
| Journaux Windows ETW | Nœuds tissu de service <br> Machines virtuelles <br> Rôles Web <br> Rôles de travail | WADETWEventTable (stockage de tables) |

>[AZURE.NOTE] Journaux IIS Azure sites Web ne sont actuellement pas pris en charge.

Pour les machines virtuelles, vous avez la possibilité de l’installation de l' [agent de journal Analytique](log-analytics-azure-vm-extension.md) dans votre machine virtuelle pour activer des informations supplémentaires. Outre la possibilité d’analyser des journaux IIS et les journaux d’événements, vous pouvez effectuer une analyse plus approfondie, y compris le suivi des modifications de configuration, l’évaluation SQL et évaluation de mise à jour.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Activer les diagnostics de Windows Azure dans une machine virtuelle pour le journal des événements et IIS journal collection de sites

Utilisez la procédure suivante pour activer les diagnostics de Windows Azure dans une machine virtuelle pour le journal des événements et IIS collection de journal à l’aide du portail Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Pour activer les diagnostics de Windows Azure dans une machine virtuelle grâce au portail Azure

1. Installez l’Agent machine virtuelle lorsque vous créez une machine virtuelle. Si la machine virtuelle existe déjà, vérifiez que l’Agent machine virtuelle est déjà installée.
    - Dans le portail Azure, accédez à la machine virtuelle, sélectionnez **Configuration facultative**, puis **Diagnostics** et définir le **statut** sur ****.

    Une fois terminé, la machine virtuelle a l’extension Azure Diagnostics installé et en cours d’exécution. Cette extension est responsable de la collecte de vos données diagnostics.

2. Activer le contrôle et configurer des événements de journalisation sur un ordinateur virtuel existant. Vous pouvez activer diagnostics au niveau de la machine virtuelle. Pour activer les diagnostics et ensuite configurer la journalisation des événements, effectuez les opérations suivantes :
    1. Sélectionnez la machine virtuelle.
    2. Cliquez sur **analyse**.
    3. Cliquez sur **Diagnostics**.
    4. Définir le **statut** sur **activé**.
    5. Sélectionnez chaque journal de diagnostic que vous souhaitez collecter.
    7. Cliquez sur **OK**.

À l’aide de PowerShell Azure vous pouvez spécifier plus précisément les événements qui sont écrits sur le stockage Azure. Reportez-vous à [collecter des données à l’aide des diagnostics écrits sur le stockage de tables ou les journaux IIS écrites dans blob de Windows Azure](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activer les diagnostics de Windows Azure dans un rôle Web pour la collecte de journal des événements IIS

Pour les étapes générales sur l’activation des diagnostics de Windows Azure, voir [Comment à activer Diagnostics dans un Service Cloud](../cloud-services/cloud-services-dotnet-diagnostics.md) . Les instructions ci-dessous utilisent ces informations et personnaliser pour une utilisation avec journal Analytique.

Avec les diagnostics de Windows Azure activé :

- Journaux IIS sont stockés par défaut, avec les données transférées à l’intervalle de transfert scheduledTransferPeriod.
- Les journaux d’événements Windows ne sont pas transférés par défaut.

### <a name="to-enable-diagnostics"></a>Pour activer des diagnostics

Pour activer les journaux d’événements Windows, ou pour changer la scheduledTransferPeriod, configurez Diagnostics Azure à l’aide du fichier de configuration XML (diagnostics.wadcfg), comme indiqué dans [étape 4 : créer votre fichier de configuration de Diagnostics et installez l’extension](../cloud-services/cloud-services-dotnet-diagnostics.md)

L’exemple de fichier de configuration suivant collecte des journaux IIS et tous les événements à partir des journaux système et d’Application :

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assurez-vous que votre ConfigurationSettings spécifie un compte de stockage, comme dans l’exemple suivant :

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Les valeurs de **nom de compte** et **AccountKey** sont trouvent dans le portail Azure dans le tableau de bord stockage compte sous Gérer les touches d’accès rapide. Le protocole pour la chaîne de connexion doit être **https**.

Une fois la configuration de diagnostic mis à jour est appliquée à votre service cloud et qu’elle écrit diagnostics au stockage Azure, vous êtes prêt à configurer journal Analytique.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utiliser le portail Azure pour collecter les journaux des stockage Azure

Vous pouvez utiliser le portail Azure pour configurer Analytique journal pour collecter les journaux pour les services Azure suivants :

+ Clusters tissu de service
+ Machines virtuelles
+ Rôles Web/collaborateur

Dans le portail Azure, accédez à votre espace de travail journal Analytique et effectuer les tâches suivantes :

1. Cliquez sur *journaux de comptes de stockage*
2. Cliquez sur la tâche *Ajouter*
3. Sélectionnez le compte de stockage contenant les journaux de diagnostic
  - Ce compte peut être un compte de stockage classique ou un compte de stockage Azure le Gestionnaire de ressources
4. Sélectionnez le Type de données à collecter les journaux de
  - Les choix possibles sont les journaux IIS ; Événements ; Journal système (Linux) ; Journaux ETW ; Événements de tissu de service
5. La valeur de la Source est remplie automatiquement en fonction du type de données et ne peuvent pas être modifiée
6. Cliquez sur OK pour enregistrer la configuration

Répétez les étapes 2 à 6 pour les comptes de stockage supplémentaire et types de données que vous souhaitez Analytique journal pour recueillir.

En 30 minutes environ, vous êtes en mesure de voir les données à partir du compte de stockage dans journal Analytique. Vous verrez uniquement les données écrites au stockage après que la configuration est appliquée. Analytique de journal ne lit pas les données existantes à partir du compte de stockage.

>[AZURE.NOTE] Le portail ne vérifie pas qu’elle existe dans le compte de stockage ou si les nouvelles données sont écrits.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Activer les diagnostics de Windows Azure dans une machine virtuelle pour la collection de sites à l’aide de PowerShell le journal journal des événements et IIS

Suivez les étapes de [Configuration de journal Analytique à indexer les diagnostics de Windows Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) utiliser PowerShell pour lire les diagnostics de Windows Azure sont écrits sur le stockage de tables.

À l’aide de PowerShell Azure vous pouvez spécifier plus précisément les événements qui sont écrits sur le stockage Azure.
Pour plus d’informations, voir [Activation des Diagnostics dans le Machines virtuelles Azure](../virtual-machines-dotnet-diagnostics.md).

Vous pouvez activer et mettre à jour les diagnostics de Windows Azure en utilisant le script PowerShell suivant.
Vous pouvez également utiliser ce script avec une configuration d’enregistrement personnalisé.
Modifiez le script pour définir le compte de stockage, le nom du service et le nom de la machine virtuelle.
Le script utilise les applets de commande pour les machines virtuelles classiques.

Passez en revue l’exemple de script suivant, copiez-le, modifiez-la selon vos besoins, enregistrer l’échantillon comme un fichier de script PowerShell et puis exécuter le script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Étapes suivantes

- [Utiliser JSON fichiers dans le stockage blob](log-analytics-azure-storage-json.md) de lire les journaux Azure services ce diagnostics écriture au stockage blob au format JSON.
- [Activer des Solutions](log-analytics-add-solutions.md) afin de fournir des données.
- [Utiliser des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
