<properties 
   pageTitle="Base de données SQL Azure Forum aux questions" 
   description="Réponses aux clients de questions courantes demander de bases de données cloud et base de données SQL Azure, système de gestion de base de données relationnelle de Microsoft (SGBDR) et base de données en tant que service dans le cloud." 
   services="sql-database" 
   documentationCenter="" 
   authors="CarlRabeler" 
   manager="jhubbard" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Forum aux questions sur base de données SQL

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Comment l’utilisation de la base de données SQL apparaissent-ils pas dans ma facture ? 
Factures de base de données SQL sur un taux horaire prévisible basée sur les deux le niveau de service + niveau de performance pour les bases de données unique ou eDTUs par pool élastique de base de données. L’utilisation réelle est calculée et au prorata toutes les heures, afin que votre facture peut afficher des fractions d’une heure. Par exemple, si une base de données existe pour 12 heures par mois, votre facture montre l’utilisation de 0,5 jours. En outre, niveaux de service + niveau de performance et eDTUs par pool sont répartis dans la facturation afficher le nombre de jours de base de données que vous avez utilisé pour chacune d’elles dans un seul mois plus facilement.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Que se passe-t-il si une seule base de données est active pour moins d’une heure ou utilise un niveau de service supérieur pour moins d’une heure ?
Vous êtes facturé pour chaque heure une base de données existe en utilisant le niveau de service plus élevé + le niveau de performance appliqués au cours de cette heure, indépendamment de l’utilisation ou la base de données était active pour moins d’une heure. Par exemple, si vous créez une seule base de données et supprimez cinq minutes plus tard votre facture reflète les frais d’heure d’une base de données. 

Exemples
    
- Si vous créez une base de données de base et niveau immédiatement à S1 Standard, vous soyez facturée au taux Standard S1 pour la première heure.

- Si vous mettez à niveau une base de données à partir de Basic à Premium à 10:00 et mise à niveau se termine à 1:35. le jour suivant, vous est facturée au taux Premium en commençant à 1:00 

- Si vous mettre à niveau une base de données à partir de Premium de base à 11:00. elle se termine à 2:15 PM, puis la base de données est facturée au taux Premium jusqu'à 3:00 PM, après laquelle il est facturée au taux de base.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Comment l’utilisation de la base de données élastique pool s’affiche sur votre facture et que se passe-t-il lorsque je modifie eDTUs par pool ?
Frais de pool de base de données élastique apparaissent sur votre facture comme DTUs élastique (eDTUs) incréments indiqué sous eDTUs par pool dans [la page tarification](https://azure.microsoft.com/pricing/details/sql-database/). Il est gratuit par base de données pour des pools élastique de base de données. Vous êtes facturé pour chaque heure qu'un pool existe à la plus élevée eDTU, indépendamment de l’utilisation ou le pool était actif pour moins d’une heure. 

Exemples

- Si vous créez un pool de base de données élastique Standard avec 200 eDTUs à 11 h 18, ajout cinq bases de données dans la liste, vous soyez facturées 200 eDTUs pour l’heure entière, début à 11. via le reste de la journée.
- Sur 2 jours, à 5 h 05, base de données 1 commence par d’autres programmes 50 eDTUs et reste stable par le biais du jour. Bases de données 2 à 5 varient entre 0 et 80 eDTUs. Pendant la journée, vous ajoutez cinq autres bases de données qui utilisent des variables eDTUs toute la journée. Jour 2 est une journée entière facturée au eDTU 200. 
- Le jour 3, à 5 matin vous ajoutez un autre 15 bases de données. Utilisation de la base de données augmente toute la journée jusqu'à l’endroit où vous décidez d’augmenter eDTUs pour le pool de 200 à 400 à 8:05 h Frais au niveau de la 200 eDTU ont été en vigueur jusqu'à 20 heures et augmentent à 400 eDTUs pour les quatre heures restantes. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Comment l’utilisation de Geo-réplication Active dans un pool de base de données élastique apparaissent-ils pas dans ma facture ?
Contrairement à des bases de données individuelles, à l’aide de [La réplication Geo Active](sql-database-geo-replication-overview.md) les bases de données élastiques n’est un impact facturation direct.  Vous êtes chargé uniquement pour l’eDTUs mis en service pour chaque pool (pool principal et secondaire pool)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Comment l’utilisation de la fonctionnalité d’audit influe sur votre facture ? 
L’audit intégré au service de base de données SQL sans supplémentaire des coûts et n’est disponible pour les bases de données basique, Standard et Premium. Toutefois, pour stocker les journaux d’audit, l’audit utilise qu'un compte de stockage Azure et les taux pour les tables et les files d’attente dans le stockage Azure appliquent selon la taille de votre journal d’audit.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Comment trouver le niveau de service couche et performances requis pour les bases de données individuelles et des pools élastique de base de données ? 
Il existe plusieurs outils disponibles. 

- Pour les bases de données locale, utilisez le [Conseiller de redimensionnement DTU](http://dtucalculator.azurewebsites.net/) à recommander des bases de données et DTUs obligatoires et évaluer plusieurs bases de données pour des pools élastique de base de données.
- Si une seule base de données serait bénéficient d’un pool, moteur intelligent d’Azure recommande un pool de base de données élastique s’il voit un modèle d’utilisation historiques qui justifie. Voir [moniteur et gérer un pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-manage-portal.md). Pour plus d’informations sur la façon d’effectuer ces opérations mathématiques vous-même, consultez [Considérations prix et des performances pour un pool élastique de base de données](sql-database-elastic-pool-guidance.md)
- Pour déterminer si vous devez composer une seule base de données vers le haut ou vers le bas, voir [conseils pour les performances des bases de données unique](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>À quelle fréquence puis-je modifier le niveau de service niveau ou les performances d’une seule base de données ? 
Les bases de données V12, vous pouvez aussi souvent que vous souhaitez modifier le niveau de service (entre basique, Standard et Premium) ou le niveau de performance au sein d’un niveau de service (par exemple, S1 à S2). Pour les bases de données de version antérieures, vous pouvez modifier le niveau de performances ou de niveau de service un total de quatre fois dans un délai de 24 heures.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>À quelle fréquence puis-je ajuster l’eDTUs par pool ? 
Aussi souvent que vous le souhaitez.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Combien de temps faut-il pour modifier le niveau de service niveau ou les performances d’une base de données unique ou déplacer une base de données et déconnexion d’un pool de base de données élastique ? 
Modifier le niveau de service d’une base de données et déplacement et déconnexion d’un pool requièrent la base de données à copier sur la plateforme comme une opération d’arrière-plan. Modifier le niveau de service peut prendre quelques minutes à plusieurs heures selon la taille des bases de données. Dans les deux cas, les bases de données restent en ligne et disponible lors du déplacement. Pour plus d’informations sur la modification des bases de données individuelles, voir [Modifier le niveau de service d’une base de données](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quand dois-je utiliser une seule base de données et bases de données élastiques ? 
En règle générale, des pools élastique de base de données sont conçus pour un [modèle d’application logiciel en tant que service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md)typique, lorsqu’il existe une base de données par client ou client. Achat de bases de données individuelles et overprovisioning afin de respecter la variable et pic à la demande pour chaque base de données sont souvent pas les coûts efficace. Avec des pools, vous gérez les performances collectives du pool et les bases de données évoluer automatiquement. 

Moteur intelligent d’Azure recommande un pool pour les bases de données quand un modèle d’utilisation justifie. Pour plus d’informations, voir [base de données SQL tarifs recommandations niveau](sql-database-service-tier-advisor.md). Pour obtenir des instructions détaillées sur le choix entre les bases de données unique et souple, consultez [Considérations prix et des performances pour des pools élastique de base de données](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Que signifie être jusqu'à 200 % de votre espace de stockage de base de données généré maximale pour le stockage de sauvegarde ? 
Stockage de sauvegarde est le stockage associé à vos sauvegardes automatisées de base de données qui sont utilisés pour [Point-dans-temps restauration](sql-database-recovery-using-backups.md#-point-in-time-restore) et [Geo restaurer](sql-database-recovery-using-backups.md#geo-restore). Base de données SQL Microsoft Azure fournit jusqu'à 200 % de votre espace de stockage de base de données généré maximale de stockage de sauvegarde sans frais supplémentaires. Par exemple, si vous avez une instance de base de données Standard avec une taille de base de données générée de 250 Go, vous sont fournis avec 500 Go de stockage de sauvegarde sans frais supplémentaires. Si votre base de données dépasse le stockage de sauvegarde fourni, vous pouvez choisir réduire la période de rétention en contactant le Support Azure ou payer pour le stockage supplémentaire sauvegarde facturé au taux standard d’accès en lecture géographiquement stockage redondants (RA GRS). Pour plus d’informations sur la facturation RA GRS, voir détails sur le stockage tarifs.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Je suis passer de/activité sur le Web vers les nouveaux niveaux de service, que dois-je savoir également ?
Les bases de données Web SQL et entreprise Azure sont annulées maintenant. Les niveaux basique, Standard, Premium et élastique remplacent les bases de données Web et entreprise retraite. Nous avons Forum aux questions supplémentaires qui doit vous aider à cette période de transition. [Forum aux questions sur coucher de soleil Web et Business Edition](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Qu’est un décalage de réplication attendu lors de la réplication geo une base de données entre deux régions au sein de la même géographie Azure ?  
Nous sommes actuellement en charge un RPO de cinq secondes et le décalage de réplication a été inférieure à que lorsque le secondaire geo est hébergé dans le Azure recommandé pour la région et au même niveau de service.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Qu’est un décalage de réplication attendus lorsque geo-secondaire est créé dans la même région en tant que la base de données principale ?  
Des données brute, il n’est pas trop de différence entre région à l’intérieur et le décalage de réplication entre région lorsque la Azure recommandé pour région est utilisée. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>S’il existe une défaillance du réseau entre les deux régions, comment la logique de nouvelle tentative fonctionne lorsque Geo réplication est configurée ?  
S’il existe une déconnexion, nous Réessayer toutes les 10 secondes pour rétablir les connexions.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Que puis-je faire pour vous assurer qu’une modification critique sur la base de données principale est répliquée ?
Geo-secondaire est une réplique asynchrone et nous n’essayez pas de le conserver dans parfaitement synchronisé avec le serveur principal. Mais nous fournit une méthode pour forcer la synchronisation pour garantir la réplication des modifications critiques (par exemple, mises à jour de mot de passe). Synchronisation forcée affecte les performances, car il bloque le thread appelant jusqu'à ce que toutes les transactions validées sont répliquées. Pour plus d’informations, voir [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quels outils sont disponibles pour contrôler le décalage de réplication entre la base de données principale et geo-secondaire ?
Nous exposer le décalage de réplication en temps réel entre la base de données principale et geo-secondaire via une vue. Pour plus d’informations, voir [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
