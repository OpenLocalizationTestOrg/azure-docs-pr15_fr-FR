<properties 
   pageTitle="Tarifs recommandations niveau pour la base de données SQL Azure" 
   description="Lorsque vous modifiez les tarifs niveaux dans le portail Azure, tarifs recommandations couche est fournis recommander la couche qui correspond le mieux adapté à la charge de travail un existant Azure SQL de bases de données en cours d’exécution. Niveaux de tarification décrire le niveau de performances et de niveau de service d’une base de données SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Recommandations couche tarification de base de données SQL

 Recommandations couche tarification suggérant le niveau de service couche et de performances qui est idéale pour l’exécution de la charge de travail d’une base SQL Azure existante.

> [AZURE.NOTE] Recommandations couche tarification sont uniquement disponibles pour les bases de données Web et de l’entreprise et des pools de base de données élastique--et disponible uniquement dans le [portail Azure](https://portal.azure.com/).


Obtenez le tarif recommandations couche pendant les tâches suivantes :

- [Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL](sql-database-scale-up.md)
- [Mise à niveau Azure SQL server V12](sql-database-upgrade-server-portal.md)
- Accédez à votre serveur V12. Consultez la [base de données SQL tarification recommandations niveau](sql-database-service-tier-advisor.md).
- [Créer un pool élastique de base de données](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Vue d’ensemble

Le service de base de données SQL analyse des performances actuel et les exigences de la fonction en analysant l’utilisation des ressources historiques pour une base de données SQL. En outre, le niveau de service acceptable minimum est déterminé par la taille de la base de données et les fonctionnalités activées [continuité des activités](sql-database-business-continuity.md) . 

Ces informations sont analysées et le niveau de service couche et de performances qui correspond le mieux adapté pour l’exécution de charge de travail standard de la base de données et leur gestion qu'est en cours ensemble de fonctionnalités est recommandé.

- Le service examine des données historiques (utilisation des ressources, la taille de la base de données et activité de base de données) 15 à 30 jours précédents et effectue une comparaison entre la quantité de ressources consommées et les limites réels des niveaux de performances et les niveaux de service actuellement disponibles.
- Données sont analysées dans 15 secondes et jeu de résultats de chaque intervalle est classé selon le niveau de service existante et niveau de performance qui correspond le mieux adapté pour gérer la charge de travail de ce jeu de résultats.
- Ces 15 secondes exemples sont regroupées puis dans l’analyse de 15-30 jours supérieure et le niveau de service couche et de performances qui peut gérer optimale de 95 % de la charge de travail historique est recommandé.

### <a name="recommendations"></a>Recommandations

En fonction de l’utilisation de votre base de données, il existe actuellement 2 catégories de recommandations que vous pouvant rencontrer :


| Recommandation | Description |
| :--- | :--- |
| Mise à niveau | Mettre à niveau vers un nouveau niveau. |
| Non disponible | Une base de données requiert une charge de travail minimale ou 35 jours environ d’activité. Il n’est pas suffisamment de données pour fournir une recommandation valide. |

## <a name="getting-pricing-tier-recommendations"></a>Prise tarifs recommandations niveau

Obtenir des tarifs recommandations couche en sélectionnant une base de données Web ou entreprise existante, cliquez sur **tous les paramètres**, puis sur **couche tarification (échelle DTUs)**. (Tarification couche recommandations sont également disponibles lorsque vous [mettez à niveau Azure SQL server V12](sql-database-upgrade-server-portal.md).)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Parcourir** > **bases de données SQL**.
4. Dans la carte de **bases de données SQL** , cliquez sur la base de données que vous voulez afficher une recommandation pour :

    ![Sélectionnez la base de données][1]

5. Dans la carte de base de données, sélectionnez **tous les paramètres** , puis sélectionnez **tarification couche (échelle DTUs)**.


7. La **recommandé tarifs niveaux** ouvrir l’endroit où vous pouvez cliquez sur la couche suggérée et puis cliquez sur **le bouton Modifier à ce niveau** .

    ![S’inscrire pour l’aperçu][4]

8. Si vous le souhaitez, cliquez sur **Afficher les détails de l’utilisation** pour ouvrir la carte **Tarifs couche recommandation détails** dans laquelle vous pouvez afficher la couche recommandée pour la base de données, une comparaison des fonctionnalités entre les niveaux actuels et recommandés et un graphique de l’analyse de l’utilisation des ressources historiques.

    ![S’inscrire pour l’aperçu][5]



## <a name="summary"></a>Résumé

Recommandations couche tarification fournissent une expérience automatisée pour collecter les données de télémétrie pour chaque base de données SQL et de recommandations la meilleure service/performances de niveau niveau combinaison en fonction des besoins de performances réelles et des exigences de la fonction d’une base de données. Sur la carte de paramètres cliquez sur **couche de tarification (échelle DTUs)** pour afficher les tarifs recommandations couche pour les bases de données Web et entreprise.



## <a name="next-steps"></a>Étapes suivantes

Selon les détails de votre base de données, effectuez une mise à niveau ou un déclassement généralement n’a pas lieu instantanément. Le portail de fournir des notifications en tant que les transitions de base de données vers sa nouvelle couche, ou vous pouvez surveiller l’état de mise à niveau par l’interrogation de la vue [sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) dans la base de données principale du serveur de base de données SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
