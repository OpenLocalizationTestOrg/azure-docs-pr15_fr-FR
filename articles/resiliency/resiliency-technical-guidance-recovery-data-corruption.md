<properties
   pageTitle="Conseils techniques résilience de récupération à partir de la corruption des données ou de suppression accidentelle | Microsoft Azure"
   description="Article à comprendre comment récupérer des données corrompues de données ou suppression accidentelle des données à et conception d’applications à tolérance de panne résistant, hautement disponible, ainsi que de planification de sinistre"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Conseils techniques résilience Azure : récupération d’altération des données ou d’une suppression accidentelle

Partie d’un plan continuité de service robuste rencontre une offre si vos données obtient endommagées ou supprimées par mégarde. Voici les informations sur la récupération après que données a été corrompues ou accidentellement supprimées, en raison des erreurs d’application ou l’erreur de l’opérateur.

##<a name="virtual-machines"></a>Machines virtuelles

Pour protéger vos Machines virtuelles Azure (parfois appelée machines virtuelles infrastructure en tant que service) à partir d’erreurs d’application ou suppression accidentelle, utilisez la [Sauvegarde Azure](https://azure.microsoft.com/services/backup/). Sauvegarde Azure permet de créer des sauvegardes sont cohérentes entre plusieurs disques machine virtuelle. En outre, l’archivage sécurisé sauvegarde peuvent être répliquée sur des régions pour assurer la récupération contre la perte de région.

##<a name="storage"></a>Espace de stockage

Notez que lorsque le stockage Azure fournit la résilience des données par le biais réplicas automatisés, cela n’empêche pas votre code de l’application (ou les développeurs/utilisateurs) à partir de la corruption des données via la suppression accidentelle ou involontaire, mise à jour et ainsi de suite. La conservation de fidélité de données face à des erreurs utilisateur ou application nécessite des techniques plus avancées, telles que la copie des données dans un emplacement de stockage secondaire avec un journal d’audit. Les développeurs peuvent tirer parti des objets blob [fonctionnalité instantané](https://msdn.microsoft.com/library/azure/ee691971.aspx)qui peut créer des instantanés de point dans le temps en lecture seule des matières blob. Cela peut servir comme base d’une solution fidélité de données pour les objets BLOB Azure stockage.

###<a name="blob-and-table-storage-backup"></a>Objets BLOB et sauvegarde de stockage de Table

Tandis que les tables et des objets BLOB sont très longue durées, ils représentent toujours l’état actuel des données. Récupération d’indésirable modification ou suppression de données peut-être nécessiter la restauration des données à un état précédent. Pour cela, en tirant parti des fonctionnalités fournies par Azure pour stocker et conserver des copies de point dans le temps.

Pour les objets BLOB Azure, vous pouvez effectuer des sauvegardes point-à-temps à l’aide de la [fonctionnalité de capture instantanée blob](https://msdn.microsoft.com/library/ee691971.aspx). Pour chaque instantané, vous êtes chargé uniquement pour le stockage requis pour stocker les différences au sein de l’objet blob depuis le dernier instantané d’état. Les instantanés étant dépendants de l’existence du blob d’origine qu’ils sont basés sur, une opération de copie à un autre blob ou même un autre compte de stockage est recommandée. Cela garantit que les données de sauvegarde sont correctement protégées contre la suppression accidentelle. Pour les Tables Azure, vous pouvez créer des copies de point dans le temps pour une autre table ou pour des objets BLOB Azure. Plus d’instructions et exemples effectuer des sauvegardes au niveau de l’application des tables et des objets BLOB sont accessibles ici :

  * [Protection de vos Tables contre les erreurs d’Application](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Protection de vos objets BLOB contre les erreurs d’Application](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Base de données

Plusieurs options de [continuité des activités](../sql-database/sql-database-business-continuity.md) (sauvegarder, restaurer) sont disponibles pour la base de données SQL Azure. Bases de données peuvent être copiés à l’aide de la fonctionnalité de [Copie de la base de données](../sql-database/sql-database-copy.md) , ou en [l’exportation](../sql-database/sql-database-export.md) et [l’importation](https://msdn.microsoft.com/library/hh710052.aspx) d’un fichier à dos de SQL Server. Copie de la base de données fournit les résultats cohérentes, mais pas à un DOS (via le service d’importation/exportation). Ces deux options fonctionnent comme des services basée sur la file d’attente au sein du centre de données, et elles ne fournissent pas actuellement un SLA délais d’exécution.

>[AZURE.NOTE]Les options de copie et importation/exportation de base de données placez un degré significatif de la charge sur la base de données source. Ils peuvent engendrer des conflits de ressources ou la limitation des événements.

###<a name="sql-database-backup"></a>Sauvegarde de base de données SQL

Point-à-temps des sauvegardes de base de données SQL Microsoft Azure sont obtenus en [copiant votre base de données SQL Azure](../sql-database/sql-database-copy.md). Vous pouvez utiliser cette commande pour créer une copie cohérente d’une base de données du même serveur de base de données logique ou vers un autre serveur. Dans les deux cas, la copie de la base de données est entièrement fonctionnelle et indépendamment de la base de données source. Chaque copie que vous créez représente une option de récupération de point dans le temps. Vous pouvez récupérer l’état de base de données entièrement en renommant la nouvelle base de données portant le nom de la base de données source. Par ailleurs, vous pouvez récupérer un sous-ensemble spécifique des données à partir de la nouvelle base de données à l’aide de requêtes Transact-SQL. Pour plus d’informations sur la base de données SQL, voir [vue d’ensemble de la continuité avec la base de données SQL Azure](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server sur Machines virtuelles sauvegarde

Pour SQL Server utilisés avec Azure infrastructure comme un machines virtuelles service (souvent appelés IaaS ou machines virtuelles IaaS), il existe deux possibilités : des sauvegardes traditionnels et envoi des journaux. Utilisation de sauvegardes traditionnels vous permet de restaurer à un point spécifique dans le temps, mais le processus de récupération est lent. Restauration de sauvegardes traditionnels nécessite commençant par une sauvegarde initiale complète, puis en appliquant les sauvegardes effectuées par la suite. La deuxième option consiste à configurer une session pour retarder la restauration des sauvegardes du journal (par exemple, en deux heures) d’envoi de journaux. Cela fournit une fenêtre pour récupérer les erreurs apportées sur le serveur principal.

##<a name="other-azure-platform-services"></a>D’autres services de plateforme Azure

Certains services plateforme Azure stockent des informations dans un compte de stockage contrôlée par l’utilisateur ou de la base de données SQL Azure. Si la ressource compte ou stockage est supprimée ou endommagée, cela peut provoquer des erreurs graves avec le service. Dans ce cas, il est important de tenir à jour des sauvegardes qui vous permet de recréer ces ressources s’ils ont été supprimés ou endommagés.

Pour les Sites Web Azure et Azure Mobile Services, vous devez sauvegarder et mettre à jour des bases de données associées. Pour Azure Media Services et Machines virtuelles, vous devez mettre à jour le compte de stockage Azure associé et toutes les ressources dans ce compte. Par exemple, pour les Machines virtuelles, vous devez sauvegarder et gérer les disques machine virtuelle dans le stockage blob Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Aide-mémoire pour l’altération des données ou de suppression accidentelle

##<a name="virtual-machines-checklist"></a>Machines virtuelles aide-mémoire

  1. Passez en revue la section Machines virtuelles de ce document.
  2. Sauvegarder et mettre à jour les disques machine virtuelle avec Azure sauvegarde (ou votre propre système de sauvegarde à l’aide de stockage d’objets blob Azure et des instantanés de disque dur virtuel).

##<a name="storage-checklist"></a>Liste de vérification de stockage

  1. Passez en revue la section de stockage de ce document.
  2. Sauvegardez régulièrement les ressources de stockage critique.
  3. Envisagez d’utiliser la fonctionnalité instantané pour les objets BLOB.

##<a name="database-checklist"></a>Liste de vérification de base de données

  1. Passez en revue la section de base de données de ce document.
  2. Créer des sauvegardes point-à-temps à l’aide de la commande Copier de base de données.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server dans la liste de vérification sauvegarde Machines virtuelles

  1. Passez en revue le SQL Server dans la section sauvegarde Machines virtuelles de ce document.
  2. Utilisez traditionnel sauvegarde et restauration techniques.
  3. Créer un journal différé livraison session.

##<a name="web-apps-checklist"></a>Liste des applications Web

  1. Sauvegarder et mettre à jour de la base de données associé, le cas échéant.

##<a name="media-services-checklist"></a>Liste de contrôle Media Services

  1. Sauvegarder et mettre à jour les ressources de stockage associé.

##<a name="more-information"></a>Plus d’informations

Pour plus d’informations sur les fonctionnalités de sauvegarde et restauration d’Azure, voir [stockage, scénarios de sauvegarde et de restauration](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques résilience Azure](./resiliency-technical-guidance.md). Si vous recherchez plus résilience sinistre et ressources de disponibilité, consultez les conseils techniques de Azure résilience [des ressources supplémentaires](./resiliency-technical-guidance.md#additional-resources).