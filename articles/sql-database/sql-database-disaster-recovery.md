<properties
   pageTitle="Reprise de base de données SQL | Microsoft Azure"
   description="Découvrez comment récupérer une base de données à partir d’un centre de données régional ou panne avec les fonctions de Geo restaurer et Azure SQL de base de données Active Geo réplication."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurer une base de données SQL Azure ou basculement sur un site secondaire

Base de données SQL Azure propose les fonctionnalités suivantes pour la récupération en cas de panne :

- [Geo-réplication Active](sql-database-geo-replication-overview.md)
- [Geo-restaurer](sql-database-recovery-using-backups.md#point-in-time-restore)

Pour en savoir plus sur des scénarios continuité d’entreprise et les fonctionnalités de prise en charge ces scénarios, voir [continuité des activités](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Préparation de l’événement d’une coupure

Pour réussir avec récupération à une autre zone de données à l’aide de la réplication Geo Active ou sauvegardes geo redondantes, vous devez préparer un serveur dans un autre centre de données une panne de devenir le nouveau serveur principal en cas de besoin se présentent ainsi qu’ont bien définies les étapes présentées et testés pour s’assurer une récupération lisse. Ces étapes de préparation sont les suivantes :

- Identifiez le serveur logique dans une autre zone à devenir le nouveau serveur principal. Avec la réplication-Active Geo, il s’agit au moins une et éventuellement chacun des serveurs secondaires. Pour restaurer-Geo, il s’agit généralement un serveur dans la [région pour](../best-practices-availability-paired-regions.md) la région dans lequel se trouve votre base de données.
- Identifier et vous pouvez également définir, les règles de pare-feu au niveau du serveur nécessaires sur pour les utilisateurs à accéder à la nouvelle base de données principale.
- Déterminer la façon dont vous souhaitez rediriger les utilisateurs vers le nouveau serveur principal, par exemple en modifiant les chaînes de connexion ou en modifiant les entrées DNS.
- Identifier et vous pouvez également créer, les connexions qui doivent être présents dans la base de données maître sur le nouveau serveur principal et vérifiez ces connexions disposer des autorisations appropriées dans la base de données principale, le cas échéant. Pour plus d’informations, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md)
- Identifier les règles d’alerte qui devront être mis à jour pour faire correspondre à la nouvelle base de données principale.
- Document la configuration d’audit sur la base de données principale active
- Effectuer une [reprise d’exploration](sql-database-disaster-recovery-drills.md). Pour simuler une panne pour Geo restaurer, vous pouvez supprimer ou renommer la base de données source pour entraîner l’échec de connectivité l’application. Pour simuler une panne pour Geo-réplication Active, vous pouvez désactiver l’application web ou virtuel connecté à la base de données ou le basculement de la base de données pour entraîner des erreurs de connectity application.

## <a name="when-to-initiate-recovery"></a>Début de restauration

L’opération de récupération a un impact sur l’application. Il nécessite la modification de la chaîne de connexion SQL ou à l’aide de DNS et peut entraîner la perte de données permanentes. Par conséquent, elle doit être effectuée uniquement lorsque la panne est susceptible de durer plus longtemps que les objectifs de temps de votre application. Lorsque l’application est déployée en production vous devez effectuer une analyse régulière de l’état de l’application et utiliser les points de données suivants pour affirmer que la récupération est justifiée :

1.  Échec de la connexion permanente à partir de la couche application à la base de données.
2.  Le portail Azure affiche une alerte concernant un incident dans la région avec un impact large.
3.  Le serveur de base de données SQL Azure est marqué comme étant dégradé.

Selon votre tolérance application responsabilité entreprise temps d’arrêt et possibles, vous pouvez envisager les options suivantes de la récupération.

Utilisez la [Base de données récupérable obtenir](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration répliquées Geo.

## <a name="wait-for-service-recovery"></a>Attendez que la récupération de service

Le travail équipes Azure assidûment pour restaurer la disponibilité des services dans la mesure où rapidement que possible, mais en fonction de la racine il peut prendre heures ou jours.  Si votre application peut tolérer des interruptions importantes, vous pouvez simplement attendre la récupération terminer. Dans ce cas, aucune action de votre part est requise. Vous pouvez voir l’état actuel du service sur notre [Tableau de bord au niveau de Service Azure](https://azure.microsoft.com/status/). La récupération de la zone de disponibilité de votre application est restaurée.

## <a name="failover-to-geo-replicated-secondary-database"></a>Basculer vers la base de données secondaire répliquées geo

Si le temps d’arrêt de votre application peut entraîner la responsabilité entreprise vous devez utiliser les bases de données répliquée geo dans votre application. Il permettra à l’application rapidement restaurer disponibilité dans une zone différente en cas de panne. Découvrez comment [configurer la réplication de Geo](sql-database-geo-replication-portal.md).

Pour restaurer la disponibilité des bases de données, vous devez démarrer le basculement vers le secondaire répliquées geo à l’aide d’une des méthodes prises en charge.

Utilisez une des guides à basculement vers une base de données secondaire répliquée geo suivants :

- [Basculer vers un secondaire répliquées geo à l’aide de portail Azure](sql-database-geo-replication-portal.md)
- [Basculer vers un secondaire répliquées geo à l’aide de PowerShell](sql-database-geo-replication-powershell.md)
- [Basculer vers un secondaire répliquées geo à l’aide de T-SQL](sql-database-geo-replication-transact-sql.md)

## <a name="recover-using-geo-restore"></a>Récupérer à l’aide de Geo de restauration

Si le temps d’arrêt de votre application n’entraîne pas la responsabilité entreprise vous pouvez utiliser Geo restaurer en tant que méthode pour récupérer vos bases de données d’application. Il crée une copie de la base de données à partir de la dernière sauvegarde geo redondants.

Utilisez une des opérations suivantes les repères pour geo-restaurer une base de données dans une nouvelle zone :

- [Geo-restaurer une base de données dans une nouvelle région à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- [Geo-restaurer une base de données dans une nouvelle région à l’aide de PowerShell](sql-database-geo-restore-powershell.md)

## <a name="configure-your-database-after-recovery"></a>Configurer votre base de données après restauration

Si vous utilisez le basculement de la réplication de geo ou geo restaurer pour récupérer en cas de panne, assurez-vous que la connectivité à nouvelles bases de données est correctement configurée pour que la fonction application normale peut reprendre. Il s’agit d’une liste de vérification des tâches pour préparer votre production récupéré de la base de données.

### <a name="update-connection-strings"></a>Mettre à jour les chaînes de connexion

Parce que votre base de données récupérée résideront dans un autre serveur, vous devez mettre à jour la chaîne de connexion de votre application pour qu’il pointe sur ce serveur.

Pour plus d’informations sur la modification des chaînes de connexion, voir le langage de développement appropriée pour votre [bibliothèque de connexions](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurer des règles de pare-feu

Vous devez vous assurer que les règles de pare-feu configurées sur serveur et sur la base de données correspondent à ceux qui ont été configuré sur le serveur principal et la base de données principale. Pour plus d’informations, voir [Comment : configurer les paramètres de pare-feu (de base de données SQL Azure)](sql-database-configure-firewall-settings.md).


### <a name="configure-logins-and-database-users"></a>Configurer les connexions et les utilisateurs de base de données

Vous devez vous assurer que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données récupérée. Pour plus d’informations, voir [Configuration de la sécurité de la réplication de Geo](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] Vous devez configurer et tester vos règles de pare-feu server et connexions (et leurs autorisations) au cours d’une exploration de récupération d’urgence. Ces objets au niveau du serveur et leur configuration peuvent ne pas être disponibles pendant la coupure.

### <a name="setup-telemetry-alerts"></a>Configurer des alertes de télémétrie

Vous avez besoin pour vous assurer que vos paramètres de règle d’alerte existants sont mis à jour pour faire correspondre à la base de données récupéré et un serveur différent.

Pour plus d’informations sur les règles d’alerte de base de données, voir [Recevoir des Notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) et [Suivre l’état du Service](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Activer l’audit

Si l’audit est requis pour accéder à votre base de données, vous devez activer l’audit après la récupération de base de données. Un bon indicateur que l’audit est requis est que les applications clientes utilisent des chaînes de connexion sécurisée dans un motif de *. database.secure.windows.net. Pour plus d’informations, voir [prise en main d’audit de base de données SQL](sql-database-auditing-get-started.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour connaître les scénarios de conception et de restauration de la continuité professionnels, voir [scénarios continuité](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
