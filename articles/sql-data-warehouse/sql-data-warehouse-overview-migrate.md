<properties
   pageTitle="Migrer votre solution vers SQL Data Warehouse | Microsoft Azure"
   description="Guide de migration pour mettre votre solution sur plateforme Azure SQL Data Warehouse."
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
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migrer votre solution vers Data Warehouse SQL

SQL Data Warehouse est un système de base de données distribuée ELASTIQUEMENT nuances de vos besoins. Pour maintenir des performances et échelle, pas toutes les fonctions de SQL Server sont implémentées à l’intérieur de Data Warehouse SQL. Appuyez sur les rubriques suivantes de la migration sur certains des facteurs clés de la migration de votre solution SQL Data Warehouse. Conception data warehouse pour échelle présente les différents modèles et approches donc traditionnels n’est pas toujours la meilleure. Vous trouverez peut-être dès lors qu’adapter votre solution existante garantit que vous tirer pleinement parti de la plateforme distribuée fournie par SQL Data Warehouse.

Il est également important n’oubliez pas que SQL Data Warehouse est une plateforme basée à Microsoft Azure. Par conséquent partie de votre migration peut inclure également transférer vos données dans le cloud. Transfert de données est un objet dans sa propre droite et doit être considéré comme avec soin ; surtout que les volumes d’augmenter. Transfert de données et le chargement de données sont des sujets discrètes.

## <a name="migration-guidance"></a>Guide de migration

Vérifiez que vous avez pris connaissance de ces articles pour vous assurer que vous comprenez certaines différences de produit et les concepts fondamentaux avant de commencer la migration.

- [Migrer votre schéma][]
- [Migrer vos données][]
- [Migrer votre code][]

## <a name="next-steps"></a>Étapes suivantes

Le Chat (équipe de conseil clientèle) est également quelques recommandations SQL Data Warehouse excellente dont ils publient via blogs.  Examinons leur article, [faire migrer des données à Data Warehouse SQL Azure dans la pratique][] pour obtenir des conseils supplémentaires sur la migration.

<!--Image references-->

<!--Article references-->
[Migrer votre schéma]: sql-data-warehouse-migrate-schema.md
[Migrer vos données]: sql-data-warehouse-migrate-data.md
[Migrer votre code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migration des données vers SQL Azure Data Warehouse dans la pratique]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
