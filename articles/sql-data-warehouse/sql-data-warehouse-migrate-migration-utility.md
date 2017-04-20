<properties
   pageTitle="Migrer : Utilitaire de Migration de magasin de données | Microsoft Azure"
   description="Migrer vers Data Warehouse SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Utilitaire de Migration de magasin de données (Preview)

> [AZURE.SELECTOR]
- [Télécharger l’utilitaire de Migration][]

Data Warehouse Migration Utility est un outil conçu pour migrer les données et schéma à partir de SQL Server et base de données SQL Azure Azure SQL Data Warehouse. Lors de la migration de schéma, l’outil mappe automatiquement le schéma correspondant à partir de la source vers la destination. Après que le schéma de migration, les outils offre la possibilité pour déplacer des données avec des scripts générées automatiquement.

En plus de migration de schéma et les données, cet outil vous donne la possibilité de générer des rapports de compatibilité qui résument incompatibilité entre les instances source et cible qui peuvent empêcher la migration rationalisée.

## <a name="get-started"></a>Prise en main
En tant que composant requis pour l’installation, vous devez l’utilitaire de ligne de commande BCP pour exécuter les scripts de migration et Office pour afficher le rapport de compatibilité. Après le lancement de l’exécutable est téléchargé vous devrez accepter un CLUF standard avant l’installation de l’outil.

En outre, pour exécuter la Utiliy de Migration, vous devez l’option suivi d’autorisations sur la base de données que vous recherchez pour migrer : créer une base de données, ALTER ANY DATABASE ou VIEW ANY DEFINITION.

### <a name="launching-the-tool-and-connecting"></a>Lancer l’outil et connexion
Lancez l’outil en cliquant sur l’icône du bureau qui s’affiche après installation. Lors de l’ouverture de l’outil, vous serez invité avec une page de connexion initiale dans laquelle vous pouvez choisir votre source et destination pour l’outil de migration. À ce stade, nous prennent en charge SQL Server et base de données SQL Azure en tant que sources et SQL Data Warehouse comme destination. Après avoir sélectionné cette vous êtes invité à se connecter à votre serveur source en remplissant le nom de serveur et l’authentification et puis en cliquant sur « Se connecter ».

Après avoir authentifié, l’outil affiche une liste des bases de données qui sont présents sur le serveur auquel vous êtes connecté à. Vous pouvez commencer la migration en sélectionnant une base de données que vous voulez migrer, puis en cliquant sur « Migrer sélectionné ».

## <a name="migration-report"></a>Rapport de migration
Sélection de vérifier la compatibilité de base de données dans l’outil génère un rapport résumer tous les incompatibilité objet dans la base de données que vous souhaitez pour migrer. Vous trouverez une liste plus large de certaines des fonctionnalités de SQL Server qui ne figure pas dans SQL Data Warehouse dans notre [documentation de migration][]. Une fois que le rapport est généré vous pourrez enregistrer et ouvrir le rapport dans Excel.

Notez que lors de la génération du schéma de migration, la plupart des problèmes identifiés comme « Objet » est ajusté afin de permettre la migration immédiate de ces données. Vérifiez les modifications pour vous assurer que vous ne souhaitez pas apporter des modifications supplémentaires avant d’appliquer le schéma.

## <a name="migrate-schema"></a>Migrer le schéma

Une fois connecté, sélection 'Schéma migrer' génère un script de migration du schéma pour les tables sélectionnées. Cette ports script la structure de la table, mappe les données incompatible types de formulaires compatibles plus et crée schéma et les informations d’identification de sécurité si cela est indiqué par l’utilisateur dans les paramètres de migration. Ce code peut être exécuté sur l’instance SQL Data Warehouse ciblée, enregistrées dans un fichier, copié dans le Presse-papiers ou même modifiés en ligne avant d’effectuer une action supplémentaire.  

Comme indiqué ci-dessus, lors de la migration schéma la migration les modifications que l’outil a été afin de vous assurer que bien comprendre les.  

## <a name="migrate-data"></a>Migrer les données

En cliquant sur l’option migrer les données que vous pouvez générer des scripts BCP qui permet de déplacer vos données tout d’abord vers plats fichiers sur votre serveur, puis directement dans votre Data Warehouse SQL. Nous vous recommandons de ce processus de déplacement des petites quantités de données et, en tant que tentatives ne sont pas intégrés et peut échouer s’il existe une perte de la connexion réseau. Afin d’exécuter cet exemple, vous devez avoir l’utilitaire de ligne de commande BCP installé et le schéma pour les données doit ont déjà été créé.

Après avoir rempli les paramètres ci-dessus vous deviez simplement cliquer sur migration exécution et un ensemble de deux packages sera généré à l’emplacement spécifié. Exécuter le fichier d’exportation pour exporter des données à partir de votre source de migration vers des fichiers plats, puis exécutez le fichier d’importation pour importer vos données dans Data Warehouse SQL.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez effectué une migration des données, consultez le [développement][].

<!--Image references-->

<!--Article references-->
[documentation de migration]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Télécharger l’utilitaire de Migration]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
