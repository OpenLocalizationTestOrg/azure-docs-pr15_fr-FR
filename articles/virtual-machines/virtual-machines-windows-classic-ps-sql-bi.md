<properties
    pageTitle="Analyse décisionnelle SQL Server | Microsoft Azure"
    description="Cette rubrique utilise les ressources créées avec le modèle de déploiement classique et décrit les fonctionnalités Business Intelligence (BI) disponibles pour SQL Server s’exécutant sur Machines virtuelles Azure (machines virtuelles)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>À la décision SQL Server dans des Machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

La galerie Machine virtuelle de Microsoft Azure comprend des images qui contiennent une installation de SQL Server. Les éditions de SQL Server pris en charge dans les images de la galerie sont les mêmes fichiers d’installation, que vous pouvez installer sur les ordinateurs locaux et des machines virtuelles. Cette rubrique résume les fonctionnalités de SQL Server Business Intelligence (BI) est installées sur les images et les étapes de configuration nécessaires après qu’une machine virtuelle est mis en service. Cette rubrique décrit également topologies de déploiement pris en charge pour les fonctionnalités de décisionnel et des pratiques recommandées.

## <a name="license-considerations"></a>Considérations relatives à la licence

Il existe deux manières de licence SQL Server dans Microsoft Azure virtuelles Machines :

1. Avantages de mobilité une licence qui font partie de Software Assurance. Pour plus d’informations, voir [Mobilité licence via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Payer par taux horaire du Machines virtuelles Azure avec SQL Server est installé. Consultez la section « SQL Server » dans [Machines virtuelles tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Pour plus d’informations sur les taux actuels et contrat de licences, voir [FAQ sur les licences Machines virtuelles](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server Images disponibles dans la galerie Machine virtuelle Azure

La galerie Machine virtuelle de Microsoft Azure inclut plusieurs images contenant Microsoft SQL Server. Le logiciel installé sur les images de machine virtuelle varie en fonction de la version du système d’exploitation et la version de SQL Server. La liste d’images disponibles dans la galerie Azure machine virtuelle change fréquemment.

![Image SQL dans la galerie de machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Le script PowerShell suivant retourne la liste d’images Azure contenant « SQL Server » dans la ImageName :

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Pour plus d’informations sur les éditions et fonctionnalités pris en charge par SQL Server, voir les rubriques suivantes :

- [Éditions de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Fonctionnalités prises en charge par les éditions de SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Fonctionnalités BI installées sur les Images de la galerie Machine virtuelle SQL Server

Le tableau suivant récapitule les fonctionnalités Business Intelligence installées sur les images de galerie Machine virtuelle de Microsoft Azure courantes pour SQL Server »

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 Standard

- SQL Server 2012 Service Pack 2 Enterprise

- SQL Server 2012 Service Pack 2 Standard

|Fonctionnalité de BI de SQL Server|Installé sur l’image de la galerie|Notes|
|---|---|---|
|**Reporting Services en Mode natif**|Oui|Installé mais nécessite une configuration, y compris l’URL du Gestionnaire de rapports. Consultez la section [Configurer Reporting Services](#configure-reporting-services).|
|**Mode SharePoint pour Reporting Services**|N°|L’image de la galerie Machine virtuelle de Microsoft Azure n’inclut pas SharePoint ou SharePoint fichiers d’installation. <sup>1</sup>|
|**Exploration de données et multidimensionnels Analysis Services (OLAP)**|Oui|Installé et configuré en tant que l’instance Analysis Services par défaut|
|**Tabulaire Analysis Services**|N°|Prise en charge dans SQL Server 2012, 2014 et 2016 images, mais il n'est pas installé par défaut. Installer une autre instance d’Analysis Services. Voir la section installer d’autres Services de SQL Server et les fonctionnalités dans cette rubrique.|
|**PowerPivot Analysis Services pour SharePoint**|N°|L’image de la galerie Machine virtuelle de Microsoft Azure n’inclut pas SharePoint ou SharePoint fichiers d’installation. <sup>1</sup>|

<sup>1</sup> pour plus d’informations sur SharePoint et Azure machines virtuelles, voir [Microsoft Azure Architectures pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) et [Déploiement de SharePoint Microsoft Azure machines virtuelles en fonctionnement](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Exécutez la commande PowerShell suivante pour obtenir une liste des services installés qui contiennent « SQL » dans le nom du service.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recommandations générales et des pratiques recommandées

- La taille minimale recommandée pour une machine virtuelle est la **cellule A3** lors de l’utilisation de SQL Server Enterprise Edition. La taille de la machine virtuelle **A4** est recommandée pour les déploiements SQL Server BI Analysis Services et Reporting Services.

    Pour plus d’informations sur les tailles de mémoire virtuelle en cours, consultez [Tailles Machine virtuelle pour Azure](virtual-machines-linux-sizes.md).

- Une meilleure pratique pour la gestion de disque consiste à stocker les données, journal et les fichiers de sauvegarde de lecteurs autres que **C**: et **D**:. Par exemple, créer des disques de données **E**: et **F**:.

    - Le lecteur de mise en cache de stratégie pour le lecteur par défaut **C**: n’est pas optimale pour l’utilisation de données.

    - **D**: lecteur est un lecteur temporaire qui est utilisé principalement pour le fichier de page. **D**: lecteur n’est pas conservée et n’est pas enregistré dans le stockage blob. Gestion des tâches comme une modification de la taille de la machine virtuelle réinitialiser le **D**: lecteur. Il est recommandé de **pas** utiliser le **D**: lecteur pour les fichiers de base de données, y compris tempdb.

    Pour plus d’informations sur la création et de joindre des disques, voir [l’insertion d’un disque de données pour une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md).

- Arrêter ou désinstaller les services que vous ne souhaitez pas utiliser. Par exemple si la machine virtuelle est utilisée uniquement pour Reporting Services, arrêter ou désinstaller Analysis Services et SQL Server Integration Services. L’image suivante est un exemple de ces services sont démarrés par défaut.

    ![Services de SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] Le moteur de base de données SQL Server est obligatoire dans les scénarios BI pris en charge. Dans un seul serveur topologie machine virtuelle, le moteur de base de données est nécessaire pour s’exécuter sur la même machine virtuelle.

    Pour plus d’informations, consultez la rubrique suivante : [Désinstaller Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) et [désinstaller une Instance de Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Recherchez **Windows Update** nouveau 'mises à jour importantes ». Les images de Machine virtuelle de Microsoft Azure sont actualisées fréquemment ; Toutefois les mises à jour importantes peuvent devenir disponibles à partir de **Windows Update** après la dernière actualisation de l’image de la machine virtuelle.

## <a name="example-deployment-topologies"></a>Exemple de topologie de déploiement

Voici des exemples de déploiement qui utilisent des Machines virtuelles de Microsoft Azure. Les topologies dans ces diagrammes sont uniquement les topologies possibles, que vous pouvez utiliser des fonctionnalités de SQL Server BI Machines virtuelles de Microsoft Azure.

### <a name="single-virtual-machine"></a>Machine virtuelle unique

Analysis Services, Reporting Services, moteur de base de données SQL Server et sources de données sur une seule machine virtuelle.

![scénario IAS BI avec 1 machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Deux Machines virtuelles

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sur une seule machine virtuelle. Ce déploiement comprend les bases de données de serveur de rapports.

- Sources de données sur un deuxième ordinateur virtuel. La deuxième machine virtuelle inclut le moteur de base de données SQL Server comme source de données.

![scénario iaas BI avec 2 machines virtuelles](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure mixte – données sur base de données SQL Azure

- Analysis Services, Reporting Services et le moteur de base de données SQL Server sur une seule machine virtuelle. Ce déploiement comprend les bases de données de serveur de rapports.

- Source de données est la base de données SQL Azure.

![machine virtuelle iaas BI scénarios et AzureSQL en tant que source de données](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybride – données locales

- Dans cet exemple de déploiement Analysis Services, Reporting Services et le moteur de base de données SQL Server s’exécutent sur une seule machine virtuelle. La machine virtuelle héberge les bases de données de serveur de rapports. L’ordinateur virtuel est joint à un domaine local via le réseau virtuel Azure ou certaines autres VPN tunnel solution.

- Source de données est en local.

![machine virtuelle iaas BI scénarios et sur les sources de données de site](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuration du Mode natif Reporting Services

L’image de la galerie machine virtuelle pour SQL Server inclut mode natif des Services de création de rapports installé, alors que le serveur de rapports n’est pas configuré. Les étapes décrites dans cette section Configurer le serveur de rapports Reporting Services. Pour plus d’informations sur la configuration du mode natif des Services de création de rapports, voir [Installer Reporting Services Native Mode rapport Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Pour un contenu similaire qui utilise des scripts Windows PowerShell pour configurer le serveur de rapports, voir [Utiliser PowerShell pour créer un Azure machine virtuelle avec un serveur en Mode natif rapport](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Se connecter à la Machine virtuelle et démarrer le Gestionnaire de Configuration Reporting Services

Il existe deux flux de travail courantes de connexion à une Machine virtuelle Azure :

- Se connecter dans la barre d’outils, cliquez sur le nom de la machine virtuelle, puis cliquez sur **se connecter**. Une connexion Bureau à distance s’ouvre et le nom de l’ordinateur est rempli automatiquement.

    ![se connecter à azure machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Se connecter à la machine virtuelle Windows connexion Bureau à distance. Dans l’interface utilisateur du Bureau à distance :

    1. Tapez le **nom du service cloud** sous le nom d’ordinateur.

    1. Tapez deux-points ( :)) et le numéro de port public qui est configuré pour l’extrémité de bureau à distance TCP.

        Myservice.cloudapp.NET:63133

        Pour plus d’informations, voir [qu’est un service cloud ?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Démarrer le Gestionnaire de Configuration de Services de création de rapports.**

1. Dans **Windows Server 2012**:

1. À partir de l’écran de **démarrage** , tapez **Reporting Services** pour afficher la liste des applications.

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , cliquez sur **Exécuter en tant qu’administrateur**.

1. Dans **Windows Server 2008 R2**:

1. Cliquez sur **Démarrer**, puis cliquez sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2016**.

1. Cliquez sur **Outils de Configuration**.

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , cliquez sur **Exécuter en tant qu’administrateur**.

Ou

1. Cliquez sur **Démarrer**.

1. Dans la boîte de dialogue **Rechercher les programmes et fichiers** tapez **reporting services**. Si la machine virtuelle exécutant Windows Server 2012, tapez sur l’écran de démarrage de Windows Server 2012 **reporting services** .

1. Cliquez sur **Gestionnaire de Configuration de Reporting Services** , cliquez sur **Exécuter en tant qu’administrateur**.

    ![recherche du Gestionnaire de configuration ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurer Reporting Services

**Compte de service et URL du service web :**

1. Vérifiez que le **Nom du serveur** est le nom du serveur local, cliquez sur **se connecter**.

1. Notez le **Nom du serveur de rapports de base de données**vide. La base de données est créée à l’issue de la configuration.

1. Vérifiez que l' **État du serveur rapport** est **mise en route**. Si vous souhaitez vérifier que le service dans le Gestionnaire de serveur Windows, le service est le Service Windows **SQL Server Reporting Services** .

1. Cliquez sur le **Compte de Service** et remplacez le compte selon vos besoins. Si la machine virtuelle est utilisée dans un environnement joint non à un domaine, le compte **ReportServer** intégré est suffisant. Pour plus d’informations sur le compte de service, voir le [Compte de Service](https://msdn.microsoft.com/library/ms189964.aspx).

1. Dans le volet gauche, cliquez sur **URL du Service Web** .

1. Cliquez sur **Appliquer** pour configurer les valeurs par défaut.

1. Notez les **URL de Service de rapport serveur Web**. Remarque le port TCP par défaut est 80 et fait partie de l’URL. Dans une étape ultérieure, vous créez un point de terminaison Microsoft Azure Machine virtuelle pour le port.

1. Dans le volet **des résultats** , vérifiez les actions terminées avec succès.

**Base de données :**

1. Dans le volet gauche, cliquez sur **base de données** .

1. Cliquez sur **Modifier la base de données**.

1. Vérifier que l’option **créer une nouvelle base de données de rapport serveur** est sélectionnée, puis sur Suivant.

1. Vérifiez **Le nom de serveur** , puis cliquez sur **Tester la connexion**.

1. Si le résultat est **tester la connexion a réussi**, cliquez sur **OK** , puis sur **suivant**.

1. Notez le nom de la base de données est **ReportServer** et le **serveur de rapports en mode** **natif** , puis cliquez sur **suivant**.

1. Dans la page **informations d’identification** , cliquez sur **suivant** .

1. Dans la page de **Résumé** , cliquez sur **suivant** .

1. Dans la page de **l’avancement et fin** , cliquez sur **suivant** .

**Web URL du portail, ou le Gestionnaire de rapports pour 2012 et 2014 :**

1. Cliquez sur **URL du portail Web**, ou **Le Gestionnaire de rapports** pour 2014 et 2012, dans le volet gauche.

1. Cliquez sur **Appliquer**.

1. Dans le volet **des résultats** , vérifiez les actions terminées avec succès.

1. Cliquez sur **Quitter**.

Pour plus d’informations sur les autorisations de serveur de rapports, voir [Octroi d’autorisations sur un serveur de rapports en Mode natif](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Accédez au Gestionnaire de rapport local

Pour vérifier la configuration, accédez au Gestionnaire de rapports sur l’ordinateur virtuel.

1. Sur la machine virtuelle, démarrez Internet Explorer en tant qu’administrateur.

1. Accédez à http://localhost/reports sur l’ordinateur virtuel.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Pour se connecter au portail web à distance ou Gestionnaire de rapports pour 2014 et 2012

Si vous voulez vous connecter au portail web ou au Gestionnaire de rapports pour 2014 et 2012, sur l’ordinateur virtuel depuis un ordinateur distant, créez une nouvelle machine virtuelle point de terminaison TCP. Par défaut, le serveur de rapports écoute pour les requêtes HTTP sur le **port 80**. Si vous configurez l’URL du serveur rapport pour utiliser un autre port, vous devez spécifier ce numéro de port dans les instructions suivantes.

1. Créer un point de terminaison de la Machine virtuelle du Port TCP 80. Pour plus d’informations, voir la section [points de terminaison de Machine virtuelle et Ports de pare-feu](#virtual-machine-endpoints-and-firewall-ports) dans ce document.

1. Ouvrez le port 80 dans le pare-feu la machine virtuelle.

1. Accédez au portail web ou au Gestionnaire de rapports, en utilisant Machine virtuelle Azure **Nom DNS** comme le nom du serveur dans l’URL. Par exemple :

    **Serveur de rapports**: http://uebi.cloudapp.net/reportserver  **portail Web**: http://uebi.cloudapp.net/reports

    [Configurer un pare-feu pour accéder au serveur de rapport](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur la Machine virtuelle Azure

Le tableau suivant résume quelques-unes des options disponibles pour publier des rapports existants à partir d’un ordinateur local pour le serveur de rapports hébergé sur la Machine virtuelle de Microsoft Azure :

- **Le Générateur de rapports**: la machine virtuelle inclut le clic-une fois la version de Microsoft SQL Server Report Builder pour SQL 2014 et 2012. Pour démarrer déclarer les heures générateur le premier sur l’ordinateur virtuel avec SQL 2016 :

    1. Démarrez votre navigateur avec des privilèges d’administrateur.

    1. Accédez au portail web, sur l’ordinateur virtuel et sélectionnez l’icône **Télécharger** dans le coin supérieur droit.
    
    1. Sélectionnez **le Générateur de rapports**.

    Pour plus d’informations, consultez [Démarrer le Générateur de rapports](https://msdn.microsoft.com/library/ms159221.aspx).

- **SQL Server Data Tools**: machine virtuelle : SQL Server Data Tools est installé sur l’ordinateur virtuel et peut servir à créer des **Projets de serveur de rapports** et des rapports sur l’ordinateur virtuel. SQL Server Data Tools peuvent publier les rapports sur le serveur de rapports sur l’ordinateur virtuel.

- **SQL Server Data Tools : distant**: sur votre ordinateur local, créez un projet de Reporting Services dans SQL Server Data Tools contenant des rapports Reporting Services. Configurer le projet pour vous connecter à l’URL du service web.

    ![Propriétés du projet ssdt pour projet SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Créer un. Disque dur disque dur virtuel qui contient des rapports, puis télécharger et joindre le lecteur.

    1. Créer un. Disque dur virtuel disque dur sur votre ordinateur local qui contient vos rapports.

    1. Créer et installer un certificat de gestion.

    1. Télécharger le fichier de disque dur virtuel dans Azure à l’aide de l’applet de commande Add-AzureVHD [créer et télécharger un disque dur virtuel du serveur de Windows Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Connectez le disque à la machine virtuelle.

## <a name="install-other-sql-server-services-and-features"></a>Installer d’autres Services de SQL Server et les fonctionnalités

Pour installer d’autres services de SQL Server, tels que Analysis Services en mode tabulaire, exécutez l’Assistant de configuration SQL server. Les fichiers d’installation sont sur le disque local de la machine virtuelle.

1. Cliquez sur **Démarrer** , puis sur **Tous les programmes**.

1. Cliquez sur **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** ou **Microsoft SQL Server 2012** , puis sur **Outils de Configuration**.

1. Cliquez sur **Centre d’Installation de SQL Server**.

Ou exécuter C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] La première fois que vous exécutez le programme d’installation de SQL Server, les fichiers d’installation plus peuvent être téléchargés et nécessitent un redémarrage de la machine virtuelle et un redémarrage de l’installation de SQL Server.
>
>Si vous devez personnaliser à plusieurs reprises l’image sélectionnée à partir de la Machine virtuelle de Microsoft Azure, envisagez de créer votre propre image de SQL Server. Analysis Services SysPrep fonctionnalité a été activée avec SQL Server 2012 SP1 CU2. Pour plus d’informations, voir [Prise en charge de Sysprep pour les rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)et les [Considérations pour l’installation de SQL Server à l’aide de SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) .

### <a name="to-install-analysis-services-tabular-mode"></a>Pour installer le Mode tabulaire Analysis Services

Les étapes décrites dans cette section **résumer** l’installation du mode tabulaire Analysis Services. Pour plus d’informations, voir les rubriques suivantes :

- [Installez Analysis Services en Mode tabulaire](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modélisation tabulaire (Adventure Works didacticiel)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Pour installer le Mode tabulaire Analysis Services :**

1. Dans l’Assistant installation de SQL Server, cliquez sur **l’Installation** dans le volet gauche, puis **installation autonome nouveau SQL server ou ajouter des fonctionnalités à une installation existante**.

    - Si vous voyez le **Rechercher un dossier**, accédez à c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full, puis sur **Ok**.

1. Dans la page de mises à jour de produit, cliquez sur **suivant** .

1. Dans la page **Type d’Installation** , sélectionnez **effectuer une nouvelle installation de SQL Server** , cliquez sur **suivant**.

1. Dans la page **Le rôle d’installation** , cliquez sur **L’Installation de SQL Server fonctionnalités**.

1. Dans la page de **Sélection de fonctionnalités** , cliquez sur **Analysis Services**.

1. Dans la page **Configuration de l’Instance** , tapez un nom descriptif, tel que **tabulaire** dans les zones de texte **Instance nommée** et **Id de l’Instance** .

1. Dans la page de **Configuration d’Analysis Services** , sélectionnez **Mode tabulaire**. Ajouter l’utilisateur actuel à la liste des autorisations d’administration.

1. Terminer et fermer l’Assistant installation de SQL Server.

## <a name="analysis-services-configuration"></a>Configuration d’Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Accès à distance pour le serveur Analysis Services

Serveur Analysis Services prend uniquement en charge l’authentification windows. Pour accéder à distance Analysis Services à partir des applications clientes telles que SQL Server Management Studio ou SQL Server Data Tools, la machine virtuelle doit être associé à votre domaine local, à l’aide de réseaux virtuels Azure. Pour plus d’informations, voir [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

Une **instance par défaut** de Analysis Services écoute de ports TCP **2383**. Ouvrez le port dans le pare-feu machines virtuelles. Port **2383**écoute également une instance nommée groupée de Analysis Services.

Pour une **instance nommée** d’Analysis Services, le service SQL Server navigateur est requis pour gérer l’accès de port. La configuration par défaut de SQL Server navigateur est port **2382**.

Dans le pare-feu machines virtuelles, ouvrez port **2382** et créez un statique Analysis Services appelé port instance.

1. Pour vérifier les ports qui sont déjà en cours d’utilisation sur l’ordinateur virtuel et quel processus utilise les ports, exécutez la commande suivante avec des privilèges d’administrateur :

        netstat /ao

1. Utiliser SQL Server Management Studio pour créer un statique Analysis Services port instance en mettant à jour « Port » valeur nommée dans sous forme de tableau en tant que propriétés générales de l’instance. Pour plus d’informations, voir la « utiliser un port fixe pour une valeur par défaut ou instance nommée » dans [configurer le pare-feu Windows pour autoriser l’accès Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Redémarrez l’instance tabulaire du service Analysis Services.

Pour plus d’informations, voir la section **points de terminaison de Machine virtuelle et Ports de pare-feu** dans ce document.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Ports de pare-feu et points de terminaison de Machine virtuelle

Cette section récapitule les points de terminaison de Microsoft Azure Machine virtuelle pour créer et de ports à ouvrir dans le pare-feu machine virtuelle. Le tableau suivant résume les ports **TCP** pour créer des points de terminaison pour et les ports à ouvrir dans le pare-feu machines virtuelles.

- Si vous utilisez un ordinateur virtuel unique et les deux éléments suivants sont vraies, vous n’avez pas besoin créer des points de terminaison de machine virtuelle et vous n’avez pas besoin d’ouvrir les ports dans le pare-feu sur l’ordinateur virtuel.

    - Vous ne connectez pas à distance pour les fonctionnalités de SQL Server sur l’ordinateur virtuel. Établissement d’une connexion Bureau à distance de la machine virtuelle et accès aux fonctions SQL Server localement sur l’ordinateur virtuel ne sont pas considérée une connexion à distance pour les fonctionnalités de SQL Server.

    - Vous ne pas participer à la machine virtuelle à un domaine local via réseau virtuel Azure ou une autre solution tunnel VPN.

- Si la machine virtuelle n’est pas joint à un domaine mais que vous souhaitez à distance à se connecter aux fonctionnalités sur machine virtuelle SQL Server :

    - Ouvrez les ports dans le pare-feu sur l’ordinateur virtuel.

    - Créer des points de terminaison machine virtuelle pour les ports indiqués (*).

- Si la machine virtuelle est joint à un domaine à l’aide d’un tunnel VPN telles que la mise en réseau virtuel Azure, les points de terminaison ne sont pas nécessaires. Toutefois, ouvrez les ports dans le pare-feu sur l’ordinateur virtuel.

  	|Port|Type|Description|
|---|---|---|
|**80**|TCP|Serveur de rapports d’accès à distance (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|Explorateur SQL Server. Cela est nécessaire lors de la machine virtuelle dans joint à un domaine.|
|**2382**|TCP|Explorateur SQL Server.|
|**2383**|TCP|Instance par défaut de SQL Server Analysis Services et instances nommées groupés.|
|**Définis par l’utilisateur**|TCP|Créer un statique Analysis Services nommé port instance pour un numéro de port que vous choisissez, puis débloquez le numéro de port dans le pare-feu.|

Pour plus d’informations sur la création de points de terminaison, voir les rubriques suivantes :

- Créer des points de terminaison :[comment configurer une Machine virtuelle des points de terminaison](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server : Voir la section « Configuration complète étapes pour vous connecter à la machine virtuelle en utilisant SQL Server Management Studio » de [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Le diagramme suivant illustre les ports à ouvrir dans le pare-feu machine virtuelle pour autoriser l’accès à distance aux fonctionnalités et des composants sur l’ordinateur virtuel.

![ports ouvrir pour les applications bi dans machines virtuelles Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Ressources

- Passez en revue la politique de support pour les logiciels de serveur Microsoft utilisés dans l’environnement de Machine virtuelle Azure. La rubrique suivante résume prise en charge des fonctionnalités telles que BitLocker basculement et équilibrage de charge. [Logiciel serveur Microsoft prend en charge pour le Machines virtuelles Azure](http://support.microsoft.com/kb/2721672).

- [SQL Server sur vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md)

- [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [L’insertion d’un disque de données pour une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md)

- [Migration d’une base de données vers SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md)

- [Déterminer le Mode de serveur d’une Instance Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modélisation multidimensionnelle (Adventure Works didacticiel)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centre de Documentation Azure](https://azure.microsoft.com/documentation/)

- [À l’aide de Power BI dans un environnement hybride](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Envoyer des commentaires et des informations de contact via Microsoft SQL Server se connecter](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenu de la Communauté

- [Gestion de base de données SQL Azure avec PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
