<properties
    pageTitle="Surveiller et gérer un pool de base de données élastique grâce au portail Azure | Microsoft Azure"
    description="Découvrez comment utiliser le portail Azure et analyse décisionnelle intégrée de bases de données SQL pour gérer, surveiller et un pool élastique scalable de la base de données pour optimiser les performances de base de données et gérer les coûts de taille de droite."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Surveiller et gérer un pool de base de données élastique grâce au portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Vous pouvez utiliser le portail Azure pour surveiller et gérer un pool élastique de base de données et les bases de données dans le pool. À partir du portail, vous pouvez surveiller l’utilisation d’un pool élastique et les bases de données dans ce pool. Vous pouvez également créer un jeu de modifications à votre pool élastique et envoyer toutes les modifications en même temps. Ces modifications comprennent ajouter ou supprimer les bases de données, modification de vos paramètres de pool élastique ou modification de vos paramètres de base de données.

L’illustration ci-dessous montre un pool élastique d’exemple. L’affichage inclut :

*  Graphiques pour surveiller l’utilisation des ressources du pool élastique et les bases de données contenues dans la liste.
*  Le bouton pool **configurer** pour apporter des modifications au pool élastique.
*  Le bouton **créer une base de** ce qui crée une nouvelle base de données et l’ajoute au pool élastique en cours.
*  Élastiques travaux qui vous aident à gérer un grand nombre de bases de données en exécutant les scripts Transact SQL sur toutes les bases de données dans une liste.

![Affichage du pool][2]

Pour suivre les étapes décrites dans cet article, vous devez un serveur SQL server dans Azure au moins une base de données et un pool élastique. Si vous ne disposez pas d’un pool élastique, voir [créer un pool](sql-database-elastic-pool-create-portal.md); Si vous n’avez pas une base de données, consultez le [didacticiel de base de données SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Surveillance pool élastique

Vous pouvez accéder à un pool spécifique pour afficher son utilisation des ressources. Par défaut, le pool est configuré pour afficher l’utilisation de stockage et eDTU pendant la dernière heure. Le graphique peut être configuré pour afficher les différentes mesures sur différentes fenêtres du temps.

1. Sélectionnez un regroupement pour l’utiliser avec.
2. Sous **Analyse Pool élastique** est un graphique intitulé **l’utilisation des ressources**. Cliquez sur le graphique.

    ![Surveillance pool élastique][3]

    La carte **métrique** s’ouvre, affichant une vue détaillée des mesures spécifiés au-dessus de la fenêtre de temps spécifié.   

    ![Carte métrique][9]

### <a name="to-customize-the-chart-display"></a>Pour personnaliser l’affichage du graphique

Vous pouvez modifier le graphique et la carte métrique pour afficher d’autres mesures de tels que processeur pourcentage pourcentage IO de données et pourcentage de IO journal utilisé.

2. Dans la carte métrique, cliquez sur **Modifier**.

    ![Cliquez sur Modifier][6]

- Dans la carte de **Modifier le graphique** , sélectionnez une nouvelle plage de temps (heure, l’heure actuelle, ou semaine dernière), ou cliquez sur **personnalisée** pour sélectionner une plage de dates dans les deux dernières semaines. Sélectionnez le type de graphique (barres ou linéaire), puis sélectionnez les ressources à surveiller.

> Remarque : Uniquement les mesures avec la même unité de mesure peuvent être affichés dans le graphique en même temps. Par exemple, si vous sélectionnez « eDTU pourcentage » puis vous uniquement sera en mesure de sélectionner d’autres mesures de pourcentage comme unité de mesure.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Cliquez ensuite sur **OK**.


## <a name="elastic-database-monitoring"></a>Surveillance élastique bases de données

Bases de données individuelles peuvent également être recherchés problèmes potentiels.

1. Sous **Élastique surveillance de base de données**, il est un graphique affichant des mesures pour cinq bases de données. Par défaut, le graphique affiche les 5 principaux bases de données dans le pool par moyenne eDTU l’utilisation dans la dernière heure. Cliquez sur le graphique.

    ![Surveillance pool élastique][4]

2. La carte de **L’utilisation des ressources de base de données** s’affiche. Il fournit une vue détaillée de l’utilisation de la base de données dans le pool. À l’aide de la grille dans la partie inférieure de la cuillère, vous pouvez sélectionner les bases de données dans la liste pour afficher son utilisation dans le graphique (jusqu'à 5 bases de données). Vous pouvez également personnaliser la fenêtre d’indicateurs et l’heure affichée dans le graphique en cliquant sur **Modifier le graphique**.

    ![Carte de l’utilisation des ressources de base de données][8]

### <a name="to-customize-the-view"></a>Pour personnaliser l’affichage

1. Dans la carte de **l’utilisation des ressources de base de données** , cliquez sur **Modifier le graphique**.

    ![Cliquez sur Modifier un graphique](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Dans la carte de graphique **Modifier** , sélectionnez une nouvelle plage horaire (après heure ou dernières 24 heures), ou cliquez sur **personnalisée** pour sélectionner un autre jour dans les 2 dernières semaines à afficher.

    ![Cliquez sur personnalisée](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Cliquez sur la zone déroulante **comparer les bases de données par** pour sélectionner une autre mesure à utiliser lors de la comparaison de bases de données.

    ![Modifier le graphique](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Pour sélectionner des bases de données à surveiller

Dans la liste de la base de données dans la carte de **L’utilisation des ressources de base de données** , vous pouvez trouver des bases de données particuliers en parcourant les pages dans la liste ou en tapant le nom d’une base de données. La case à cocher permet de sélectionner la base de données.

![Rechercher des bases de données à surveiller][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Ajouter une alerte pour une ressource pool

Vous pouvez ajouter des règles aux ressources envoient des messages électroniques à des personnes ou des chaînes d’alerte aux points de terminaison URL lors de la ressource atteint un seuil de l’utilisation que vous avez configurées.

> [AZURE.IMPORTANT]L’utilisation des ressources pour des Pools élastique de surveillance a un retard d’au moins 20 minutes. Définir des alertes de moins de 30 minutes pour des Pools élastique n’est pas actuellement pris en charge. Toutes les alertes définir pour des Pools élastique par un point (paramètre appelé «-WindowSize » dans l’API PowerShell) de moins de 30 minutes ne peut pas être déclenchée. Assurez-vous que toutes les alertes que vous définissez pour des Pools élastique utilisent un point (WindowSize) de 30 minutes ou plus.

**Pour ajouter une alerte à n’importe quelle ressource :**

1. Cliquez sur le graphique de **l’utilisation des ressources** pour ouvrir la carte de **mesure** , cliquez sur **Ajouter une alerte**, puis renseignez les informations dans la carte de **Ajouter une règle d’alerte** (**ressource** est automatiquement configuré pour être le pool avec lequel vous travaillez).
2. Tapez un **nom** et une **Description** qui identifie vous-même et les destinataires de l’alerte.
3. Choisissez une **métrique** que vous voulez dans la liste des alertes.

    Le graphique affiche dynamiquement l’utilisation des ressources pour cette mesure afin de vous aider à choisir un seuil.

4. Sélectionnez une **Condition** (supérieures ou inférieures à, etc.) et un **seuil**.
5. Cliquez sur **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou supprimer des bases de données à partir d’un pool existant. Les bases de données peuvent être dans d’autres pools. Toutefois, vous pouvez uniquement ajouter des bases de données qui se trouvent sur le même serveur logique.

1. Dans la carte pour le regroupement, sous **élastiques bases de données** , cliquez sur **configurer pool**.

    ![Cliquez sur Configurer le pool][1]

2. Dans la carte **pool de configurer** , cliquez sur **Ajouter au pool**.

    ![Cliquez sur Ajouter au pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. Dans la carte **Ajouter les bases de données** , sélectionnez la base de données ou les bases de données à ajouter à la liste. Puis cliquez sur **Sélectionner**.

    ![Sélectionnez Ajouter des bases de données](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    La carte de **configurer pool** répertorie désormais la base de données sélectionnée pour être ajouté, son statut défini sur **en attente**.

    ![Ajouts pool en attente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Dans la **carte du pool configurer**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Retirer une base de données d’un pool élastique

1. Dans la carte **pool configurer** , sélectionnez la base de données ou les bases de données à supprimer.

    ![bases de données de liste](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Cliquez sur **Supprimer du pool**.

    ![bases de données de liste](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    La carte de **configurer pool** répertorie désormais la base de données que vous avez choisi d’être supprimé avec son statut défini sur **en attente**.

    ![Ajout de base de données (version préliminaire) et la suppression](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Dans la **carte du pool configurer**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performances d’un pool

Lorsque vous analysez l’utilisation des ressources d’un pool, vous pouvez constater que quelques ajustements sont nécessaires. Peut-être que le pool doit être modifiée dans les limites de stockage ou des performances. Éventuellement, vous souhaitez modifier les paramètres de base de données dans le pool. Vous pouvez modifier la configuration du pool à tout moment pour trouver le meilleur équilibre de performances et de coût. Voir [quand un pool élastique de base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md) pour plus d’informations.

**Pour modifier les limites de stockage ou eDTUs par pool et eDTUs par base de données :**

1. Ouvrez la carte **pool de configurer** .

    Sous **paramètres du pool élastique de base de données**, utilisez un curseur pour modifier les paramètres du pool.

    ![Utilisation des ressources pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Lorsque le paramètre est modifiée, l’écran affiche le coût mensuel estimé de la modification.

    ![Mise à jour un pool et le nouveau coût mensuel](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Créer et gérer les travaux élastique

Tâches élastiques vous permettent d’exécuter des scripts Transact-SQL par rapport à un nombre quelconque de bases de données dans le pool. Vous pouvez créer de nouvelles tâches ou gérer les travaux existants à l’aide du portail.

![Créer et gérer les travaux élastique][5]


Avant d’utiliser des tâches, installez composants travaux élastique et vos informations d’identification. Pour plus d’informations, voir [vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-overview.md).

Voir [mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet de horizontale, de déplacer des données, de la requête, ou créer des transactions.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Pool élastique de base de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de base de données élastique avec le portail](sql-database-elastic-pool-create-csharp.md)
- [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Créer un pool de base de données élastique avec c#](sql-database-elastic-pool-create-csharp.md)
- [Remarques sur le prix et des performances pour des pools élastique de base de données](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
