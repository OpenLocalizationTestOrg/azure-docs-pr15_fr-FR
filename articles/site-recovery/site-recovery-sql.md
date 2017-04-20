<properties 
    pageTitle="Protéger SQL Server avec SQL Server reprise et de récupération de Site Azure | Microsoft Azure" 
    description="Cet article décrit comment répliquer SQL Server à l’aide des capacités d’urgence Azure Site récupération de SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Protéger SQL Server avec SQL Server reprise et de récupération de Site Azure 


Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md).

 Cet article décrit comment protéger SQL Server principaux dans une application à l’aide d’une combinaison de technologies SQL Server BCDR et récupération de Site Azure. Vous devez disposer d’une bonne compréhension de reprise de SQL Server (basculement de cluster, groupes de disponibilité AlwaysOn, de base de données mise en miroir, envoi des journaux) et de récupération de Site Azure, avant de déployer les scénarios décrits dans cet article.



## <a name="overview"></a>Vue d’ensemble

Nombreuses charges de travail utilisent SQL Server comme base. Applications tels que SharePoint et SAP Dynamics permet de mettre en œuvre des services de données SQL Server.  Applications déploiement SQL Server dans un certain nombre de différentes manières :

- **Autonome SQL Server**: SQL Server et toutes les bases de données sont hébergés sur un seul ordinateur (physique ou virtuel). Lorsque virtualisé, hôte cluster est utilisé pour haute disponibilité locale. Aucune disponibilité au niveau de l’invité n’est activée.
- **Instances cluster de basculement SQL Server (toujours sur FCI)**: les nœuds deux ou plusieurs des instances de SQL server avec disques partagés sont configurés dans un cluster de basculement Windows. Si toutes les instances cluster est vers le bas, le cluster peut échouer sur SQL Server à une autre instance. Cette configuration est généralement utilisée pour HA sur un site principal. Il ne protéger contre la défaillance ou une panne dans la couche de stockage partagé. Disque partagé peut être implémentée à l’aide de ISCSI, Fibre channel ou VHDx partagés.
- **Toujours sur disponibilité groupes SQL**: dans cette configuration, deux nœuds sont configurés dans partagé rien cluster les bases de données SQL Server configurés dans un groupe de disponibilité avec la réplication synchrone et basculement automatique.

Dans les éditions Enterprise, SQL Server fournit également urgence native technologies de récupération de récupération de bases de données vers un site distant. Dans cet article, nous allons exploiter et intégrer ces technologies de récupération d’urgence SQL natives : 

- SQL toujours sur les groupes de disponibilité de reprise pour SQL Server 2012 ou 2014 Enterprise Edition.
- SQL de base de données mise en miroir en mode de sécurité élevé pour SQL Server Standard edition (n’importe quelle version), ou pour SQL Server 2008 R2.


Récupération de site pouvez protéger SQL Server comme décrit dans le tableau.

 |**Local vers local** | **Local vers Azure** 
---|---|---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui 
**Serveur physique** | Oui | Oui


## <a name="support-and-integration"></a>Prise en charge et l’intégration

Ces versions de SQL Server sont pris en charge par les scénarios dans cet article :


- Standard et SQL Server 2014 Enterprise
- Standard et SQL Server 2012 Enterprise
- Standard et SQL Server 2008 R2 entreprise


Récupération de site peut être intégrée avec les technologies SQL Server BCDR natives synthétisées dans le tableau ci-dessous afin de fournir une solution de récupération d’urgence.

**Fonctionnalité** |**Plus d’informations** | **Version de SQL Server** 
---|---|---
**Groupe de disponibilité AlwaysOn** | Plusieurs instances autonome de SQL Server s’exécutent dans un cluster de basculement qui comporte plusieurs nœuds.<br/><br/>Bases de données peuvent être regroupés en groupes basculement pouvant être copiée (en miroir) sur des instances de SQL Server afin qu’aucun stockage partagé n’est nécessaire.<br/><br/>Fournit sinistre entre un site principal et un ou plusieurs sites secondaires. Deux nœuds peuvent être configurés dans un partagé rien cluster les bases de données SQL Server configuré dans un groupe de disponibilité avec la réplication synchrone et basculement automatique. | SQL Server 2014 & 2012 Enterprise edition
**Basculement de cluster (AlwaysOn FCI)** | SQL Server exploite basculement de Windows cluster de disponibilité de charges de travail de SQL Server locale.<br/><br/>Exécution d’instances de SQL Server avec disques partagés les nœuds sont configurés dans un cluster de basculement. Si une instance est vers le bas le cluster bascule vers un autre.<br/><br/>Le cluster ne protéger contre échec ou défaillances dans stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre channel, ou partagé VHDXs. | Éditions de SQL Server Enterprise<br/><br/>SQL Server Standard edition (limité à deux nœuds uniquement)
**Base de données mise en miroir (mode de sécurité élevé)** | Protéger une seule base de données à une seule copie secondaire. Disponible dans les deux applets (synchrone) et des modes de réplication (asynchrone) haute performance. Ne nécessite pas un cluster de basculement. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise toutes les éditions
**Autonome SQL Server** | SQL Server et base de données sont hébergés sur un serveur unique (physique ou virtuel). Hôte cluster est utilisé pour la haute disponibilité si le serveur est virtuel. Aucune disponibilité au niveau des invités. | Édition Enterprise ou Standard

## <a name="deployment-recommendations"></a>Conseils de déploiement


Le tableau suivant résume nos recommandations pour l’intégration des technologies SQL Server BCDR avec récupération de Site.

**Version** |**Édition** | **Déploiement** | **Local à local** | **Local vers Azure** 
---|---|---|---|---
SQL Server 2014 ou 2012 | Entreprise | Instance de cluster de basculement | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Entreprise | Groupes de disponibilité AlwaysOn de disponibilité | Groupes de disponibilité AlwaysOn | Groupes de disponibilité AlwaysOn
 | Standard | Consacrée (FCI) | Réplication de récupération de site avec des pages en vis-à-vis local | Réplication de récupération de site avec des pages en vis-à-vis local
 | Entreprise ou Standard | Autonome | Réplication de récupération de site | Réplication de récupération de site
SQL Server 2008 R2 | Entreprise ou Standard | Consacrée (FCI) | Réplication de récupération de site avec des pages en vis-à-vis local | Réplication de récupération de site avec des pages en vis-à-vis local
 | Entreprise ou Standard | Autonome | Réplication de récupération de site | Réplication de récupération de site
SQL Server (n’importe quelle version) | Entreprise ou Standard | Consacrée - application DTC | Réplication de récupération de site | Non pris en charge


## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Voici ce que vous devez avant de commencer :

- Un déploiement sur site de SQL Server exécute une version de SQL Server pris en charge. En règle générale vous devez également un Active Directory pour votre SQL server.
- Les conditions préalables pour le scénario que vous voulez déployer. Conditions préalables sont accessibles dans chaque article déploiement. Des liens vers ces sont fournies dans [Vue d’ensemble de la récupération de Site](site-recovery-overview.md).
- Si vous voulez configurer la récupération dans Azure, vous devez exécuter l’outil [d’Évaluation de la préparation Azure Machine virtuelle](http://www.microsoft.com/download/details.aspx?id=40898) sur vos machines virtuelles SQL Server pour vous assurer qu’elles sont compatibles avec Azure et de récupération de Site.


## <a name="set-up-active-directory"></a>Configurer Active Directory

Vous devez Active Directory sur le site de récupération secondaire pour SQL Server fonctionner correctement. Il existe deux possibilités :

- **Petite entreprise**: Si vous avez un petit nombre d’applications et un contrôleur de domaine pour le site en local, et vous souhaitez basculer sur l’ensemble du site, nous vous recommandons d’utiliser récupération de Site repication répliquer le contrôleur de domaine au centre de données secondaire ou sur Azure.

- **Moyennes et grandes entreprises**: Si vous avez un grand nombre d’application, vous exécutez une forêt Active Directory, et vous voulez basculer en application ou la charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Notez que si vous utilisez des groupes de disponibilité AlwaysOn pour récupérer vers un site distant nous vous recommandons de que configurer un autre contrôleur de domaine supplémentaire sur le site secondaire ou sur Azure, à utiliser pour l’instance SQL Server récupéré.

Les instructions présentées dans ce document supposent qu’un contrôleur de domaine est disponible dans l’emplacement secondaire. [Pour en savoir plus](site-recovery-active-directory.md) sur la protection de Active Directory avec récupération de Site.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Intégrer la protection de SQL Server sacoche (en local vers Azure)


Récupération de site prise en charge native AlwaysOn SQL. Si vous avez créé un groupe de disponibilité SQL avec une machine virtuelle Azure configurer comme « Secondaire », vous pouvez ensuite utiliser récupération de Site pour gérer le basculement des groupes de disponibilité. 

>[AZURE.NOTE] Cette fonctionnalité est actuellement en mode Aperçu avant et disponible lorsque serveurs d’hôte Hyper-V dans le centre de données principal sont gérées dans nuages VMM et lorsque le programme d’installation VMware est géré par un [Serveur de Configuration](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Pour le moment, cette fonctionnalité n’est pas disponible dans le nouveau portail Azure.

#### <a name="prerequisites"></a>Conditions préalables

Voici ce que vous devez intégrer SQL AlwaysOn récupération de Site :

- Un serveur SQL local (serveur autonome ou un cluster de basculement).
- Une ou plusieurs machines virtuelles Azure avec SQL Server est installé
- Un groupe de disponibilité SQL configurer entre un local SQL Server et SQL Server en cours d’exécution dans Azure
- Accès distant PowerShell doit être activé sur l’ordinateur de SQL Server locale. Le serveur VMM ou le serveur de Configuration doit être en mesure d’effectuer des appels PowerShell distants à SQL Server.
- Un compte d’utilisateur doit être ajouté sur SQL Server en local, dans ces groupes d’utilisateurs SQL au moins ces autorisations :
    - MODIFIER le groupe de disponibilité : autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx)et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de données ALTER - autorisations[ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Un compte RunAs doit être créé sur serveur VMM ou un compte doit être créé sur le serveur de Configuration à l’aide de la CSPSConfigtool.exe pour l’utilisateur mentionné à l’étape précédente 
- Module PS SQL doit être installé sur des serveurs SQL en cours d’exécution en local et sur les machines virtuelles Azure
- L’Agent de machine virtuelle doit être installés machines virtuelles s’exécutant sur Azure
- NTAUTHORITY\System doit avoir après les autorisations dans SQL Server s’exécutant sur des machines virtuelles dans Azure :
    - MODIFIER le groupe de disponibilité - autorisations [ici](https://msdn.microsoft.com/library/hh231018.aspx)et [ici](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de données ALTER - autorisations [ici](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Étape 1 : Ajouter un serveur SQL Server


1. Cliquez sur **Ajouter SQL** pour ajouter un nouveau SQL Server. 

    ![Ajouter SQL](./media/site-recovery-sql/add-sql.png)

2. Dans **Configurer les paramètres SQL** > **nom** fournir un nom convivial pour faire référence à SQL Server.
3. **Dans SQL Server (FQDN)** spécifiez le nom de domaine complet de la source de SQL Server que vous voulez ajouter. En cas de SQL Server est installé sur un Cluster de basculement, puis indiquez nom de domaine complet du cluster et non d’un des nœuds du cluster.  
4. Dans **l’Instance SQL Server** sélectionnez l’instance par défaut ou indiquez le nom de l’instance personnalisée.
5. Dans **Management Server** sélectionner un serveur VMM ou Configuration serveur enregistré dans l’archivage sécurisé récupération de Site. Récupération de site utilise ce serveur d’administration pour communiquer avec le serveur SQL Server.
6. Dans la zone **Exécuter en tant que compte** fournir le nom d’un compte RunAs qui a été créé sur le serveur VMM spécifié ou le compte qui a été créé sur le serveur Configuraaaon. Ce compte est utilisé pour accéder à SQL Server et doit disposer d’autorisations de lecture et le basculement sur les groupes de disponibilité sur l’ordinateur SQL Server.

    ![Boîte de dialogue SQL ajouter](./media/site-recovery-sql/add-sql-dialog.png)

Après avoir ajouté le serveur SQL Server il s’affichent dans l’onglet **Serveurs SQL Server** . 

![Liste des serveurs SQL](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Étape 2 : Ajouter un groupe de disponibilité SQL

1. Une fois le SQL Server machine est ajouté à que l’étape suivante consiste à ajouter les groupes de disponibilité à la récupération de Site. Pour ce faire, Explorer les niveaux inférieurs à l’intérieur de SQL Server ajoutée à l’étape précédente, puis cliquez sur Ajouter un groupe de disponibilité SQL. 

    ![Ajouter AG SQL](./media/site-recovery-sql/add-sqlag.png)

2. Groupe de disponibilité SQL pouvez répliquer à une ou plusieurs machines virtuelles dans Azure. Lorsque vous ajoutez le groupe de disponibilité sql, vous devrez fournir le nom et l’abonnement de la machine virtuelle Azure où vous souhaitez que le groupe disponibilité basculé vers lors de la récupération de Site.

    ![Boîte de dialogue SQL AG ajouter](./media/site-recovery-sql/add-sqlag-dialog.png)

3. Dans l’exemple ci-dessus disponibilité groupe dégressif 1-AG deviendrait principale sur machine virtuelle SQLAGVM2 fonctionnant à l’intérieur d’abonnement DevTesting2 sur un basculement. 

>[AZURE.NOTE] Seuls les groupes de disponibilité sont serveur principal de SQL Server ajoutée à l’étape ci-dessus sont disponibles à ajouter à la récupération de Site. Si vous avez effectué un principal de groupe disponibilité sur SQL Server ou si vous avez ajouté plusieurs groupes de disponibilité sur le serveur SQL après son ajout, Actualiser à l’aide de l’option d’actualisation disponible sur SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Étape 3 : Créer un Plan de récupération

L’étape suivante consiste à créer un plan de récupération à l’aide de machines virtuelles et les groupes de disponibilité. Sélectionnez le même serveur VMM ou serveur de Configuration que vous avez utilisé à l’étape 1 comme source et Microsoft Azure comme cible.

![Créer le Plan de récupération](./media/site-recovery-sql/create-rp1.png)

![Créer le Plan de récupération](./media/site-recovery-sql/create-rp2.png)

Dans l’exemple de l’application Sharepoint se compose de 3 machines virtuelles qui utilisent un groupe de disponibilité SQL que sa version serveur. Dans ce plan de récupération que nous pouvons sélectionner les deux le groupe de disponibilité ainsi que la machine virtuelle qui constituent l’application. 

Vous pouvez personnaliser davantage le plan de récupération en déplaçant des machines virtuelles à basculement différents groupes à l’ordre de basculement de séquence. Groupe de disponibilité est basculé toujours première tel qu’il est utilisé comme un serveur principal de n’importe quelle application. 

![Personnaliser le Plan de récupération](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Étape 4 : Basculer

Basculement différentes options sont disponibles une fois qu’un groupe de disponibilité a été ajouté à un Plan de récupération.

Basculement | Plus d’informations
--- | ---
**Basculement planifié** | Basculement planifié n’implique un aucun basculement de perte de données. Pour obtenir le Mode de disponibilité de ce groupe de disponibilité SQL a tout d’abord la valeur synchrone et puis un basculement se déclenche pour rendre le groupe de disponibilité principal une session sur l’ordinateur virtuel fourni lors de l’ajout du groupe de disponibilité de récupération de Site. Une fois que le basculement est terminé, le Mode de disponibilité est défini sur la même valeur qu’elle était définie avant que le basculement planifié a été déclenché.
**Basculement non planifié** | Basculement non planifié peut entraîner en perte de données. Lors du déclenchement basculement non planifié le mode de disponibilité du groupe disponibilité n’est pas modifié et le service informatique est rendu primaire une session sur l’ordinateur virtuel fourni lors de l’ajout du groupe de disponibilité de récupération de Site. Une fois que le basculement non planifié est terminée et que le serveur local qui exécute SQL Server est disponible à nouveau, inverser la réplication doit être déclenchées dans le groupe de disponibilité. Notez que cette action n’est pas disponible sur le plan de récupération et peut être prise sur le groupe de disponibilité SQL sous l’onglet serveurs SQL
**Basculement de test** | Basculement de test pour le groupe de disponibilité SQL n’est pas pris en charge. Si vous de déclencher le basculement de Test d’un Plan de récupération contenant le groupe de disponibilité SQL, basculement serait ignorée pour le groupe de disponibilité.


Pensez à ces options de basculement.

Option | Plus d’informations
--- | ---
**Option 1** | 1. effectuez un basculement de test de l’application et les niveaux frontaux.<br/><br/>2. Mettez à jour la couche application pour accéder à la copie du réplica en mode lecture seule et effectuez un test en lecture seule de l’application.
**Option 2** | 1. créer une copie de l’instance de machine virtuelle SQL Server réplica (pour la sauvegarde de Azure ou à un site à l’aide de cloner VMM) et de faire apparaître dans un réseau de test<br/><br/> 2. effectuez le basculement d’essai à l’aide du plan de récupération.

Étape 5 : Retour arrière

Si vous souhaitez créer le groupe de disponibilité à nouveau primaire sur SQL Server en local puis vous pouvez le faire en déclenchement basculement planifiée sur le Plan de récupération et en choisissant la direction de Microsoft Azure au serveur VMM en local.

>[AZURE.NOTE] Après un basculement non planifié réplication inverse doit être déclenchées dans le groupe de disponibilité pour reprendre la réplication. La réplication reste en pause jusqu'à ce que cela.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Protéger les ordinateurs sans un serveur VMM ou un serveur de Configuration

Pour les environnements qui ne sont pas gérés par un serveur VMM ou un serveur de Configuration, procédures opérationnelles Azure Automation peut servir à configurer un script basculement de groupes de disponibilité SQL. Voici les étapes de configuration qui :

1.  Créer un fichier local pour le script bascule vers un groupe de disponibilité. Cet exemple de script spécifie un chemin d’accès au groupe de disponibilité du réplica Azure et bascule à cette instance du réplica. Ce script s’exécutera sur SQL Server machine virtuelle de réplica en passant est avec l’extension de script personnalisé.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Téléchargez le script sur un blob dans un compte de stockage Azure. Utilisez cet exemple :

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Créer un runbook Azure automation pour appeler les scripts sur l’ordinateur SQL Server réplica virtuel dans Azure. Utilisez cet exemple de script à cet effet. [En savoir plus](site-recovery-runbook-automation.md) sur l’utilisation de procédures opérationnelles automation dans les plans de récupération. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Lorsque vous créez un plan de récupération pour l’application Ajouter une étape de script « préalable regrouper 1 démarrage » qui appelle la procédure opérationnelle automation basculer sur groupes de disponibilité.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Intégrer la protection SQL AlwaysOn (en local vers local)

Si le serveur SQL Server est à l’aide de groupes de disponibilité de disponibilité, ou une instance de cluster de basculement, nous recommandons d’utiliser des groupes de disponibilité sur le site de récupération ainsi. Notez que ce guide est destiné aux applications qui n’utilisent pas des transactions distribuées.

1. [Configurer les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
2. Créer un nouveau réseau virtuel sur site secondaire.
3. Configurer un réseau privé virtuel de site à entre le réseau virtuel et le site principal.
4. Créer une machine virtuelle sur le site de récupération et installez SQL Server dessus.
5. Étendre les groupes de disponibilité AlwaysOn existants à la nouvelle machine virtuelle SQL Server. Configurez cette instance SQL Server comme une copie du réplica asynchrone.
6. Créer un récepteur de groupe de disponibilité, ou mettre à jour le récepteur existant pour inclure la machine virtuelle réplica asynchrone.
7. Vérifiez que la batterie de serveurs application est configurée à l’aide de que l’auditeur. S’il est installé à l’aide du nom du serveur de base de données, mettez à jour pour utiliser le récepteur afin que vous n’avez pas besoin de reconfigurer après le basculement.

Pour les applications qui utilisent des transactions distribuées nous recommandation vous utilisez [Récupération de Site avec réplication SAN](site-recovery-vmm-san.md) ou [réplication de site à server VMWare/physique](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considérations sur la récupération plan

1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites principaux et secondaires.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Lorsque vous créez un plan de récupération pour l’application Ajouter une étape de script « préalable regrouper 1 démarrage » qui appelle le script basculer sur groupes de disponibilité.



## <a name="protect-a-standalone-sql-server"></a>Protéger une instance autonome de SQL Server

Dans cette configuration, nous vous recommandons de que réplication de récupération de Site permet de protéger l’ordinateur SQL Server. Les étapes exactes dépendra si SQL Server a été configurée en tant que machine virtuelle ou physique et que vous souhaitez répliquer vers Azure ou secondaire local site. Obtenir des instructions pour tous les scénarios de déploiement dans la [Vue d’ensemble de la récupération de Site](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Protéger un cluster de SQL Server (Standard ou 2008 R2)

Pour un cluster qui exécute SQL Server Standard edition Server ou SQL Server 2008 R2, nous vous recommandons de que réplication de récupération de Site permet de protéger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local vers local

- Si l’application utilise les transactions distribuées, nous vous recommandons de que vous déployez [Récupération de Site avec réplication SAN](site-recovery-vmm-san.md) pour un environnement Hyper-V et [serveur VMware/physique à VMware](site-recovery-vmware-to-vmware.md) pour environnementVMWare.

- Pour les applications non-DTC, exploiter l’approche ci-dessus pour récupérer le cluster comme un serveur autonome en tirant parti des applets local en miroir de base de données.

### <a name="on-premises-to-azure"></a>Local vers Azure

Récupération de site ne prend en charge prise en charge de cluster invités lors de la réplication vers Azure. SQL Server n’également fournir une solution de récupération après sinistre économique pour l’Édition Standard. Nous vous recommandons de vous protégez le cluster SQL Server en local pour une instance autonome de SQL Server et récupérer dans Azure.


1. Configurer une instance de SQL Server autonome supplémentaires sur le site local.
2. Configurer cette instance comme un miroir pour les bases de données qui doivent être protégés. Configurer la mise en miroir dans le mode de sécurité élevé.
3.  Configurer la récupération de Site sur le site local basé sur l’environnement ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [serveur VMware/physique](site-recovery-vmware-to-azure-classic.md).
4.  Utiliser la réplication de récupération de Site pour répliquer la nouvelle instance de SQL Server sur Azure. Il s’agit d’une copie de pages en vis-à-vis de sécurité élevée et donc il allez synchronisé avec le cluster principal, mais il allez être répliquée vers Azure à l’aide de la réplication de récupération de Site.

La figure suivante illustre cette configuration.

![Cluster standard](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Considérations relatives à la restauration

Pour les clusters standards SQL, retour arrière après un basculement non planifié nécessitent une sauvegarde SQL et restaurer à partir de l’instance de pages en vis-à-vis pour le cluster d’origine et rétablir les pages en vis-à-vis.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-best-practices.md) sur l’obtention de prêt à déployer récupération de Site.










 