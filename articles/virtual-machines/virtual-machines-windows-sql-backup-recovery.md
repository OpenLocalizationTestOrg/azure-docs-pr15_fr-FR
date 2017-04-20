<properties
    pageTitle="Sauvegarde et restauration pour SQL Server | Microsoft Azure"
    description="Décrit les considérations sauvegarder et restaurer des bases de données SQL Server Azure machines virtuelles en fonctionnement en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Sauvegarder et restaurer pour SQL Server dans des Machines virtuelles Azure

## <a name="overview"></a>Vue d’ensemble

Sauvegarder des données dans les bases de données SQL Server est un composant important de la stratégie de protection contre la perte de données en raison des erreurs d’application ou l’utilisateur. Ceci est également vrai pour SQL Server s’exécutant sur Machines virtuelles Azure (machines virtuelles).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Pour SQL Server en cours d’exécution dans Azure machines virtuelles, vous pouvez utilisez native sauvegarde et restauration techniques à l’aide de disques liés à la destination des fichiers de sauvegarde. Toutefois, il existe une limite au nombre de disques que vous pouvez joindre à une machine virtuelle Azure, selon la [taille de la machine virtuelle](virtual-machines-linux-sizes.md). Il existe également la charge de gestion des disques à prendre en compte.

À partir de SQL Server 2014, vous pouvez sauvegarder et restaurer à Microsoft Azure Blob storage. SQL Server 2016 fournit également des améliorations pour cette option. En outre, pour les fichiers de base de données stockées dans Microsoft Azure Blob storage, SQL Server 2016 fournit une option pour sauvegardes quasi instantanées et restaure rapide à l’aide des instantanés Azure. Cet article fournit une vue d’ensemble des options suivantes, puis vous trouverez des informations supplémentaires sur [SQL Server sauvegarde et restauration avec le Service de stockage Blob Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Pour une description des options de sauvegarde des bases de données très volumineuses, voir [Stratégies de sauvegarde de base de données à plusieurs to SQL Server pour le Machines virtuelles Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Les sections ci-dessous incluent des informations spécifiques sur les différentes versions de SQL Server pris en charge dans une machine virtuelle Azure.

## <a name="sql-server-virtual-machines"></a>Machines virtuelles SQL Server

Lors de l’instance SQL Server est en cours d’exécution sur une Machine virtuelle Azure, vos fichiers de base de données se trouvent déjà sur disques de données dans Azure. Ces disques live dans le stockage Blob Azure. Les motifs de sauvegarde de votre base de données et les approches vous prennent maintenant modifier légèrement. Posez-vous les questions suivantes. 

- Vous n’avez plus besoin d’effectuer des sauvegardes de base de données pour fournir une protection contre les pannes de matériel ou des médias, car Microsoft Azure fournit cette protection dans le cadre du service Microsoft Azure.

- Vous avez besoin effectuer des sauvegardes de base de données pour fournir une protection contre les erreurs de l’utilisateur, ou pour des besoins d’archivage, les questions de réglementation ou à des fins d’administration.

- Vous pouvez stocker le fichier de sauvegarde directement dans Azure. Pour plus d’informations, voir les sections suivantes fournissent des instructions pour les différentes versions de SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 prend en charge les fonctionnalités de [sauvegarde et restauration avec des objets BLOB Azure](https://msdn.microsoft.com/library/jj919148.aspx) figurant dans SQL Server 2014. Mais il inclut également les améliorations suivantes :

| Amélioration 2016               | Plus d’informations                          |
|---------------------|-------------------------------|
| **Répartition**              | Lorsque vous sauvegardez à Windows Azure blob storage, SQL Server 2016 prend en charge la sauvegarde sur plusieurs objets BLOB pour activer la sauvegarde des bases de données, jusqu'à 12,8 To maximum.      |
| **Sauvegarde instantanée**                | Grâce à l’utilisation des instantanés Azure, sauvegarde instantanée de fichier SQL Server fournit des sauvegardes quasi instantanées et restaure rapide pour les fichiers de base de données stockées à l’aide du service de stockage Blob Azure. Cette fonctionnalité permet de simplifier la sauvegarde et restauration des stratégies. Instantané de fichiers sauvegarde prend également en charge le point de restauration dans le temps. Pour plus d’informations, voir [Les sauvegardes d’instantanés pour les fichiers de base de données dans Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Gérées planification des sauvegardes**            | Sauvegarde SQL Server gérés à Azure prend désormais en charge les plannings personnalisés. Pour plus d’informations, voir [Sauvegarde SQL Server gérés à Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Pour voir un didacticiel des fonctionnalités de SQL Server 2016 lors de l’utilisation de stockage d’objets Blob Azure, [didacticiel : au moyen du service de stockage de Microsoft Azure Blob les bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 inclut les améliorations suivantes :

1. **Sauvegarder et restaurer sur Azure**:

 - *Sauvegarde SQL Server à URL* prend désormais en charge dans SQL Server Management Studio. L’option sauvegarder sur Azure est désormais disponible lors de l’utilisation de la tâche de sauvegarde ou de restauration, ou Assistant plan de maintenance dans SQL Server Management Studio. Pour plus d’informations, voir [URL la sauvegarde SQL Server](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *Sauvegarde SQL Server gérés à Azure* a une nouvelle fonctionnalité qui permet de gestion des sauvegardes automatisée. Il s’agit particulièrement utile pour automatiser la gestion de la sauvegarde des instances de SQL Server 2014 s’exécutant sur un ordinateur Azure. Pour plus d’informations, voir [Sauvegarde SQL Server gérés à Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Sauvegarde automatique* fournit une automatisation supplémentaire pour activer automatiquement *Sauvegarde SQL Server gérés à Azure* sur toutes les bases de données existants et nouveaux pour un serveur SQL Azure ordinateur virtuel. Pour plus d’informations, voir [Sauvegarde automatique pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).
 - Pour une vue d’ensemble de toutes les options pour la sauvegarde 2014 Azure SQL Server, voir [SQL Server sauvegarde et restauration avec le Service de stockage Blob Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Chiffrement**: SQL Server 2014 prend en charge le chiffrement des données lors de la création d’une sauvegarde. Il prend en charge plusieurs algorithmes de chiffrement et l’utilisation osf un certificat ou clé asymétrique. Pour plus d’informations, consultez [Chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Pour plus d’informations sur SQL Server sauvegarde et restauration de SQL Server 2012, voir [sauvegarde et restauration de SQL Server bases de données (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

À partir de SQL Server 2012 SP1 cumulé mise à jour 2, vous pouvez sauvegarder et restaurer à partir du service de stockage d’objets Blob Azure. Cette amélioration peut être utilisée pour sauvegarder des bases de données SQL Server sur un serveur SQL Server s’exécutant sur une Machine virtuelle Azure ou une instance locale. Pour plus d’informations, voir [SQL Server sauvegarde et restauration avec le Service de stockage Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Certains des avantages de l’utilisation du service de stockage Blob Azure incluent la possibilité d’ignorer la limite de 16 disque pour disques connectés, facilité de gestion de la disponibilité du fichier de sauvegarde à une autre instance d’instance de SQL Server s’exécutant sur une machine virtuelle Azure ou une instance locale de migration ou de reprise après sinistre directe. Pour une liste complète des avantages offerts par à l’aide d’un service de stockage blob Azure des sauvegardes SQL Server, voir la section *avantages* dans [SQL Server sauvegarde et restauration avec le Service de stockage Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Pour des recommandations de meilleures pratiques et les informations de dépannage, voir [sauvegarder et restaurer les meilleures pratiques (Azure Blob Storage Service)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Pour SQL Server sauvegarde et restauration dans SQL Server 2008 R2, voir [sauvegarde et restauration des bases de données SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Pour SQL Server sauvegarde et restauration de SQL Server 2008, voir [sauvegarde et restauration des bases de données SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Étapes suivantes

Si vous envisagez de votre déploiement de SQL Server dans une machine virtuelle Azure, vous pouvez trouver des conseils de mise à disponibilité dans le didacticiel suivant : [mise en service d’un serveur SQL Machine virtuelle sur Azure avec le Gestionnaire de ressources Azure](virtual-machines-windows-portal-sql-server-provision.md).

Sauvegarde et restauration peuvent être utilisées pour migrer vos données, mais il existe potentiellement plus faciles chemins de migration de données vers SQL Server sur un ordinateur virtuel Azure. Pour une description détaillée des options de migration et de recommandations, voir [migrer une base de données SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md).

Passez en revue d’autres [ressources pour l’exécution de SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
