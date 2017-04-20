<properties
   pageTitle="Charge de travail données entrepôt"
   description="Élasticité du SQL Data Warehouse vous permet d’agrandir, réduire ou suspendre la puissance de calcul en utilisant une échelle coulissante entrepôt des unités de données (DWUs). Cet article explique les mesures de magasin de données et comment ils sont liés aux DWUs. "
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
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>Charge de travail données entrepôt
Une charge de travail de magasin de données fait référence à toutes les opérations qui s’écouler par rapport à un data warehouse. La charge de travail données warehouse englobe l’ensemble du processus de chargement des données dans le magasin, l’analyse et création de rapports sur le data warehouse, gestion des données dans le data warehouse et l’exportation de données à partir du magasin de données. L’étendue et ces composants sont souvent proportionnelles avec le niveau de maturité du data warehouse.


## <a name="new-to-data-warehousing"></a>Vous débutez sur le stockage des données ?
Un data warehouse est un ensemble de données qui sont chargé à partir d’une ou plusieurs sources de données et sont utilisés pour effectuer des tâches de décisionnel tels que des rapports et analyse de données.

Data Warehouse est caractérisée par les requêtes analyse plus grand nombre de lignes, grandes plages de données et peuvent retourner des résultats relativement longs aux fins d’analyse et de création de rapports. Data Warehouse est également caractérisée par charges relativement importantes de données et au niveau des transactions petites insertions/mises à jour/suppressions.

- Un data warehouse donne de meilleurs résultats lorsque les données sont stockées de façon à optimiser les requêtes que vous avez besoin d’analyser grand nombre de lignes ou des plages de données de grande taille. Ce type d’analyse fonctionne mieux lorsque les données sont stockées et des recherches en colonnes, plutôt que de lignes.

>[AZURE.NOTE] L’index columnstore en mémoire, qui utilise le stockage de colonne, fournit jusqu'à 10 fois gains de compression et 100 x gains de performances de requête sur traditionnels arborescences binaires pour les requêtes de création de rapports et analytique. Nous prenons l’index columnstore la norme pour le stockage et l’analyse de données volumineux dans un data warehouse.

- Un data warehouse comporte des exigences différentes à un système pour optimiser pour online traitement des transactions (OLTP). Le système OLTP comporte de nombreux insérer, mettre à jour et supprimer des opérations. Ces opérations de recherche à des lignes spécifiques du tableau. Table cherche fonctionnent mieux si les données sont stockées sous forme de ligne par ligne. Les données peuvent être triées rapidement une division de recherche et relever approche appelée une recherche arborescence ou btree binaire.


## <a name="data-loading"></a>Chargement de données
Chargement de données est une grande partie de la charge de travail de magasin de données. Entreprises disposent généralement d’un système OLTP occupé (e) qui effectue le suivi des modifications dans la journée lorsque clients génèrent des transactions commerciales. Régulièrement, souvent nuit pendant une période de maintenance, les transactions sont déplacées ou copiées vers le data warehouse. Une fois les données dans le data warehouse, les analystes peuvent effectuer une analyse et prendre des décisions commerciales sur les données.

- En règle générale, le processus de chargement est appelé ETL pour extraire, transformer et charger. Données généralement doivent être transformés afin qu’elle soit cohérente avec d’autres données dans le data warehouse. Précédemment, entreprises utilisé des serveurs ETL dédiés pour effectuer les transformations. À présent, avec ce type de traitement rapide considérablement en parallèle, vous pouvez charger les données dans Data Warehouse SQL tout d’abord et ensuite effectuer les transformations. Ce processus est appelé extraire, charger et transformer (ELT) et devient un nouveau standard pour la charge de travail de magasin de données.

> [AZURE.NOTE] Avec SQL Server 2016, vous pouvez désormais effectuer analytique en temps réel sur une table OLTP. Cela ne remplace pas la nécessité d’un data warehouse stocker et analyser des données, mais elle fournit un moyen pour effectuer une analyse en temps réel.

### <a name="reporting-and-analysis-queries"></a>Rapports et l’analyse des requêtes
Rapports et l’analyse des requêtes sont souvent classés petite, moyenne et grande basé sur un numéro de critères, mais en règle générale, il est basé sur le temps. Dans la plupart des magasins de données, il existe une charge de travail mixte de durée d’exécution rapide et requêtes à long terme. Dans chaque cas, il est important pour déterminer cette combinaison et afin de déterminer son fréquence (toutes les heures, tous les jours, fin de mois, fin de trimestre, etc.). Il est important de comprendre que la charge de travail mixte requête associée à la concurrence, vous amener à planification appropriée pour un data warehouse.

- Planification de la capacité peut être une tâche complexe pour une charge de travail mixte requête, particulièrement quand vous avez besoin d’un long délai augmenter la capacité au data warehouse. Data Warehouse SQL supprime l’urgence de planification de la capacité dans la mesure où vous pouvez agrandir et réduire la capacité cluster à tout moment et depuis le stockage et de capacité de traitement sont dimensionné séparément.

### <a name="data-management"></a>Gestion des données
Gestion des données est important, en particulier lorsque vous savez que vous pouvez manquer d’espace disque prochainement. Data Warehouse diviser généralement les données dans les plages significatifs, qui sont stockés en tant que partitions dans un tableau. Tous les produits de SQL Server vous permettent de déplacer des partitions vers et depuis la table. Cette partition basculement vous permet de déplacer les données plus anciennes vers un stockage moins coûteuse et conserver les données les plus récentes disponibles sur le stockage en ligne.

- Index ColumnStore prend en charge les tables partitionnées. Pour les index columnstore, les tables partitionnées sont utilisés pour la gestion des données et l’archivage. Pour les tables de stockage en ligne, partitions ont un rôle plus important dans les performances des requêtes.  

- PolyBase joue un rôle important dans la gestion des données. À l’aide de PolyBase, vous avez la possibilité d’archiver les données antérieures à Hadoop ou Azure blob storage.  Cette offre une grande des options dans la mesure où les données sont toujours en ligne.  Il peut être plus longue pour récupérer des données à partir de Hadoop, mais le compromis d’extraction peut l’emportent sur le coût de stockage.

### <a name="exporting-data"></a>Exportation de données
Une des façons de rendre disponibles pour les rapports et analyse de données consiste à envoyer des données à partir du magasin de données aux serveurs dédié pour exécuter des rapports et analyse. Ces serveurs sont appelés magasins de données. Par exemple, vous faire prétraitement des données de rapport et puis exporter à partir du magasin de données à de nombreux serveurs dans le monde, afin de pouvoir être disponibles pour les clients et les analystes.

- Pour générer des rapports, chaque nuit vous pourriez renseigner les serveurs de rapports en lecture seule avec un instantané des données quotidiennes. Cela donne une bande passante pour les clients tout en réduisant les besoins en ressources cluster sur le data warehouse. À partir d’un aspect sécurité, magasins de données vous permettent de réduire le nombre d’utilisateurs qui ont accès au data warehouse.
- Pour analytique, vous pouvez créer un cube analysis sur le data warehouse et lancer une analyse sur le data warehouse ou traiter des données et exporter au serveur d’analyse pour poursuivre analytique.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez un peu Data Warehouse SQL, découvrez comment rapidement [créer un Data Warehouse SQL][] et [charger les exemples de données][].

<!--Image references-->

<!--Article references-->
[charger les exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[créer un Data Warehouse SQL]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
