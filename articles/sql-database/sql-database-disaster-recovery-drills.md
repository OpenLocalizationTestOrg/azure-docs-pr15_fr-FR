<properties 
   pageTitle="Exercices de récupération d’urgence de base de données SQL | Microsoft Azure" 
   description="Découvrez des conseils et des pratiques recommandées pour l’utilisation de la base de données SQL Azure pour effectuer des exercices de récupération d’urgence qui aideront à conservent votre mission critique applications d’entreprise résistants d’échecs et des interruptions." 
   services="sql-database" 
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>Exécution d’exploration de récupération d’urgence

Il est recommandé d’effectue régulièrement de validation de compatibilité avec les applications de flux de travail de récupération. Vérification du comportement de l’application et les implications de perte de données et/ou l’interruption implique que le basculement est une bonne pratique d’ingénierie. Il est également une obligation par la plupart des normes industrielles dans le cadre de certification continuité business.

Effectuer une exploration de récupération d’urgence comprend :

- Simulation panne du niveau de données
- Récupération 
- Valider la restauration de billet l’intégrité des applications

Comment les flux de travail à exécuter dans la hiérarchie vous [conçu votre application pour la continuité](sql-database-business-continuity.md), peut varier selon. Ci-dessous, nous décrivent les meilleures pratiques conduite d’une exploration de récupération d’urgence dans le contexte de base de données SQL Azure. 

##<a name="geo-restore"></a>Geo-restaurer

Pour éviter la perte de données potentielle lorsque vous effectuez une exploration de récupération d’urgence, nous vous recommandons d’effectuer à l’aide d’un environnement de test en créant une copie de l’environnement de production et son utilisation pour vérifier le flux de travail de basculement de l’application dans la hiérarchie.
 
####<a name="outage-simulation"></a>Simulation de panne

Pour simuler l’interruption, vous pouvez supprimer ou renommer la base de données source. Cela va entraîner l’échec de connectivité l’application. 

####<a name="recovery"></a>Récupération

- Effectuez la Geo-restauration de la base de données dans un autre serveur comme indiqué [ici](sql-database-disaster-recovery.md). 
- Modifier la configuration d’application pour se connecter à la base de données récupéré, puis suivez le guide de [configurer une base de données après récupération](sql-database-disaster-recovery.md) pour achever la récupération.

####<a name="validation"></a>Validation

- Terminer dans la hiérarchie en vérifiant la récupération de billet intégrité application (c'est-à-dire les chaînes de connexion, connexions, tester les fonctionnalités de base ou autre composant validations standard approbations de procédures d’application).

##<a name="geo-replication"></a>Geo-réplication

Pour une base de données qui est protégé à l’aide de la réplication Geo l’exercice exploration impliquera basculement planifié vers la base de données secondaire. Le basculement planifié garantit que la langue principale et les bases de données secondaires soient synchronisés lorsque les rôles sont activées. Contrairement au basculement non planifié, cette opération n’implique pas de perte de données, afin de la hiérarchie peut être effectuée dans l’environnement de production. 

####<a name="outage-simulation"></a>Simulation de panne

Pour simuler l’interruption, vous pouvez désactiver l’application web ou virtuel connecté à la base de données. Ainsi, les problèmes de connectivité pour les clients web.

####<a name="recovery"></a>Récupération

- Vérifiez que la configuration d’application dans la région DR pointe vers l’ancien secondaire devenant entièrement accessible nouvelle primaire. 
- Effectuer [planifiée basculement](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) pour rendre la base de données secondaire une nouvelle primaire
- Suivez le guide de [configurer une base de données après récupération](sql-database-disaster-recovery.md) pour achever la récupération.

####<a name="validation"></a>Validation

- Terminer dans la hiérarchie en vérifiant la récupération de billet intégrité application (c'est-à-dire les chaînes de connexion, connexions, tester les fonctionnalités de base ou autre composant validations standard approbations de procédures d’application).


## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les scénarios continuité d’entreprise, voir [scénarios continuité](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
