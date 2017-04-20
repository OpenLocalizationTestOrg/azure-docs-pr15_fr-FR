<properties
    pageTitle="Comment utiliser le stockage Azure pour la sauvegarde de SQL Server et restaurer | Microsoft Azure"
    description="Découvrez comment faire pour sauvegarder SQL Server pour le stockage Azure. Décrit les avantages de la sauvegarde des bases de données SQL pour le stockage Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilisez le stockage Azure pour SQL Server sauvegarde et restauration

## <a name="overview"></a>Vue d’ensemble

À partir de SQL Server 2012 SP1 CU2, vous pouvez maintenant écrire des sauvegardes SQL Server directement au service de stockage Blob Azure. Vous pouvez utiliser cette fonctionnalité pour sauvegarder et restaurer à partir du service d’objets Blob Azure avec une base de données SQL Server locale ou une base de données SQL Server sur une machine virtuelle Azure. Sauvegarde vers le cloud offre les avantages de disponibilité, illimité hors stockage répliquée geo et facilité de migration de données vers ou à partir du cloud. Vous pouvez émettre sauvegarder ou restaurer des instructions à l’aide de Transact-SQL ou SMO.

SQL Server 2016 présente les nouvelles fonctionnalités ; Vous pouvez utiliser [un instantané du fichier de sauvegarde](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées, restaure extrêmement rapides.

Cette rubrique explique pourquoi vous pouvez choisir d’utiliser le stockage Azure des sauvegardes SQL et puis décrit les composants impliqués. Vous pouvez utiliser les ressources fournies à la fin de cet article pour accéder aux procédures pas à pas et des informations supplémentaires pour commencer à utiliser ce service avec vos sauvegardes SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Avantages de l’utilisation du Service Blob Azure pour sauvegardes SQL Server

Il existe plusieurs problèmes rencontrés par les vous lorsque vous sauvegardez SQL Server. Ces défis incluent la gestion du stockage, risque d’échec de l’espace de stockage, accès au stockage hors site et la configuration matérielle. La plupart de ces défis sont adressées à l’aide du service banques Blob Azure des sauvegardes SQL Server. Prenez en compte les avantages suivants :

- **Facilité d’utilisation**: stockage de vos sauvegardes dans des objets BLOB Azure peut être une pratique, flexible et faciliter l’accès hors option. Création hors site espace de stockage pour vos sauvegardes SQL Server peut être aussi simple que la modification de votre scripts/tâches existantes à utiliser la syntaxe de la **Sauvegarde à l’URL** . Stockage hors site doit être généralement suffisamment à partir de l’emplacement de base de données de production pour empêcher un incident unique qui peut-être avoir un impact sur les emplacements de base de données hors site et de production. En choisissant de [geo répliquer votre Azure BLOB](../storage/storage-redundancy.md), vous avez un niveau supplémentaire de protection en cas de sinistre pouvant entraîner la région entière.
- **Archive de sauvegarde**: le service du stockage d’objets Blob Azure offre une meilleure alternative à l’option bandes souvent utilisés pour archiver des sauvegardes. Stockage sur bande peut nécessiter de transport physique à une installation hors site et les mesures pour protéger les éléments multimédias. Stockage de vos sauvegardes dans le stockage Blob Azure fournit un instant, hautement disponible, et un durables d’archivage option.
- **Matériel géré**: il n’est pas de surcharge de gestion du matériel avec les services Azure. Services Azure Gestion du matériel et fournissent geo réplication pour redondance et la protection contre les défaillances matérielles.
- **Stockage illimité**: en activant une sauvegarde directe sur des objets BLOB Azure, vous avez accès au stockage quasi illimitée. Par ailleurs, la sauvegarde sur un disque Azure machine virtuelle a limites de taille de l’ordinateur. Il existe une limite au nombre de disques que vous pouvez joindre à une machine virtuelle Azure des sauvegardes. Cette limite est 16 disques d’une instance de très grande et moins instances plus petit.
- **Disponibilité de sauvegarde**: sauvegardes stockées dans des objets BLOB Azure sont disponibles en tout lieu et à tout moment et facilement accessibles pour restaure à un serveur SQL local ou un autre SQL Server en cours d’exécution dans une Machine de virtuelle Azure, sans qu’il soit nécessaire pour attacher/détacher de base de données ou de télécharger et joindre le disque dur virtuel.
- **Coût**: payer uniquement pour le service qui est utilisé. Peut être plus rentable en tant qu’archive hors site et sauvegarde option. Voir la [Calculatrice tarification Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculatrice de tarification")et [l’article Azure tarifs](http://go.microsoft.com/fwlink/?LinkId=277059 "tarifs article") pour plus d’informations.
- **Instantanés de stockage**: lorsque les fichiers de base de données sont stockées dans un blob Azure et à l’aide de SQL Server 2016, vous pouvez utiliser [un instantané du fichier de sauvegarde](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées, restaure extrêmement rapides.

Pour plus d’informations, voir [SQL Server sauvegarde et restauration avec le Service de stockage Blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Les deux sections suivantes présentent le service de stockage Blob Azure, y compris les composants de SQL Server requis. Il est important de comprendre les composants et leur interaction avec succès utiliser la sauvegarde et de restauration à partir du service de stockage Blob Azure.

## <a name="azure-blob-storage-service-components"></a>Composants de Service de stockage Blob Azure

Les composants Azure suivants sont utilisés lorsque vous sauvegardez au service de stockage Blob Azure.

| Composant               | Description                          |
|---------------------|-------------------------------|
| **Compte de stockage** | Le compte de stockage est le point de départ pour tous les services de stockage. Pour accéder à un service de stockage d’objets Blob Azure, tout d’abord créer un compte de stockage Azure. Pour plus d’informations sur le service de stockage Blob Azure, Découvrez [comment utiliser le Service de stockage d’objets Blob Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Conteneur** | Un conteneur fournit un regroupement d’un ensemble d’objets BLOB et peut stocker un nombre illimité d’objets BLOB. Pour écrire un serveur SQL Server sauvegarde sur un service d’objets Blob Azure, vous devez disposer au moins le conteneur racine créé. |
| **Objets BLOB** | Un fichier de n’importe quel type et la taille. Objets BLOB sont dédié au format URL suivant : **https://[storage account].blob.core.windows.net/[container]/[blob]**. Pour plus d’informations sur les objets BLOB de page, voir [présentation bloc et des objets BLOB de la Page](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Composants de SQL Server

Les composants de SQL Server suivants sont utilisés lorsque vous sauvegardez au service de stockage Blob Azure.

| Composant               | Description                          |
|---------------------|-------------------------------|
| **URL** | Une URL spécifie un identificateur URI (Uniform Resource) vers un fichier de sauvegarde unique. L’URL est utilisée pour indiquer l’emplacement et le nom du fichier de sauvegarde SQL Server. L’URL doit pointer vers un blob réel, pas seulement un conteneur. Si le blob n’existe pas, il est créé. Si un blob existant n’est spécifié, sauvegarde échoue, à moins que la > option WITH FORMAT est spécifiée. Voici un exemple de l’URL que vous spécifiez dans la commande BACKUP : **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS est recommandée mais pas obligatoire. |
| **Informations d’identification** | Les informations requises pour se connecter et s’authentifier au service de stockage Blob Azure apparaît sous informations d’identification.  Dans l’ordre pour SQL Server écrire des sauvegardes sur un Blob Azure ou les restaurer à partir de celle-ci, les informations d’identification SQL Server doivent être créée. Pour plus d’informations, voir [Les informations d’identification SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Si vous choisissez de copier et télécharger un fichier de sauvegarde au service de stockage Blob Azure, vous devez utiliser un type de blob page comme option de stockage si vous envisagez d’utiliser ce fichier pour les opérations de restauration. RESTAURER à partir d’un type de blob bloc échoue avec une erreur.

## <a name="next-steps"></a>Étapes suivantes

1. Créez un compte Azure si vous n’en avez pas déjà. Si vous évaluez Azure, pensez à la [version d’évaluation gratuite](https://azure.microsoft.com/free/).

1. Suivez ensuite une des didacticiels suivants qui vous guide dans la création d’un compte de stockage et de restauration.

    - **SQL Server 2014**: [didacticiel : SQL Server 2014 sauvegarde et restauration à Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [didacticiel : au moyen du service de stockage de Microsoft Azure Blob les bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Passez en revue la documentation supplémentaire commençant par [SQL Server sauvegarde et restauration avec le Service de stockage Blob Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Si vous rencontrez des problèmes, consultez la rubrique [Sauvegarde SQL Server à URL meilleures pratiques et résolution des problèmes](https://msdn.microsoft.com/library/jj919149.aspx).

Pour d’autres SQL Server sauvegarder et restaurer les options, voir [sauvegarde et restauration pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
