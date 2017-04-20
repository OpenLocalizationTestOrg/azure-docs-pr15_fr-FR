<properties
    pageTitle="Mise à niveau vers V12 de base de données SQL Azure à l’aide du portail Azure | Microsoft Azure"
    description="Explique comment mettre à niveau vers V12 de base de données SQL Azure, notamment comment mettre à niveau des bases de données Web et entreprise et comment mettre à niveau un serveur 11 migration ses bases de données directement dans un pool élastique de base de données à l’aide du portail Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Mise à niveau vers V12 de base de données SQL Azure à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de données SQL est la version la plus récente afin que la mise à niveau des serveurs existants V12 de base de données SQL est recommandé.
V12 de base de données SQL contient de nombreux [avantages par rapport à la version précédente](sql-database-v12-whats-new.md) , y compris :

- Augmenter la compatibilité avec SQL Server.
- Performances améliorées premium et nouveaux niveaux de performances.
- [Des pools élastique de base de données](sql-database-elastic-pool.md).

Cet article fournit des instructions relatives à la mise à niveau des bases de données et les serveurs 11 de base de données SQL existants vers V12 de base de données SQL.

Au cours du processus de mise à niveau vers V12 des bases de données Web et entreprise met à niveau vers un nouveau niveau de service afin que les indications correspondant à la mise à niveau des bases de données Web et d’entreprise sont incluses.

En outre, la migration vers un [pool de base de données élastique](sql-database-elastic-pool.md) peut être plus efficace que la mise à niveau des niveaux de performance individuels (tarification niveaux) pour les bases de données unique. Pools simplifient également la gestion de base de données, car vous ne devez gérer les paramètres de performance pour le pool plutôt que séparément gérer les niveaux de performances des bases de données. Si vous avez des bases de données sur plusieurs serveurs envisagez en les déplaçant dans le même serveur et tirer parti de les placer dans une liste. Vous pouvez facilement [migrer automatique des bases de données à partir de serveurs 11 directement dans des pools élastique de base de données à l’aide de PowerShell](sql-database-upgrade-server-powershell.md). Vous pouvez également utiliser le portail pour migrer des bases de données 11 dans un pool mais dans le portail, vous devez déjà un serveur V12 pour créer un pool. Itinéraires sont fournies plus loin dans cet article pour créer le pool après la mise à niveau du serveur si vous avez des [bases de données qui peuvent bénéficier d’un pool](sql-database-elastic-pool-guidance.md).

Notez que vos bases de données seront restent en ligne et continuer à travailler dans l’ensemble de l’opération de mise à niveau. Au moment de la transition réelle vers le nouveau niveau de performance temporaire de suppression des connexions à la base de données peut se produire pendant une durée très petite est généralement environ 90 secondes, mais peut être autant que 5 minutes. Si votre application a [traitement d’inattendues de connexion de l’erreur transitoire](sql-database-connectivity-issues.md) , il est suffisant pour protéger contre les pertes de connexion à la fin de la mise à niveau.

Mise à niveau vers V12 de base de données SQL ne peut pas être annulée. Après une mise à niveau le serveur ne peut pas être annulée en 11.

Après mise à niveau vers V12, [recommandations de niveau de service](sql-database-service-tier-advisor.md) et [Remarques sur les performances pool élastique](sql-database-elastic-pool-guidance.md) immédiatement sera pas disponibles jusqu'à ce que le service a temps d’évaluer vos charges de travail sur le nouveau serveur. Historique des recommandations de serveur 11 ne s’applique pas aux serveurs V12 afin qu’il n’est pas conservé.

## <a name="prepare-to-upgrade"></a>Préparer la mise à niveau

- **Mettre à niveau toutes les bases de données Web et entreprise**: voir [mettre à niveau toutes les bases de données Web et entreprise](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) section ci-dessous ou [moniteur et gérer un pool de base de données élastique (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Révision et suspendre la réplication de Geo**: Si votre base de données SQL Azure est configuré pour la réplication de Geo vous devez documenter sa configuration en cours et [Arrêter Geo réplication](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois terminée la mise à niveau reconfigurer votre base de données pour la réplication de Geo.
- **Ouvrez les ports si vous disposez de clients sur un ordinateur virtuel Azure**: Si votre programme client se connecte à V12 de base de données SQL pendant l’exécution de votre client sur une Azure machine virtuelle (), vous devez ouvrir plages de port 11000 11999 et 14000-14999 sur l’ordinateur virtuel. Pour plus d’informations, voir [Ports pour V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Démarrer la mise à niveau

1. Dans le [portail Azure](https://portal.azure.com/) naviguer vers le serveur que vous souhaitez mettre à niveau en sélectionnant **Parcourir** > **serveurs SQL**, puis en sélectionnant le serveur de la version 2.0 vous souhaitez mettre à niveau.
2. Sélectionnez la **Dernière mise à jour de base de données SQL**, puis sélectionnez **mettre à jour de ce serveur**.

      ![mise à niveau du serveur][1]

3. Mise à niveau d’un serveur vers la dernière mise à jour de base de données SQL est permanente et irréversible. Pour confirmer la mise à niveau, tapez le nom de votre serveur, puis cliquez sur **OK**.

## <a name="upgrade-all-web-and-business-databases"></a>Mettre à niveau toutes les bases de données Web et entreprise

Si votre serveur comporte des bases de données Web ou entreprise vous devez mettre à niveau les. Au cours du processus de mise à niveau vers SQL de base de données V12 vous mettra à jour toutes les bases de données Web et entreprise sur un nouveau niveau de service.    

Pour vous aider avec la mise à niveau, le service de base de données SQL recommande un niveau et les performances niveau de service approprié (tarification couche) pour chaque base de données. Le service recommande le niveau de meilleures pour l’exécution de la charge de travail de votre base de données existante en analysant l’historique de l’utilisation pour votre base de données.

3. Dans la carte de **mise à niveau de ce serveur** sélectionnez chaque base de données en revue et sélectionnez les tarifs recommandés niveau pour mettre à niveau vers. Vous pouvez toujours parcourir les niveaux de prix disponibles et sélectionnez celle qui le mieux à votre environnement.


     ![bases de données][2]


7. Après avoir cliqué sur la couche suggérée s’affiche avec la carte de **Choisir votre niveau de tarification** où vous pouvez sélectionner un niveau et puis cliquez sur **le bouton Modifier à ce niveau** . Sélectionnez un nouveau niveau pour chaque base de données Web ou entreprise.

    Important à noter est que votre base de données SQL n’est pas verrouillé dans n’importe quel niveau de performance ou de niveau service spécifique, afin que la configuration requise de changement de votre base de données que vous pouvez facilement modifier entre les niveaux de performances et les niveaux de service disponible. En fait, basique, Standard et bases de données SQL Premium sont facturés par l’heure, et vous avez la possibilité d’évoluer chaque base de données vers le haut ou vers le bas 4 heures dans un délai de 24 heures.

    ![recommandations][6]


Une fois que toutes les bases de données sur le serveur sont éligibles vous êtes prêt à commencer la mise à niveau

## <a name="confirm-the-upgrade"></a>Confirmer la mise à niveau

3. Lorsque toutes les bases de données sur le serveur sont éligibles pour la mise à niveau vous devez **TYPE le nom du serveur** pour vérifier que vous voulez effectuer la mise à niveau, puis cliquez sur **OK**.

    ![vérifier la mise à niveau][3]


4. La mise à niveau démarre et affiche la progression de notification. Le processus de mise à niveau est lancé. Selon les détails de vos bases de données spécifiques mise à niveau vers V12 peut prendre un certain temps. Pendant ce temps, toutes les bases de données sur le serveur restera en ligne, mais le serveur et les actions de gestion de base de données se limite.

    ![mettre à niveau en cours][4]

    Au moment de la transition vers le nouvel performances réelle niveau temporaire de suppression des connexions à la base de données peut se produire pendant une durée très petite (généralement mesurée en secondes). Si une application dispose d’une gestion de défaillance transitoires (logique des nouvelles tentatives) d’inattendues de connexion, il est suffisant pour protéger contre les pertes de connexion à la fin de la mise à niveau.

5. Une fois l’opération de mise à niveau se termine la carte de la **Dernière mise à jour** affichera **activé**.

    ![V12 activé][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Déplacer vos bases de données dans un pool élastique de base de données

Dans le [portail Azure](https://portal.azure.com/) naviguer vers le serveur V12 et cliquez sur **Ajouter la liste**.

- ou -

Si vous voyez un message indiquant que **Cliquez ici pour afficher les pools élastique recommandés de la base de données pour ce serveur**, cliquez dessus pour créer facilement un pool est optimisé pour les bases de données de votre serveur. Pour plus d’informations, voir [Considérations prix et de performances d’un pool élastique de base de données](sql-database-elastic-pool-guidance.md).

![Ajouter un pool à un serveur][7]

Suivez les instructions de l’article [créer un pool de base de données élastique](sql-database-elastic-pool.md) pour terminer la création de votre pool.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Surveiller les bases de données après la mise à niveau vers V12 de base de données SQL

>[AZURE.IMPORTANT] Mettre à niveau vers la dernière version de SQL Server Management Studio (SSMS) pour tirer parti des nouvelles fonctionnalités v12. [Télécharger SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Après la mise à niveau, surveiller activement la base de données pour vous assurer que les applications sont en cours d’exécution lors de l’exécution de votre choix et puis optimiser les paramètres souhaités.

Outre l’analyse des bases de données individuelles, vous pouvez surveiller élastique de base de données des pools [moniteur, gérer et la taille d’un pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-manage-portal.md) ou avec [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Données de consommation de ressources :** Pour les bases de données basique, Standard et Premium données de consommation de ressource sont disponibles via le [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) vue dans la base de données utilisateur. Cette vue offre près en temps réel consommation informations sur la ressource à 15 précision deuxième pour l’heure précédente d’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme étant la consommation pourcentage maximal des dimensions processeur, IO et journal. Voici une requête pour calculer la consommation de pourcentage moyenne DTU sur la dernière heure :

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations supplémentaires :

- [Conseils pour les performances de base de données SQL azure des bases de données unique](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considérations prix et de performances d’un pool élastique de base de données](sql-database-elastic-pool-guidance.md).
- [Base de données SQL Azure à l’aide des vues de gestion dynamiques de surveillance](sql-database-monitoring-with-dmvs.md)




**Alertes :** Configurer des « Alertes » dans le portail Azure afin de vous avertir lorsque la consommation DTU pour une base de données mis à niveau approche certaine niveau élevé. Alertes de base de données peuvent être installés dans le portail Azure pour différents indicateurs de performance comme DTU, processeur, IO et journal. Accédez à votre base de données et sélectionnez **règles d’alerte** dans la carte de **paramètres** .

Par exemple, vous pouvez configurer un message d’alerte sur « Pourcentage DTU » si la valeur de pourcentage moyenne DTU est supérieure à 75 % sur les cinq minutes. Pour plus d’informations sur la façon de configurer les notifications d’alerte, reportez-vous à [recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .





## <a name="next-steps"></a>Étapes suivantes

- [Vérifier la présence de recommandations du pool et créer un pool](sql-database-elastic-pool-create-portal.md).
- [Modifier le niveau de service niveau et les performances de votre base de données](sql-database-scale-up.md).



## <a name="related-links"></a>Liens connexes

- [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md)
- [Planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
