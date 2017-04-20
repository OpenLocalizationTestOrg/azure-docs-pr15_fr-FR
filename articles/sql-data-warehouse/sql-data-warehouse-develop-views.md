<properties
   pageTitle="Affichages dans Data Warehouse SQL | Microsoft Azure"
   description="Conseils pour l’utilisation d’affichages Transact-SQL Azure SQL Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Affichages dans Data Warehouse SQL

Affichages sont particulièrement utiles dans Data Warehouse SQL. Ils peuvent être utilisés dans un certain nombre de différentes façons d’améliorer la qualité de votre solution.  Cet article présente quelques exemples d’enrichir votre solution avec les vues, ainsi que les limites qui doivent être prises en compte.

> [AZURE.NOTE] Syntaxe pour `CREATE VIEW` n’est pas mentionnés dans cet article. Reportez-vous à l’article [Créer un affichage][] sur MSDN pour ces informations de référence.

## <a name="architectural-abstraction"></a>Abstraction architecturale
Un modèle d’application très courante consiste à recréer les tables à l’aide de créer tableau en tant que sélectionner (SACT) suivi d’un objet renommer motif pendant le chargement de données.

L’exemple suivant ajoute des enregistrements de nouvelle date à une dimension de date. Remarque un nouveau tableau, DimDate_New, tout d’abord la création et comment ensuite renommé pour remplacer la version d’origine de la table.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Toutefois, cette approche peut générer des tableaux qui apparaissent et disparaissent de l’affichage d’un utilisateur ainsi que les messages d’erreur « la table n’existe pas ». Les vues peuvent servir à fournir aux utilisateurs un calque présentation cohérent tandis que les objets sous-jacents sont renommés. En permettant aux utilisateurs l’accès aux données via un affichages, signifie que les utilisateurs ne doivent disposer de visibilité des tables sous-jacentes. Il fournit une expérience utilisateur cohérente tandis que s’assurer que les données d’entrepôt concepteurs puisse évoluer le modèle de données et optimiser les performances à l’aide de SACT pendant le processus de chargement de données.    

## <a name="performance-optimization"></a>Optimisation des performances
Affichages peuvent également être utilisés pour appliquer des jointures performances optimisé entre les tables. Par exemple, un affichage pouvez incorporer une clé de répartition redondantes dans le cadre des critères jonction pour réduire le déplacement de données.  Avantage d’une vue peuvent forcer une requête spécifique ou un indicateur de jointure. Utilisation d’affichages de cette manière garantit que les jointures sont toujours exécutées de façon optimale éviter qu’il soit nécessaire pour les utilisateurs de se rappeler la construction correcte pour les jointures.

## <a name="limitations"></a>Limitations
Affichages dans SQL Data Warehouse sont des métadonnées uniquement.  Par conséquent les options suivantes ne sont pas disponibles :

-   Il n’existe aucune option de liaison de schéma
-   Tables de base ne peut pas être mis à jour via l’affichage
-   Impossible de créer des vues sur tables temporaires
-   Il n’existe aucune prise en charge pour le développer / conseils NOEXPAND
-   Il n’existe aucune vue indexés dans Data Warehouse SQL


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].
Pour `CREATE VIEW` syntaxe, consultez [Créer un affichage][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble du développement Data Warehouse SQL]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CRÉER L’AFFICHAGE]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
