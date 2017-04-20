<properties
   pageTitle="Utiliser des données Azure usine avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation d’usine de données Azure (chargeur) avec Azure SQL Data Warehouse pour développer des solutions."
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

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Utiliser des données Azure usine avec Data Warehouse SQL

Azure Data Factory offre une méthode entièrement gérée pour organiser le transfert de données et l’exécution des procédures stockées sur SQL Data Warehouse.  Cela vous permettra plus facilement configuration correcte et planifiez extraire transformer, puis chargement des procédures complexes avec SQL Data Warehouse. Pour une vue d’ensemble plus complète des Azure Data Factory, voir la [documentation Azure Data Factory][].

## <a name="data-movement"></a>Déplacement de données

Azure Data Factory permet le déplacement de données entre les différents services Azure et de sources locales.  Globalement, Active l’intégration avec Azure Data Factory prend en charge le déplacement des données vers et à partir des emplacements suivants :

+ Stockage d’objets blob Azure
+ Base de données SQL Azure
+ En local SQL Server
+ SQL Server sur IaaS

Pour plus d’informations sur la façon de configurer un données activité de copie voir [copier des données avec Azure Data Factory][]

## <a name="stored-procedures"></a>Procédures stockées
 Dans la même manière, qu'il peut être utilisé pour planifier le transfert de données, Azure Data Factory peut également servir à organiser l’exécution des procédures stockées.  Ceci permet pipelines plus complexes à créer et étend la possibilité d’usine de données Azure pour tirer parti de la puissance de calcul de Data Warehouse SQL.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de l’intégration, voir [vue d’ensemble de l’intégration de SQL Data Warehouse][].
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->

[Copier des données avec Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Vue d’ensemble du développement Data Warehouse SQL]: ./sql-data-warehouse-overview-develop.md
[Présentation de l’intégration Data Warehouse SQL]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentation présentée sous d’usine données Azure]:https://azure.microsoft.com/documentation/services/data-factory/

