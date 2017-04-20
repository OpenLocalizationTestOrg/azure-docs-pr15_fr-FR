<properties
   pageTitle="Gérer les bases de données dans SQL Azure Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut les outils d’administration, DWUs et performances horizontale, résolution des problèmes de performances des requêtes, définition de stratégies de sécurité et de restaurer une base de données à partir de l’altération des données ou une panne régionale."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gérer les bases de données dans le magasin de données SQL Azure

Data Warehouse SQL permet d’automatiser nombreux aspects de la gestion de vos bases de données. Par exemple, pour adapter les performances vous uniquement devez régler et payer pour le niveau de ressources de calcul, puis laissez SQL Data Warehouse faire tout le travail de l’évolution horizontale et la même échelle précédent. 

Vous souhaiterez sans doute surveiller votre charge de travail pour identifier vos besoins de performances, ainsi que pour résoudre les requêtes à long terme. Vous devrez également effectuer quelques tâches de sécurité pour gérer les autorisations des utilisateurs et des connexions.

Cette vue d’ensemble couvre ces aspects de la gestion des Data Warehouse SQL.

- Outils de gestion
- Échelle cluster
- Suspendre et reprendre
- Meilleures pratiques de performances
- Analyse de la requête
- Sécurité
- Sauvegarde et restauration

## <a name="management-tools"></a>Outils de gestion

Vous pouvez utiliser une série d’outils pour gérer les bases de données SQL Data Warehouse. Lorsque vous gérez les bases de données, vous allez développer les préférences de l’outil pour chaque type de tâche, que vous devez effectuer.

### <a name="azure-portal"></a>Portail Azure
Le [portail Azure][] est un portail web dans laquelle vous pouvez créer, mettre à jour et supprimer des bases de données et surveiller les ressources de base de données. Cet outil est très est si vous venez juste de commencer avec Azure, gérez un petit nombre de bases de données warehouse données, ou si vous devez effectuer rapidement une action.

Pour commencer à utiliser le portail Azure, voir [créer un SQL Data Warehouse (Azure portal)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>Outils de données SQL Server dans Visual Studio
[Outils de données SQL Server][] (SSDT) dans Visual Studio vous permet de vous connecter, gérer et développer vos bases de données. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), essayez d’utiliser SSDT dans Visual Studio.

SSDT inclut l’Explorateur d’objets SQL Server qui vous permet de visualiser, vous connecter et exécuter des scripts sur les bases de données SQL Data Warehouse. Pour vous connecter rapidement à Data Warehouse SQL, vous pouvez cliquer simplement sur le bouton **Ouvrir dans Visual Studio** dans la barre de commandes lorsque vous affichez les détails de la base de données dans le portail classique Azure.  

Pour commencer à utiliser SSDT dans Visual Studio, consultez [Requête Azure SQL Data Warehouse avec Visual Studio][].

### <a name="command-line-tools"></a>Outils de ligne de commande
Outils de ligne de commande sont parfaits pour automatiser vos charges de travail.  PowerShell et sqlcmd de deux manières correctement d’automatiser vos processus.  Nous vous recommandons de ces outils permettant de gérer un grand nombre de serveurs logiques et déploiement des modifications de ressources dans un environnement de production que les tâches nécessaires peuvent être un script et puis automatisées.

### <a name="dynamic-management-views"></a>Vues de gestion dynamiques 

DMV est indispensables de gestion de Data Warehouse SQL. Presque toutes les informations qui couvre dans le portail s’appuie sur DMV. Pour afficher la liste des DMV de magasin de données SQL, consultez [les vues système Data Warehouse SQL][].

Pour commencer, voir [se connecter et requête de sqlcmd][]et [créer une base de données (PowerShell)][].

## <a name="scale-compute"></a>Échelle cluster

Dans SQL Data Warehouse, vous pouvez rapidement adapter les performances ou revenir à croissantes ou décroissantes cluster ressources du processeur, la mémoire et de la bande passante. Pour adapter les performances, il vous souhaitez est ajuster le nombre d’unités de magasin de données (DWUs) qui SQL Data Warehouse affecte à votre base de données. Data Warehouse SQL modifie les rapidement et gère toutes les modifications sous-jacentes de matériel ou de logiciel.

Pour en savoir plus sur la mise à l’échelle DWUs, voir [adapter les performances][].

##  <a name="pause-and-resume"></a>Suspendre et reprendre

Pour enregistrer les coûts, vous pouvez interrompre et reprendre cluster ressources à la demande. Par exemple, si vous n’utiliserez pas la base de données pendant la nuit et le week-end, vous pouvez suspendre pendant ces heures et reprendre pendant la journée. Vous ne seront-elles facturées de DWUs pendant la suspension de la base de données.

Pour plus d’informations, voir [Pause calculer][]et [Calculer CV][].

## <a name="performance-best-practices"></a>Meilleures pratiques de performances

Lorsque vous commencez à utiliser une nouvelle technologie, découvrir les conseils et astuces fonctionnant meilleures droite à partir du début peut de gagner beaucoup de temps.  Vous trouverez les meilleures pratiques tout au long de la plupart des nos sujets.

Pour afficher plusieurs un résumé des considérations plus importantes lorsque vous développez votre charge de travail, voir [Meilleures pratiques SQL données entrepôt][].

## <a name="query-monitoring"></a>Analyse de la requête

Parfois une requête est trop longue, mais vous n’êtes pas sûr de celui qui est la cause du problème. SQL Data Warehouse contient des vues de gestion dynamiques (DMV) que vous pouvez utiliser pour déterminer quelle requête prend trop de temps. 

Pour rechercher des requêtes à long terme, voir [surveiller votre charge de travail à l’aide de DMV][].

## <a name="security"></a>Sécurité

Pour conserver un système sécurisé, vous devez être dans l’alerte et se protéger contre n’importe quel type d’accès non autorisé. Un système de sécurité doit s’assurer que les règles de pare-feu sont en place afin que seule autorisé adresses IP peuvent se connecter. Il doit authentifications des informations d’identification utilisateur. Une fois un utilisateur s’est connecté à la base de données, l’utilisateur doit avoir uniquement des autorisations pour effectuer un nombre minimal d’actions. Pour sécuriser les données, vous pouvez utiliser le chiffrement. Il est également important de disposer d’audits et le suivi de sorte que vous pouvez retracer événements s’il existe une activité suspecte.

Pour en savoir plus sur la gestion de sécurité, tête sur la [Présentation de la sécurité][].

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Vous rencontrez des backps fiable de vos données sont une partie essentielle d’une base de données de production. Data Warehouse SQL préserver vos données en sauvegardant automatiquement vos bases de données actives à intervalles réguliers. Ces sauvegardes permettent de récupérer des scénarios où que vous avez vos données corrompues ou accidentellement supprimé vos données ou une base de données.  Pour la planification de sauvegarde de données, stratégie de rétention et comment restaurer une base de données, voir [restaurer à partir d’un instantané][].

## <a name="next-steps"></a>Étapes suivantes
À l’aide de conception de base de données bon principes facilitera gérer vos bases de données SQL Data Warehouse. Pour plus d’informations, tête sur la [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[Créer un SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Créer une base de données (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Requête SQL Azure Data Warehouse avec Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Se connecter et de la requête avec sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Vue d’ensemble de développement]: sql-data-warehouse-overview-develop.md
[Surveiller votre charge de travail à l’aide de DMV]: sql-data-warehouse-manage-monitor.md
[Cluster pause]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restaurer à partir d’un instantané]: sql-data-warehouse-restore-database-overview.md
[CV cluster]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Adapter les performances]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Présentation de la sécurité]: sql-data-warehouse-overview-manage-security.md
[Meilleures pratiques SQL données entrepôt]: sql-data-warehouse-best-practices.md
[Vues système Data Warehouse SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[Outils de données SQL Server]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portail Azure]: http://portal.azure.com/
