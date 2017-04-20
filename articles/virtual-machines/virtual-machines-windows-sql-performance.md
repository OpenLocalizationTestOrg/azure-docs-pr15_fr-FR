<properties
    pageTitle="Les procédures recommandées pour SQL Server | Microsoft Azure"
    description="Décrit les méthodes conseillées pour l’optimisation des performances de SQL Server dans Microsoft Azure machines virtuelles."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Les procédures recommandées pour SQL Server dans le Machines virtuelles Azure

## <a name="overview"></a>Vue d’ensemble

Cette rubrique fournit des recommandations pour l’optimisation des performances de SQL Server dans Microsoft Azure Virtual Machine. Pendant l’exécution de SQL Server dans le Machines virtuelles Azure, nous vous recommandons de continuer à l’aide du même performances de base de données optimisation des options qui s’applique à SQL Server dans un environnement de serveur local. Toutefois, les performances d’une base de données relationnel dans un nuage public dépendant de nombreux facteurs tels que la taille d’une machine virtuelle et la configuration des disques de données.

Lorsque vous créez des images de SQL Server, [prendre en compte vos ordinateurs virtuels dans le portail Azure de mise en service](virtual-machines-windows-portal-sql-server-provision.md). Machines virtuelles du serveur SQL sa mise en service dans le portail avec le Gestionnaire de ressources mise en œuvre toutes ces recommandations, y compris la configuration du stockage.

Cet article se concentre sur l’obtention de performances *optimales* pour SQL Server sur machines virtuelles Azure. Si votre charge de travail est alléger la charge, vous pouvez être amené pas chaque optimisation présentée ci-après. Pensez à vos besoins de performance et les modèles de charge de travail lorsque vous évaluez ces recommandations.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server Azure machines virtuelles en fonctionnement :

|Zone|Optimisations|
|---|---|
|[Taille de mémoire virtuelle](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou une version ultérieure pour SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou une version ultérieure pour les éditions de SQL Standard et sur le Web.|
|[Espace de stockage](#storage-guidance)|Utilisez [le stockage Premium](../storage/storage-premium-storage.md). Stockage standard est recommandé uniquement pour le développement/test.<br/><br/>Conserver le [compte de stockage](../storage/storage-create-storage-account.md) et de la machine virtuelle SQL Server dans la même région.<br/><br/>Désactiver Azure [stockage geo redondantes](../storage/storage-redundancy.md) (geo-réplication) sur le compte de stockage.|
|[Disques](#disks-guidance)|Utiliser un minimum de 2 [P30 disques](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 pour les fichiers journaux ; 1 pour les fichiers de données et TempDB).<br/><br/>Évitez d’utiliser le système d’exploitation ou disques temporaires pour le stockage de base de données ou.<br/><br/>Activer le cache des disques hébergeant les fichiers de données et TempDB de lecture.<br/><br/>N’activez pas la mise en cache sur disques qui héberge le fichier journal.<br/><br/>Important : Arrêter le service SQL Server lorsque vous modifiez les paramètres de cache d’un disque machine virtuelle Azure.<br/><br/>Répartissez plusieurs disques de données Azure pour augmenter la productivité IO.<br/><br/>Format avec présentées tailles d’allocation.|
|[E/S](#io-guidance)|Activer la compression de page de base de données.<br/><br/>Activer l’initialisation instantanée des fichiers pour les fichiers de données.<br/><br/>Limiter ou désactiver la croissance automatique sur la base de données.<br/><br/>Désactiver la réduction automatique de la base de données.<br/><br/>Déplacer toutes les bases de données à disques de données, y compris les bases de données système.<br/><br/>Déplacer des répertoires de fichier des suivi et journaux de SQL Server d’erreur à disques de données.<br/><br/>Configurer des emplacements de fichiers de sauvegarde et de base de données par défaut.<br/><br/>Activer les pages verrouillées.<br/><br/>Appliquer les correctifs de performances de SQL Server.|
|[Fonctionnalités spécifiques](#feature-specific-guidance)|Sauvegarder directement au stockage d’objets blob.|

Pour plus d’informations sur *comment* et *Pourquoi* pour rendre ces optimisations, veuillez consulter les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils de taille de la mémoire virtuelle

Pour les applications sensibles de performances, il est recommandé que vous utilisez les [tailles machines virtuelles](virtual-machines-windows-sizes.md)suivantes :

- **SQL Server Enterprise Edition**: DS3 ou une version ultérieure

- **SQL Server Standard et les éditions Web**: DS2 ou une version ultérieure


## <a name="storage-guidance"></a>Conseils de stockage

Prise en charge de machines virtuelles série DS (ainsi que DSv2-séries et GS) [Stockage Premium](../storage/storage-premium-storage.md). Stockage Premium est recommandée pour toutes les charges de travail.

> [AZURE.WARNING] Stockage standard a latence et la bande passante variés et est recommandé uniquement pour les charges de travail de développement/test. Charges de travail doivent utiliser le stockage Premium.

En outre, nous vous recommandons de créer votre compte de stockage Azure dans le même centre de données en tant que vos machines virtuelles SQL Server pour réduire les délais de transfert. Lorsque vous créez un compte de stockage, désactivez la réplication geo comme ordre d’écriture cohérent sur plusieurs disques n’est pas garantie. À la place, envisagez de configurer une technologie de récupération d’urgence SQL Server entre deux Azure centres de données. Pour plus d’informations, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Conseils de disques

Il existe trois types de disque principale sur un ordinateur virtuel Azure :

- **Disque du système d’exploitation**: lorsque vous créez une Machine virtuelle Azure, la plateforme s’attache au moins un disque (appelé le lecteur **C** ) de la machine virtuelle pour votre système d’exploitation disque. Ce disque est un disque dur virtuel stocké sous la forme d’un objet dans le stockage blob de page.
- **Disque temporaire**: Machines virtuelles Azure contiennent un autre disque appelé le disque temporaire (appelé le **D**: lecteur). Il s’agit d’un disque sur le nœud qui peut être utilisé pour l’espace de travail.
- **Disques de données**: vous pouvez également joindre des disques supplémentaires à votre machine virtuelle en tant que disques de données, et ces seront stockés dans le stockage en tant que BLOB de page.

Les sections suivantes décrivent des recommandations sur l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel que vous pouvez démarrer et monter sous forme d’une version du système d’exploitation en cours d’exécution et est appelé lecteur **C** .

Par défaut mise en cache de stratégie sur le disque système d’exploitation est **En lecture/écriture**. Pour les applications sensibles de performances, nous vous recommandons d’utiliser disques de données au lieu du disque système d’exploitation. Consultez la section aux données disques ci-dessous.

### <a name="temporary-disk"></a>Disque temporaire

Le lecteur de stockage temporaire, comme le **D**: le lecteur, n’est pas conservée à Azure blob storage. Ne stockez pas vos fichiers de base de données utilisateur ou les fichiers journaux des transactions utilisateur sur le **D**: lecteur.

Pour série D, Dv2 série et machines virtuelles série G, le lecteur temporaire de ces ordinateurs virtuels est basé sur les SSD. Si votre charge de travail est une utilisation intensive de TempDB (par exemple, pour les objets temporaires ou jointures complexes), le stockage TempDB sur le lecteur **D** peut entraîner débit TempDB supérieur et inférieure latence TempDB.

Pour les machines virtuelles qui prennent en charge le stockage Premium (série DS, DSv2-séries et GS), nous vous recommandons de stockage TempDB et/ou tampon Pool Extensions sur un disque qui prend en charge le stockage Premium avec une mise en cache en lecture. Il existe une exception à cette recommandation ; Si votre utilisation TempDB est écriture accrue, vous pouvez obtenir améliorer les performances en stockant TempDB sur le lecteur **D** local, qui est également basée sur SSD sur ces tailles machine.

### <a name="data-disks"></a>Disques de données

- **Utiliser des disques de données pour les fichiers journaux et de données**: au minimum, utilisez 2 Premium de stockage [P30 disques](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) où un disque contient les fichiers journaux et l’autre contient les fichiers de données et TempDB.

- **Répartition des disques**: pour plus de débit, vous pouvez ajouter des données supplémentaires disques et utilisent la répartition. Pour déterminer le nombre de disques de données, vous devez analyser le nombre de sorties par disponibles pour vos données et journal disques. Pour obtenir ces informations, consultez les tableaux sur sorties par la taille de la [taille de mémoire virtuelle](virtual-machines-windows-sizes.md) et le disque dans l’article suivant : [Utilisation du stockage Premium pour les disques](../storage/storage-premium-storage.md). Utilisez la commande suivante aux instructions suivantes :

    - Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx). Définir la taille de répartition à 64 Ko pour les charges de travail OLTP et 256 Ko pour les charges de travail entrepôt de données pour éviter l’impact sur les performances en raison d’alignement partition. En outre, définir le nombre de colonnes = nombre de disques physiques. Pour configurer un espace de stockage avec plus de 8 disques, vous devez utiliser PowerShell (pas le Gestionnaire de serveur UI) explicitement définir le nombre de colonnes en fonction du nombre de disques. Pour plus d’informations sur la configuration [Des espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx), voir [Applets de commande espace de stockage dans Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Pour Windows 2008 R2 ou antérieur, vous pouvez utiliser des disques dynamiques (volumes OS réparties) et la taille de répartition est toujours 64 Ko. Notez que cette option est déconseillée à partir de Windows 8/Windows Server 2012. Pour plus d’informations, consultez la prise en charge de [Service de disque virtuel est en cours de transition API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Si votre charge de travail n’est pas connecté intensive et sorties par dédié n’a pas besoin, vous pouvez configurer une pool de stockage. Dans le cas contraire, créez deux pools de stockage, une pour les fichiers journaux et un autre pool de stockage pour les fichiers de données et TempDB. Déterminer le nombre de disques associés à chaque pool de stockage en fonction de vos attentes charge. N’oubliez pas que différentes tailles de machine virtuelle autoriser différents nombres d’associés disques de données. Pour plus d’informations, voir [formats pour les Machines virtuelles](virtual-machines-windows-sizes.md).

    - Si vous n’utilisez pas de stockage Premium (scénarios de développement/test), il est recommandé pour ajouter le nombre maximal de disques de données pris en charge par votre [taille de mémoire virtuelle](virtual-machines-windows-sizes.md) , utilisez agrégat.

- **Stratégie de mise en cache**: disques de données pour le stockage de Premium, activer le cache de lecture sur les disques de données qui héberge vos fichiers de données et TempDB uniquement. Si vous n’utilisez pas de stockage Premium, n’activez pas toute la mise en cache sur les disques de données. Pour obtenir des instructions sur la configuration de la mise en cache du disque, consultez les rubriques suivantes : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [Jeu AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Arrêter le service SQL Server lorsque vous modifiez le paramètre de cache de disques machine virtuelle Azure pour éviter d’éventuels problèmes de base de données.

- **Taille d’unité d’allocation NTFS**: lors de la mise en forme le disque de données, il est recommandé d’utiliser une taille d’unité d’allocation de 64 Ko pour les données et fichiers journaux aussi TempDB.

- **Meilleures pratiques de gestion de disque**: lorsque suppression d’un disque de données ou en modifiant son type de cache, arrêtez le service SQL Server lors de la modification. Lorsque les paramètres de mise en cache sont modifiées sur le disque du système d’exploitation, Azure cesse de la machine virtuelle, modifie le type de cache et redémarrage de la machine virtuelle. Lorsque les paramètres de cache d’un disque de données sont modifiées, la machine virtuelle n’est pas interrompue, mais le disque de données est détaché de la machine virtuelle lors de la modification et puis reconnectez.

    >[AZURE.WARNING] Pour arrêter le service SQL Server pendant ces opérations peut provoquer la base de données.

## <a name="io-guidance"></a>Conseils d’e/s

- Le meilleur parti de stockage Premium est obtenu lorsque vous parallélisez votre application et requêtes. Stockage Premium est conçu pour les scénarios où la file d’attente IO étant supérieure à 1, vous verrez des gains de performances peu ou pas pour les demandes de série thread unique (même si elles sont stockage intensive). Par exemple, cela peut affecter les résultats des tests de thread unique des outils d’analyse de performances, par exemple SQLIO.

- Envisagez d’utiliser la [compression de page de base de données](https://msdn.microsoft.com/library/cc280449.aspx) qu’il peut aider à améliorer les performances de charges de travail intensives e/s. Toutefois, la compression de données peut augmenter la consommation processeur sur le serveur de base de données.

- Pensez à activer l’initialisation instantanée des fichiers réduire le temps nécessaire pour l’allocation de fichier d’origine. Pour tirer parti de l’initialisation instantanée des fichiers, vous accordez au compte de service SQL Server (MSSQLSERVER) avec autorisation SE_MANAGE_VOLUME_NAME et l’ajoutez à la stratégie de sécurité **d’Effectuer les tâches de Maintenance en Volume** . Si vous utilisez une image de la plateforme SQL Server pour Azure, le compte de service par défaut (NT Service\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **d’Effectuer les tâches de Maintenance en Volume** . En d’autres termes, initialisation instantanée des fichiers n’est pas activée dans une image de la plateforme SQL Server Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **d’Effectuer les tâches de Maintenance Volume** , redémarrez le service SQL Server. Vous pouvez rencontrer considérations relatives à la sécurité pour l’utilisation de cette fonctionnalité. Pour plus d’informations, voir [L’initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

- **étendue automatique** est considéré comme simplement un plan d’urgence pour la croissance inattendue. Ne gérez pas la croissance de vos données et le journal quotidienne avec étendue automatique. Si étendue automatique est utilisée, avant d’augmenter le fichier en utilisant le commutateur taille.

- Vérifiez que la **réduction automatique** est désactivé pour éviter toute surcharge inutile qui peut affecter les performances.

- Déplacer toutes les bases de données à disques de données, y compris les bases de données système. Pour plus d’informations, voir [Déplacer des bases de données système](https://msdn.microsoft.com/library/ms345408.aspx).

- Déplacer des répertoires de fichier des suivi et journaux de SQL Server d’erreur à disques de données. Il est possible dans le Gestionnaire de Configuration SQL Server par clic droit sur l’instance SQL Server et en sélectionnant Propriétés. Les paramètres du fichier journal et suivi des messages d’erreur peuvent être modifiées dans l’onglet **Paramètres de démarrage** . Le répertoire de l’image est spécifié dans l’onglet **Options avancées** . La capture d’écran suivante montre où rechercher le paramètre de démarrage du journal d’erreur.

    ![Capture d’écran du journal des erreurs SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Configurer des emplacements de fichiers de sauvegarde et de base de données par défaut. Utilisez les recommandations dans cette rubrique, puis apportez les modifications dans la fenêtre Propriétés du serveur. Pour plus d’informations, voir [Afficher ou modifier les emplacements par défaut pour les données et les fichiers journaux (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La capture d’écran suivante montre l’emplacement d’apporter ces modifications.

    ![Fichiers de sauvegarde et de journal de données SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Activer les pages verrouillées réduire IO et les activités d’échange. Pour plus d’informations, voir [Activer le verrouiller les Pages en mémoire Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Si vous exécutez SQL Server 2012, installez 10 de mise à jour Cumulative de Service Pack 1. Cette mise à jour contient le correctif des mauvaises performances d’e/s lorsque vous exécutez select into, instruction table temporaire dans SQL Server 2012. Pour plus d’informations, voir cet [article de la base de connaissances](http://support.microsoft.com/kb/2958012).

- Compressez les fichiers de données lors du transfert dans/hors de Azure.

## <a name="feature-specific-guidance"></a>Fonctionnalité des recommandations spécifiques

Certains déploiements obtiendrez de meilleures performances supplémentaires à l’aide des techniques de configuration plus avancées. La liste suivante présente certaines des fonctionnalités SQL Server peuvent vous aider à obtenir de meilleures performances :

- **Sauvegarde au stockage Azure**: lorsque vous effectuez des sauvegardes pour SQL Server Azure machines virtuelles, vous pouvez utiliser [La sauvegarde SQL Server URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2 et recommandé pour la sauvegarde disques données attachées. Lorsque vous sauvegarde/restauration vers ou à partir d’Azure stockage, suivez les recommandations fournies à [SQL Server sauvegarde sur URL meilleures pratiques et résolution des problèmes et restauration à partir de sauvegardes stockées dans le stockage Azure](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes à l’aide de [Sauvegarde automatique pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-classic-sql-automated-backup.md).

    Avant SQL Server 2012, vous pouvez utiliser [La sauvegarde SQL Server Azure outil](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut vous aider à augmenter débit de sauvegarde à l’aide de plusieurs cibles répartition sauvegarde.

- **Fichiers de données SQL Server dans Azure**: cette nouvelle fonction, [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. SQL Server en cours d’exécution des fichiers de données dans Azure montre les caractéristiques de performances comparable comme à l’aide de disques de données Azure.

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes intéressé exploration SQL Server et stockage Premium plus en détail, voir l’article [Utiliser le stockage Azure Premium avec SQL Server sur des Machines virtuelles](virtual-machines-windows-classic-sql-server-premium-storage.md).

Meilleures pratiques de sécurité, consultez [Considérations relatives à la sécurité pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-security.md).

Consultez les autres rubriques Machine virtuelle de SQL Server à [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
