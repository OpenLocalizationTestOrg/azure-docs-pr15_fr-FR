<properties
    pageTitle="Vue d’ensemble de SQL Server sur des Machines virtuelles Azure | Microsoft Azure"
    description="Découvrez comment exécuter complètes éditions de SQL Server sur machines virtuelles Azure. Obtenir un lien direct avec toutes les images de machine virtuelle SQL Server et le contenu associé."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Vue d’ensemble de SQL Server sur des Machines virtuelles Azure

Cette rubrique décrit les options disponibles pour exécuter SQL Server sur Azure machines virtuelles (machines virtuelles), ainsi que [des liens vers des images portails](#option-1-create-a-sql-vm-with-per-minute-licensing) et une vue d’ensemble des [tâches courantes](#manage-your-sql-vm).

>[AZURE.NOTE] Si vous connaissez déjà SQL Server et que vous voulez simplement afficher comment déployer une machine virtuelle de SQL Server, voir [mise en service une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Vue d’ensemble
Si vous êtes un administrateur de base de données ou un développeur, Azure machines virtuelles permettent de déplacer les charges de travail de SQL Server en local et les applications dans le Cloud. La vidéo suivante fournit une vue d’ensemble technique de SQL Server Azure machines virtuelles.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

La vidéo traite des sujets suivants :

|Heure|Zone|
|---|---|
| 00:21 | Que sont les machines virtuelles Azure ? |
| 01:45 | Sécurité |
| 02:50 | Connectivité |
| 03:30 | Performances et la fiabilité de stockage |
| 05:20 | Taille de la mémoire virtuelle |
| 05:54 | Disponibilité et SLA |
| 07:30 | Prise en charge de la configuration |
| 08:00 | Surveillance des mots clés |
| 08:32 | Démo : Créer une machine virtuelle de SQL Server 2016 |

>[AZURE.NOTE] La vidéo se concentre sur SQL Server 2016, mais Azure fournit des images de machine virtuelle depuis plusieurs versions de SQL Server, y compris 2008, 2012, 2014 et 2016. 

## <a name="scenarios"></a>Scénarios
Il existe de nombreuses raisons que vous pouvez choisir d’héberger vos données dans Azure. Si votre application se déplace vers Azure, il améliore les performances pour déplacer des données. Mais il existe d’autres avantages. Vous avez automatiquement accès à plusieurs centres de données pour une récupération de présence et de sinistre globale. Les données sont également hautement sécurisée et résistant.

SQL Server s’exécutant sur machines virtuelles Azure est une option pour le stockage de vos données relationnelles dans Azure. Il est judicieux de plusieurs scénarios. Par exemple, vous souhaiterez peut-être configurer la machine virtuelle Azure de même que possible pour une machine de SQL Server locale. Ou vous souhaiterez peut-être exécuter des applications et services supplémentaires sur le même serveur de base de données. Il existe deux principales ressources qui peuvent vous aider à penser davantage de scénarios et considérations relatives à :

 - [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fournit une vue d’ensemble des scénarios meilleures pour l’utilisation de SQL Server sur machines virtuelles Azure. 
 - [Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) fournit une comparaison détaillée entre base de données SQL et SQL Server sur un ordinateur virtuel.

## <a name="create-a-new-sql-vm"></a>Créer un nouveau VM SQL
Les sections suivantes fournissent des liens directs vers le portail Azure pour les images de la galerie de machine virtuelle SQL Server. En fonction de l’image sélectionnée, vous pouvez soit paie pour SQL Server les coûts sur une base par minute des licences, ou vous pouvez transférer votre propre licence (BYOL).

Vous trouverez des instructions détaillées pour ce processus dans le didacticiel, [mise en service une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). En outre, passez en revue [les procédures recommandées pour les machines virtuelles SQL Server](virtual-machines-windows-sql-performance.md), qui explique comment sélectionner la taille de l’ordinateur approprié et d’autres fonctionnalités disponibles lors de la mise en service.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Option 1 : Créer une VM SQL avec la licence par minute
Le tableau suivant fournit une matrice des images de SQL Server disponibles dans la galerie de machine virtuelle. Cliquez sur un lien pour commencer à créer une VM SQL nouveau système d’exploitation, Édition et version spécifiée.

|Version|Système d'exploitation|Édition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [développement](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Option 2 : Créer une VM SQL avec une licence existante
Vous pouvez également mettre votre propre licence (BYOL). Dans ce scénario, vous payez uniquement pour la machine virtuelle sans les frais supplémentaires pour les licences de SQL Server. Pour utiliser votre propre licence, utilisez la matrice des versions SQL Server, les éditions et les systèmes d’exploitation ci-dessous. Dans le portail, ces noms image sont précédés **{BYOL}**.

|Version|Système d'exploitation|Édition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Entreprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Entreprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Entreprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Pour utiliser des images BYOL VM, vous devez avoir et accord entreprise mobilité [licence via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Vous devez également une licence valide pour l’édition/version de SQL Server à utiliser. Vous devez [fournir les informations nécessaires BYOL à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) depuis **10** jours de mise en service de votre ordinateur virtuel.

## <a name="manage-your-sql-vm"></a>Gérer votre machine virtuelle SQL
Après la configuration de votre machine virtuelle de SQL Server, il existe plusieurs tâches de gestion facultatif. Dans de nombreux aspects, vous configurer et gérer SQL Server exactement comme vous le feriez gérer une instance de SQL Server locale. Toutefois, certaines tâches sont spécifiques à Azure. Les sections suivantes illustrent certaines de ces zones avec des liens vers des informations supplémentaires.

### <a name="connect-to-the-vm"></a>Se connecter à la machine virtuelle
Parmi les étapes de gestion plus simples consiste à se connecter à votre ordinateur de virtuel SQL Server grâce à des outils, tels que SQL Server Management Studio (SSMS). Pour obtenir des instructions sur la connexion à votre nouvelle machine virtuelle SQL Server, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrer vos données
Si vous avez une base de données existante, vous souhaiterez déplacer jusqu'à la VM SQL nouvellement mis en service. Pour une liste des options de migration et consulter des instructions, voir [migrer une base de données SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurer la haute disponibilité
Si vous avez besoin de disponibilité, envisagez de configurer des groupes de disponibilité de SQL Server. Cela implique plusieurs Azure machines virtuelles dans un réseau virtuel. Le portail Azure comporte un modèle qui définit cette configuration pour vous. Pour plus d’informations, voir [configurer un groupe de disponibilité AlwaysOn dans le Gestionnaire de ressources Azure des machines virtuelles](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si vous voulez configurer manuellement votre groupe de disponibilité et d’écoute associée, voir [Configurer des groupes de disponibilité AlwaysOn dans Azure machine virtuelle](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Pour d’autres considérations relatives à la disponibilité, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Sauvegarder vos données
Machines virtuelles Azure peuvent bénéficier de [Sauvegarde automatique](virtual-machines-windows-sql-automated-backup.md), ce qui crée régulièrement des sauvegardes de votre base de données vers le stockage blob. Vous pouvez utiliser également manuellement cette technique. Pour plus d’informations, voir [Utiliser le stockage Azure pour SQL Server sauvegarde et restauration](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Pour une vue d’ensemble de toutes les options de sauvegarde et de restauration, voir [sauvegarde et restauration pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatiser les mises à jour
Machines virtuelles Azure permet [Correction automatique](virtual-machines-windows-sql-automated-patching.md) pour planifier une période de maintenance pour l’installation de windows importantes et SQL Server met à jour automatiquement.

### <a name="customer-experience-improvement-program-ceip"></a>Programme d’amélioration de l’expérience utilisateur (CEIP)
Le programme (amélioration) est activée par défaut. Rapports régulièrement envoie à Microsoft pour aider à améliorer SQL Server. Il n’y a aucune tâche de gestion requise avec CEIP, sauf si vous voulez désactiver après la mise en service. Vous pouvez personnaliser ou désactiver le CEIP en vous connectant à la machine virtuelle avec Bureau à distance. Puis exécutez l’utilitaire **d’erreur de SQL Server et des rapports d’utilisation** . Suivez les instructions pour désactiver le rapport. 

Pour plus d’informations, consultez la section CEIP de la rubrique [d’Accepter les termes du contrat de licence](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Étapes suivantes
[Explorer les rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.

Autres questions ? Tout d’abord, voir le [SQL Server sur le Forum aux questions sur Azure Machines virtuelles](virtual-machines-windows-sql-server-iaas-faq.md). Mais également d’ajouter vos questions ou commentaires au bas de tous les sujets SQL VM pour interagir avec Microsoft et de la Communauté.
