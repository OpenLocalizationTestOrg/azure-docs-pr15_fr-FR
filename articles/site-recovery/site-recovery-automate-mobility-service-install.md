<properties
    pageTitle="Répliquer machines virtuelles VMware sur Azure à l’aide de la récupération de Site avec Azure Automation DSC | Microsoft Azure"
    description="Décrit comment utiliser Azure Automation DSC à déployer automatiquement le service Azure Site récupération mobilité et Azure l’agent pour les ordinateurs virtuel/physique Azure."
    services="site-recovery"
    documentationCenter=""
    authors="krnese"
    manager="lorenr"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="krnese"/>

# <a name="replicate-vmware-virtual-machines-to-azure-by-using-site-recovery-with-azure-automation-dsc"></a>Répliquer machines virtuelles VMware sur Azure à l’aide de la récupération de Site avec Azure Automation DSC

Dans la Suite de gestion des opérations, nous vous fournissons une sauvegarde complète et la solution de récupération d’urgence que vous pouvez utiliser dans le cadre de votre plan continuité de service.

Nous avons commencé ce voyage avec Hyper-V à l’aide de réplica Hyper-V. Mais nous avons développée pour prendre en charge une configuration hétérogène, car les clients ont plusieurs hyperviseurs et plateformes dans leurs nuages.

Si vous exécutez les charges VMware et/ou serveurs physiques aujourd'hui, un serveur de gestion de tous les composants de récupération de Site Azure s’exécute dans votre environnement pour gérer la réplication de communications et des données avec Azure, lorsque Azure est votre destination.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Déployer le service mobilité de récupération de Site à l’aide de Automation DSC
Commençons en effectuant une analyse rapide du rôle de ce serveur d’administration.

Le serveur d’administration s’exécute plusieurs rôles de serveur. Un de ces rôles est *configuration*qui coordonnées de communication et gère les processus de réplication et la restauration des données.

En outre, le rôle de *processus* constitue une passerelle de réplication. Ce rôle reçoit les données de réplication à partir d’ordinateurs source protégée, optimise avec la mise en cache, de compression et chiffrement et envoie ensuite à un compte de stockage Azure. Un des fonctions pour le rôle de processus est également pour l’installation du service mobilité de transmission pour machines protégées et effectuer la découverte automatique des machines virtuelles VMware.

S’il existe un retour arrière à partir d’Azure, le rôle *cible maître* gérera les données de réplication dans le cadre de cette opération.

Pour les ordinateurs protégées, nous reposent sur le *service de mobilité*. Ce composant est déployé sur chaque ordinateur (VMware VM ou serveur physique) que vous souhaitez répliquer vers Azure. Il capture écritures de données sur l’ordinateur et les transmet au serveur d’administration (rôle de processus).

Lorsque vous avez affaire à continuité des activités, il est important de comprendre vos charges de travail, votre infrastructure et les composants impliqués. Vous pouvez puis remplissez les conditions pour votre récupération heure objectifs et récupération objectifs de point (). Dans ce contexte, le service mobilité est essentiels pour s’assurer que vos charges de travail sont protégés comme prévu.

Comment pouvez vous, d’une manière optimisée, assurez-vous d’avoir une configuration fiable protégée à l’aide de certains composants opérations Management Suite ?

Cet article fournit un exemple de comment vous pouvez utiliser Azure Automation vous le souhaitez état Configuration (DSC), ainsi que de récupération de Site, pour vous assurer que :

- La mobilité et agent de machine virtuelle Azure sont déployés sur les ordinateurs Windows que vous voulez protéger.
- La mobilité et agent de machine virtuelle Azure sont toujours en cours d’exécution lorsque Azure est la cible de réplication.

## <a name="prerequisites"></a>Conditions préalables

- Un référentiel pour stocker la configuration requise
- Un référentiel pour stocker le mot de passe requis pour enregistrer avec le serveur de gestion

 > [AZURE.NOTE] Un mot de passe unique est généré pour chaque serveur d’administration. Si vous vous apprêtez à déployer plusieurs serveurs d’administration, vous devez vous assurer que le mot de passe correct est stocké dans le fichier passphrase.txt.

- Windows Management Framework (WMF) 5.0 est installé sur les ordinateurs que vous souhaitez activer la protection (obligatoire pour Automation DSC)

 > [AZURE.NOTE] Si vous voulez utiliser des ordinateurs DSC pour Windows qui disposent de WMF 4.0, consultez la section [Utiliser DSC dans les environnements déconnectés](#Use DSC in disconnected environments).

Le service mobilité pouvant être installé via la ligne de commande et accepte plusieurs arguments. C’est pourquoi vous devez avoir les fichiers binaires (après les extraire à partir de votre installation) et les stocker dans un emplacement où vous pouvez les récupérer en utilisant une configuration DSC.

## <a name="step-1-extract-binaries"></a>Étape 1 : Fichiers binaires extraire

1. Pour extraire les fichiers dont vous avez besoin pour cette configuration, accédez au répertoire suivant sur votre serveur de gestion :

    **\Microsoft Site azure Recovery\home\svsystems\pushinstallsvc\repository**

    Dans ce dossier, vous devez voir un fichier MSI nommé :

    **Microsoft ASR_UA_version_Windows_GA_date_Release.exe**

    Utilisez la commande suivante pour extraire le programme d’installation :

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe/q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**

2. Sélectionnez tous les fichiers et les envoyer à un dossier compressé (zippé).

Vous avez à présent les fichiers binaires dont vous avez besoin pour automatiser la configuration du service mobilité à l’aide de Automation DSC.

### <a name="passphrase"></a>Mot de passe

Ensuite, vous devez déterminer l’endroit où vous souhaitez placer ce dossier compressé. Vous pouvez utiliser un compte de stockage Azure, comme illustré plus tard, pour stocker le mot de passe dont vous avez besoin pour le programme d’installation. L’agent sera enregistré puis avec le serveur d’administration dans le cadre du processus.

Le mot de passe que vous avez obtenu lorsque vous avez déployé le serveur de gestion peut être enregistré dans un fichier texte en tant que passphrase.txt.

Placez le dossier compressé et le mot de passe dans un conteneur dédié dans le compte de stockage Azure.

![Emplacement du dossier](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si vous préférez conserver ces fichiers sur un partage de votre réseau, vous pouvez le faire. Vous devez simplement vous assurer que la ressource DSC que vous envisagez d’utiliser plus tard a l’accès et peut accéder à la configuration et le mot de passe.

## <a name="step-2-create-the-dsc-configuration"></a>Étape 2 : Créer la configuration DSC

Le programme d’installation dépend WMF 5.0. Pour l’ordinateur pour appliquer la configuration via Automation DSC, WMF 5.0 doit être présent.

L’environnement utilise l’exemple de configuration DSC suivant :

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuration effectue les opérations suivantes :

- Les variables indiquera la configuration où la trouver les fichiers binaires pour la mobilité et l’agent machine virtuelle Azure, où la trouver le mot de passe et l’emplacement de stockage de la sortie.
- La configuration va importer la ressource xPSDesiredStateConfiguration DSC, afin que vous puissiez utiliser `xRemoteFile` pour télécharger les fichiers à partir du référentiel.
- La configuration créera un répertoire où vous voulez stocker les fichiers binaires.
- La ressource archive extrait les fichiers à partir du dossier compressé.
- Le package ressource d’installation s’installe le service mobilité à partir de la UNIFIEDAGENT. Programme d’installation EXE avec les arguments spécifiques. (Les variables construire les arguments doivent être modifiées pour refléter votre environnement.)
- Le package AzureAgent ressource s’installe l’agent machine virtuelle Azure, qui est recommandé sur chaque ordinateur virtuel qui s’exécute dans Azure. L’agent machine virtuelle Azure facilite également possible d’ajouter des extensions de la machine virtuelle après le basculement.
- L’ou les ressources service garantit que les services de mobilité associés et les services Azure toujours en cours d’exécution.

Enregistrer la configuration en tant que **ASRMobilityService**.

>[AZURE.NOTE] N’oubliez pas de remplacer le CSIP dans votre configuration pour refléter le serveur d’administration réel, afin que l’agent sera correctement connecté et utilisera le mot de passe correct.

## <a name="step-3-upload-to-automation-dsc"></a>Étape 3 : Télécharger dans Automation DSC

Étant donné que la configuration DSC que vous avez apportées importera un module de ressources DSC requis (xPSDesiredStateConfiguration), vous devez importer ce module dans automatisation avant de télécharger la configuration DSC.

Connectez-vous à votre compte automatisation, accédez à **actifs** > **Modules**, cliquez sur **Parcourir la galerie**.

Ici, vous pouvez rechercher du module et importer dans votre compte.

![Module d’importation](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Lorsque vous avez terminé cette, accédez à votre ordinateur où vous avez les modules Gestionnaire de ressources Azure installés et passez à importer la configuration DSC nouvellement créée.

### <a name="import-cmdlets"></a>Importer des applets de commande

Dans PowerShell, connectez-vous à votre abonnement Azure. Modifier les applets de commande pour refléter votre environnement et capturer vos informations de compte Automation dans une variable :

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Télécharger la configuration à DSC Automation à l’aide de l’applet de commande suivante :

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compiler la configuration dans DSC Automation

Ensuite, vous devez compiler la configuration dans Automation DSC, afin que vous pouvez commencer à enregistrer des nœuds lui. Vous effectuer cette opération en exécutant l’applet de commande suivante :

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Cela peut prendre quelques minutes, car vous déployez fondamentalement la configuration du service d’extraction DSC hébergé.

Après avoir compilé la configuration, vous pouvez récupérer les informations du projet à l’aide de PowerShell (Get-AzureRmAutomationDscCompilationJob) ou à l’aide du [portail Azure](https://portal.azure.com/).

![Récupérer tâche](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Vous avez maintenant correctement publié et téléchargé votre configuration DSC dans Automation DSC.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Étape 4 : Intégrés machines à DSC Automation
>[AZURE.NOTE] Une des conditions préalables à la fin de ce scénario est que vos ordinateurs Windows sont mis à jour avec la dernière version de WMF. Vous pouvez télécharger et installer la version appropriée pour votre plateforme à partir du [Centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=50395).

Vous allez maintenant créer un metaconfig pour DSC vous s’appliquent à vos nœuds. Pour réussir avec cela, vous devez récupérer l’URL du point de terminaison et la clé primaire pour votre compte Automation sélectionné dans Azure. Vous trouverez ces valeurs sous **clés** sur la carte de **tous les paramètres** du compte Automation.

![Valeurs de clé](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

Dans cet exemple, vous avez un serveur physique Windows Server 2012 R2 que vous souhaitez protéger à l’aide de récupération de Site.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Recherchez les opérations de changement de nom de fichier dans le Registre en attente

Avant de commencer à associer le serveur le point de terminaison DSC automatisation, nous vous recommandons de vérifier les opérations de changement de nom de fichier dans le Registre en attente. Ils peuvent empêchent la configuration de finition en raison d’un redémarrage en attente.

Exécutez l’applet de commande suivante pour vérifier qu’il n’existe aucun redémarrage en attente sur le serveur :

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si cela fait apparaître vide, vous êtes OK pour continuer. Si ce n’est pas le cas, vous devez le traiter en redémarrant le serveur pendant une période de maintenance.

Pour appliquer la configuration sur le serveur, démarrez l’environnement de l’écriture de script intégré PowerShell (ISE) et exécutez le script suivant. Il s’agit pour l’essentiel une configuration DSC locale qui donne au moteur de gestionnaire de Configuration locales inscrire auprès du service Automation DSC et de récupérer la configuration spécifique (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Cette configuration entraînera le moteur de gestionnaire de Configuration Local à s’inscrire avec Automation DSC. Elle déterminera également comment le moteur doit fonctionner, qu’il doit faire s’il existe un problèmes de configuration (ApplyAndAutoCorrect) et comment il doit procéder à la configuration si un redémarrage est nécessaire.

Après avoir exécuté ce script, le nœud doit commencer à enregistrer avec Automation DSC.

![Enregistrement du nœud en cours](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vous revenez au portail Azure, vous pouvez voir que le nœud récemment enregistré est maintenant répertoriée dans le portail.

![Nœud enregistré dans le portail](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Sur le serveur, vous pouvez exécuter l’applet de commande PowerShell suivante pour vérifier que le nœud a été correctement inscrit :

```powershell
Get-DscLocalConfigurationManager
```

Une fois la configuration a été extraite et appliquée au serveur, vous pouvez vérifier ceci en exécutant l’applet de commande suivante :

```powershell
Get-DscConfigurationStatus
```

Le résultat indique que le serveur a extrait avec succès sa configuration :

![Sortie](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

En outre, la configuration du service mobilité possède son propre journal soient retrouvé en *lecteur_système*\ProgramData\ASRSetupLogs.

Voilà. Vous disposez désormais correctement déployée et inscrit au service mobilité sur l’ordinateur que vous souhaitez protéger à l’aide de récupération de Site. DSC va s’assurer que les services requis sont toujours en cours d’exécution.

![Déploiement réussi](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Une fois que le serveur de gestion détecte la réussite du déploiement, vous pouvez configurer la protection et activer la réplication sur l’ordinateur à l’aide de récupération de Site.

## <a name="use-dsc-in-disconnected-environments"></a>Utiliser DSC dans les environnements déconnectés

Si vos ordinateurs ne sont pas connectés à Internet, vous pouvez toujours compter sur DSC à déployer et configurer le service mobilité sur les charges de travail que vous voulez protéger.

Vous pouvez instanciation votre propre serveur extraire DSC dans votre environnement pour l’essentiel fournir les mêmes fonctionnalités que vous obtenez d’automatisation DSC. Autrement dit, les clients extrait la configuration (après son inscription) au point de terminaison DSC. Toutefois, une autre option consiste à distribuer manuellement la configuration de DSC sur vos ordinateurs, localement ou à distance.

Notez que dans cet exemple, il existe un paramètre supplémentaire pour le nom d’ordinateur. Les fichiers distants sont maintenant situés sur un partage distant doit être accessible par les machines que vous voulez protéger. La fin du script active la configuration, puis démarre à appliquer la configuration DSC vers l’ordinateur cible.

### <a name="prerequisites"></a>Conditions préalables

Assurez-vous que le module PowerShell xPSDesiredStateConfiguration est installé. Pour les ordinateurs Windows où WMF 5.0 est installé, vous pouvez installer le module xPSDesiredStateConfiguration en exécutant l’applet de commande suivante sur les ordinateurs cibles :

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Vous pouvez également télécharger et enregistrez le module au cas où vous devez distribuer aux ordinateurs Windows ayant WMF 4.0. Exécuter cette applet de commande sur un ordinateur où se trouve présenter PowerShellGet (WMF 5.0) :

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Également pour 4.0 WMF, assurez-vous que la [mise à jour KB2883200 de Windows 8.1](https://www.microsoft.com/download/details.aspx?id=40749) est installé sur les ordinateurs.

La configuration suivante peut être installée sur des ordinateurs Windows qui ont WMF 5.0 et 4.0 WMF :

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si vous voulez instanciation votre propre serveur extraire DSC sur votre réseau d’entreprise pour simuler les fonctionnalités que vous pouvez obtenir des commentaires DSC automatisation, voir [configuration d’un serveur DSC web extraire](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Facultatif : Déploiement d’une configuration DSC à l’aide d’un modèle de gestionnaire de ressources Azure

Cet article compte axée sur comment vous pouvez créer votre propre configuration DSC pour déployer la mobilité et l’Agent de machine virtuelle Azure--automatiquement et vous assurer que celles-ci s’exécutent sur les ordinateurs que vous voulez protéger. Nous avons également un modèle de gestionnaire de ressources Azure qu’allez déployer cette configuration DSC à un compte Azure Automation nouvel ou existant. Le modèle utilisera les paramètres d’entrée pour créer des éléments Automation contenant les variables pour votre environnement.

Une fois que vous déployez le modèle, vous pouvez simplement faire référence à l’étape 4 de ce guide intégrée pour vos ordinateurs.

Le modèle effectue les opérations suivantes :

1. Utiliser un compte Automation existant ou créez-en un
2. Prendre des paramètres d’entrée pour :
    - ASRRemoteFile--l’emplacement où vous avez stocké l’installation du service mobilité
    - ASRPassphrase--l’emplacement où vous avez enregistré le fichier passphrase.txt
    - ASRCSEndpoint--l’adresse IP de votre serveur de gestion
3. Importer le module PowerShell xPSDesiredStateConfiguration
4. Créer et compiler la configuration DSC

Toutes les étapes précédentes seront produit dans l’ordre approprié, afin que vous puissiez lancer d’intégration vos ordinateurs pour la protection.

Le modèle, des instructions pour le déploiement, se trouve sur [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Déployez le modèle à l’aide de PowerShell :

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous déployez les agents de service mobilité, vous pouvez [Activer la réplication](site-recovery-vmware-to-azure.md#step-6-replicate-applications) pour les ordinateurs virtuels.
