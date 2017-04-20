<properties
   pageTitle="Solutions de reprise - SQL de base de données Active Geo réplication en nuage | Microsoft Azure"
   description="Apprenez à créer des solutions de reprise cloud pour la planification de la continuité entreprise à l’aide de la réplication geo pour la sauvegarde de données d’application avec la base de données SQL Azure."
   keywords="cloud sinistre, solutions de reprise, sauvegarde des données de l’application, geo-réplication, planification continuité de l’entreprise"
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
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>Concevoir une application pour sinistre cloud à l’aide de la réplication Geo Active dans la base de données SQL


> [AZURE.NOTE] [Réplication de Geo Active](sql-database-geo-replication-overview.md) est désormais disponible pour toutes les bases de données de tous les niveaux.



Découvrez comment utiliser [La réplication Geo Active](sql-database-geo-replication-overview.md) dans la base de données SQL aux applications de base de données création résistant échecs régionaux et défaillances grave. Pour entreprise continuité planification, vous pouvez la topologie de déploiement d’application, le contrat de niveau de service vous ciblez, latence du trafic et les coûts. Dans cet article, nous examinons les modèles d’application courantes et parlez des avantages et inconvénients de chaque option. Pour plus d’informations sur Geo-réplication Active avec des Pools élastique, voir [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Modèle de conception 1 : déploiement actif-passif de reprise cloud avec une base de données situé

Cette option est la mieux adaptée aux applications avec les caractéristiques suivantes :

+ Instance active dans une seule région Azure
+ Dépendance fort en lecture / écriture (RW) l’accès aux données
+ La connectivité entre région entre la logique d’application et la base de données n’est pas acceptable en raison de latence et le trafic des coûts    

Dans ce cas, la topologie de déploiement d’application est optimisée pour la gestion des incidents régionaux lorsque tous les composants d’application sont affectés et que vous devez basculement comme une unité. Redondance géographique, la logique d’application et la base de données sont répliquées dans une autre région, mais ils ne sont pas utilisés pour la charge de l’application dans les conditions normales. L’application dans la zone secondaire doit être configurée pour utiliser une chaîne de connexion SQL à la base de données secondaire. Gestionnaire de trafic est configuré pour utiliser [la méthode de routage basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [AZURE.NOTE] [Gestionnaire de trafic Azure](../traffic-manager/traffic-manager-overview.md) est utilisée dans cet article uniquement à des fins d’illustration. Vous pouvez utiliser toute solution équilibrage de charge qui prend en charge la méthode de routage basculement.    

Outre les instances d’application principale, vous devriez envisager de déploiement d’une petite [application de rôle de collaborateur](cloud-services-choose-me.md#tellmecs) qui surveille votre base de données principale à l’aide de périodiques commandes (OFF) T-SQL en lecture seule. Vous pouvez l’utiliser pour déclencher automatiquement le basculement, pour générer une alerte sur la console d’administration de votre application, ou les deux. Pour vous assurer que la surveillance n’est pas concerné par région à l’échelle des coupures, vous devez déployer les instances d’application surveillance à chaque région et possèdent chaque instance connecté à la base de données principale dans la région principale et la base de données secondaire dans la région locale. 

> [AZURE.NOTE] Les deux applications d’analyse doivent être active et sonde bases de données principales et secondaires. Ce dernier peut être utilisé pour détecter une défaillance dans la zone secondaire et l’alerte si l’application n’est pas protégée.     

Le diagramme suivant illustre cette configuration avant d’arrêt.

![Configuration Geo-réplication de base de données SQL. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Après une interruption dans la région principale, l’application de surveillance détecte que la base de données principal n’est pas accessible et enregistre une alerte. Selon votre SLA application, vous pouvez décider combien tests surveillance consécutifs doivent échouer avant qu’elle déclare une panne de base de données. Pour obtenir des coordonnées basculement de la fin de l’application et la base de données, vous devez avoir l’application de surveillance effectuez les opérations suivantes :

1. [Mettre à jour l’état de la fin principale](https://msdn.microsoft.com/library/hh758250.aspx) afin de déclencher le basculement de point de terminaison.
2. Appeler la base de données secondaire pour [déclencher le basculement de la base de données](sql-database-geo-replication-portal.md).

Après le basculement, l’application traite les demandes d’utilisateur dans la zone secondaire mais reste située au même endroit avec la base de données car la base de données principale sera désormais dans la zone secondaire. Ce scénario est illustré par le diagramme suivant. Dans tous les diagrammes, traits pleins indiquent une connexion active, lignes en pointillés indiquent les connexions suspendues et arrêter signes indiquent déclencheurs d’action.


![Réplication-geo : Basculement de l’à une base de données secondaire. Sauvegarde de données d’application.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si une panne s’est-il passé dans la zone secondaire, le lien de réplication entre le serveur principal et la base de données secondaire est suspendu et l’application de surveillance enregistre une alerte que la base de données principale est exposée. Cela n’affecte pas les performances de l’application, mais l’application fonctionne exposée et par conséquent plus exposés en cas de modification à deux régions échouent à la suite.

> [AZURE.NOTE] Nous vous recommandons uniquement des configurations de déploiement avec une seule région DR. C’est parce que la plupart des zones géographiques Azure ont deux régions. Ces configurations ne protège pas votre application à partir d’une panne de deux régions. Dans une éventualité d’un tel échec, vous pouvez récupérer vos bases de données dans une zone troisième de [geo restauration](sql-database-disaster-recovery.md#recovery-using-geo-restore).

Une fois que l’interruption est réduite, la base de données secondaire est automatiquement synchronisé avec le serveur principal. Pendant la synchronisation, les performances du primaire pouvant être affectées légèrement en fonction de la quantité de données qui doivent être synchronisé. Le diagramme suivant illustre une panne dans la zone secondaire.

![Base de données secondaire synchronisée avec principal. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Principaux **avantages** de ce modèle de conception sont :

+ La chaîne de connexion SQL est définie pendant le déploiement des applications dans chaque région et ne change pas après le basculement.
+ Performances de l’application ne sont pas concerné par le basculement que l’application et la base de données se trouvent toujours co-création.

Le principal **inconvénient** est que l’instance d’application redondantes dans la zone secondaire est utilisé uniquement pour la reprise.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Modèle de conception 2 : déploiement actif pour une application l’équilibrage de charge
Cette option de récupération d’urgence cloud est mieux adaptée aux applications avec les caractéristiques suivantes :

+ Rapport élevé de base de données lit écritures
+ Latence d’écriture de base de données n’influe pas sur l’expérience utilisateur  
+ Logique en lecture seule peut être séparée de logique en lecture / écriture à l’aide d’une chaîne de connexion différente
+ Logique en lecture seule ne dépend pas entièrement synchronisées avec les dernières mises à jour des données  

Si vos applications ont ces caractéristiques, équilibrage de charge les connexions de l’utilisateur final sur plusieurs instances de l’application dans différentes régions peut améliorer les performances et l’expérience utilisateur final. Pour implémenter l’équilibrage de charge, chaque région doit avoir une instance active de l’application avec la logique (RW) en lecture / écriture connectée à la base de données principale dans la région principale. La logique (ligne) en lecture seule doit être connectée à une base de données secondaire dans la même région en tant que l’instance d’application. Gestionnaire de trafic doit être configuré pour utiliser [cyclique routage](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md) ou [routage de performances](../traffic-manager/traffic-manager-configure-performance-routing-method.md) avec [point de terminaison de surveillance](../traffic-manager/traffic-manager-monitoring.md) activé pour chaque instance de l’application.

Comme dans motif #1, vous devez envisager de déploiement d’une application de surveillance similaire. Mais contrairement à motif #1, l’application de surveillance ne sera pas chargée de déclencher le basculement de point de terminaison.

> [AZURE.NOTE] Bien que ce modèle utilise plusieurs bases de données secondaire, seule des secondaires serait être utilisée pour le basculement pour les raisons indiqués précédemment. Étant donné que ce modèle nécessite un accès en lecture seule sur le site secondaire, il nécessite la réplication Geo Active.

Gestionnaire de trafic doit être configuré pour des performances routage pour diriger les connexions de l’utilisateur pour l’instance d’application plus proche de l’emplacement géographique de l’utilisateur. Le diagramme suivant illustre cette configuration avant d’arrêt.
![Aucune interruption de service : performances routage vers le plus proche application. Geo-réplication.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si une panne de base de données est détectée dans la région principale, vous lancez basculement de la base de données principale à l’une des régions secondaires, modification de l’emplacement de la base de données principale. Le Gestionnaire de trafic exclut le point de terminaison en mode hors connexion de la table de routage automatiquement, mais continue le trafic de l’utilisateur final de leur routage vers les autres instances en ligne. Étant donné que la base de données principale figure à présent dans une zone différente, toutes les instances online doivent modifier leur chaîne de connexion en lecture / écriture SQL pour vous connecter à la nouvelle primaire. Il est important que vous apportez cette modification avant de lancer le basculement de la base de données. Les chaînes de connexion SQL en lecture seule doivent rester ne change pas qu’ils pointent toujours vers la base de données dans la même région. Les étapes de basculement sont :  

1. Modifier les chaînes de connexion en lecture / écriture SQL pour pointer vers la nouvelle primaire.
2. Appeler la base de données secondaire désignée par ordre à [basculement de début de la base de données](sql-database-geo-replication-portal.md).

Le diagramme suivant illustre la nouvelle configuration après le basculement.
![Configuration après basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En cas de panne d’une des régions secondaires, le Gestionnaire de trafic supprime automatiquement le point de terminaison en mode hors connexion dans cette zone de la table de routage. Le canal de réplication de base de données secondaire dans cette zone est suspendu. Étant donné que les autres zones obtenir le trafic utilisateur supplémentaires dans ce scénario, les performances de l’application sont affectées pendant la coupure. Une fois que l’interruption est réduite, la base de données secondaire dans la région concernée est immédiatement synchronisée avec le serveur principal. Pendant la synchronisation performances du primaire pouvant être affectées légèrement en fonction de la quantité de données qui doivent être synchronisé. Le diagramme suivant illustre une panne d’une des régions secondaires.

![Interruption de service dans la zone secondaire. Reprise - Geo réplication le cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Le principal **avantage** de ce modèle de conception est que vous pouvez répartir la charge de travail d’application sur plusieurs secondaires pour obtenir les performances optimales pour l’utilisateur final. Les **compromis** de cette option sont les suivantes :

+ En lecture / écriture les connexions entre les instances de l’application et la base de données ont variés latence et coût
+ Performances de l’application sont un impact sur l’interruption
+ Instances de l’application sont requises pour modifier la chaîne de connexion SQL de façon dynamique après le basculement de la base de données.  

> [AZURE.NOTE] Une approche similaire peut servir à décharger les charges de travail spécialisées telles que la création de rapports, outils d’analyse décisionnelle ou de sauvegarde. En règle générale ces charges de travail consomment des ressources de base de données significative par conséquent, il est recommandé de désigner une base de données secondaires pour les avec le niveau de performance correspondant à la charge de travail escomptée.

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Modèle de conception 3 : déploiement actif-passif pour la conservation des données  
Cette option est la mieux adaptée aux applications avec les caractéristiques suivantes :

+ Toute perte de données est risque élevé. Le basculement de la base de données peut uniquement être utilisé en dernier recours si la panne est définitive.
+ L’application peut fonctionner en « lecture seule » pour une période donnée.

Dans ce modèle, l’application bascule en mode lecture seule lorsque cela est connecté à la base de données secondaire. La logique d’application dans la région principale se trouve co-création avec la base de données principale et fonctionne en mode lecture seule (RW). La logique d’application dans la zone secondaire co-création trouve au niveau de la base de données secondaire et est prête à fonctionner en mode lecture seule (OFF).  Gestionnaire de trafic doit être configuré pour utiliser le [routage de basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md) avec [point de terminaison de surveillance](../traffic-manager/traffic-manager-monitoring.md) activé pour les deux instances de l’application.

Le diagramme suivant illustre cette configuration avant d’arrêt.
![Déploiement actif-passif avant le basculement. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Lorsque le Gestionnaire de trafic détecte un problème de connectivité à la région principal, il bascule automatiquement le trafic des utilisateurs vers l’instance d’application dans la zone secondaire. Avec ce modèle, il est important que vous **ne pas** déclencher le basculement de base de données après que l’interruption est détectée. L’application dans la zone secondaire est activée et fonctionne en mode lecture seule à l’aide de la base de données secondaire. Ceci est illustré par le diagramme suivant.

![Interruption de service : L’Application en mode lecture seule. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Une fois que l’interruption dans la région principale est réduite, le trafic responsable détecte la restauration de la connectivité dans la région principale et bascule le trafic des utilisateurs vers l’instance d’application dans la région principale. Cette instance d’application reprend et fonctionne en mode lecture / écriture à l’aide de la base de données principale.

> [AZURE.NOTE] Étant donné que ce modèle nécessite un accès en lecture seule sur le site secondaire il nécessite Geo-réplication Active.

En cas de panne dans la zone secondaire, le Gestionnaire de trafic marque le point de terminaison d’application dans la région principale comme étant dégradé et le canal réplication est suspendu. Toutefois, cette interruption de service n’affecte pas les performances de l’application pendant la coupure. Une fois que l’interruption est réduite, la base de données secondaire est immédiatement synchronisée avec le serveur principal. Pendant la synchronisation performances du primaire pouvant être affectées légèrement en fonction de la quantité de données qui doivent être synchronisé.

![Interruption de service : Base de données secondaire. Récupération d’urgence cloud.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Ce modèle de conception présente plusieurs **avantages**:

+ Elle permet d’éviter les pertes de données pendant les interruptions temporaires.
+ Il ne nécessite pas vous permet de déployer une application de surveillance comme la récupération se déclenche par Gestionnaire de trafic.
+ Temps d’arrêt dépend uniquement la rapidité avec laquelle le trafic Gestionnaire détecte l’échec de connexion, qui peut être configuré.

Les **compromis** sont les suivantes :

+ Application doit être en mesure de fonctionner en mode lecture seule.
+ Il est nécessaire pour y dynamiquement lorsqu’il est connecté à une base de données en lecture seule.
+ Reprise des opérations en lecture / écriture nécessite la récupération de la région principale, ce qui signifie que l’accès aux données complet peut être désactivée pour heures, voire des jours.

> [AZURE.NOTE] Dans le cas d’une interruption de service permanent dans la région, vous devez manuellement activez le basculement de la base de données et accepter la perte de données. L’application sera fonctionnelle dans la région secondaire avec accès en lecture / écriture à la base de données.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planification de la continuité entreprise : choisir une conception d’application cloud récupération d’urgence

Votre stratégie de récupération d’urgence cloud spécifiques peut combiner ou étendre ces modèles de conception pour l’adapter aux besoins de votre application.  Comme mentionné précédemment, la stratégie que vous choisissez est basée sur le contrat SLA que vous voulez proposer à vos clients et la topologie de déploiement d’application. Pour vous aider à prendre vos décisions, le tableau suivant compare les choix en fonction de la perte de données estimées ou récupération pointez sur l’objectif et estimée temps de récupération (Insér).

| Motif | RPO | INSÉR
| :--- |:--- | :---
| Déploiement actif-passif de reprise avec access trouve la base de données | L’accès en lecture / écriture < 5 s | Temps de détection de panne API basculement appeler + vérification des applications de test
| Déploiement actif pour une application l’équilibrage de charge | L’accès en lecture / écriture < 5 s | Temps de détection de panne + basculement API appel + chaîne de connexion SQL modifier + vérification des applications de test
| Déploiement actif-passif pour la conservation des données | Accès en lecture seule < 5 s d’accès en lecture / écriture = zéro | Accès en lecture seule = temps de détection de connectivité échec + test de vérification d’application <br>L’accès en lecture / écriture = temps à limiter l’interruption

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)
- Pour plus d’informations sur Geo-réplication Active avec des Pools élastique, voir [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
