<properties
    pageTitle="OLTP en mémoire améliore les performances de txn SQL | Microsoft Azure"
    description="Adopter en mémoire OLTP pour améliorer les performances de transactions dans une base de données SQL existante."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Utiliser en mémoire OLTP (preview) pour améliorer les performances de votre application dans la base de données SQL

[OLTP en mémoire](sql-database-in-memory.md) peut être utilisé pour améliorer les performances de charge de travail OLTP dans [Premium](sql-database-service-tiers.md) les bases de données SQL Azure sans augmenter le niveau de performance.

Suivez ces étapes pour adoptent OLTP en mémoire dans votre base de données existante.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Étape 1 : Vérifiez que votre base de données Premium prend en charge OLTP en mémoire

Bases de données Premium créées dans novembre 2015 ou version ultérieure prennent en charge la fonctionnalité de mémoire. Vous pouvez vérifier si votre base de données Premium prend en charge la fonctionnalité en mémoire en exécutant l’instruction Transact-SQL suivante. En mémoire est prise en charge si le résultat retourné est de 1 (pas 0) :

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* est l’abréviation de *Traitement des transactions extrême*

Si votre base de données existante doit être déplacé vers une nouvelle base de données V12 Premium, vous pouvez utiliser les techniques suivantes à exporter, puis importer vos données.

#### <a name="export-steps"></a>Étapes d’exportation

Exporter votre base de données de production vers un à DOS à l’aide de des options :

- La fonctionnalité [Exporter](sql-database-export.md) dans le [portail](https://portal.azure.com/).

- La fonctionnalité **Application exportation de la couche de données** dans un [SSMS.exe à jour](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. Dans l' **Explorateur d’objets**, développez le nœud **bases de données** .
 2. Clic droit sur le nœud de votre base de données.
 3. Cliquez sur **tâches** > **exportation de l’Application de couche données**.
 4. Utiliser la fenêtre de l’Assistant qui s’affiche.


#### <a name="import-steps"></a>Étapes d’importation

Importer l’à DOS dans une nouvelle base de données Premium.

1. Dans le [portail](https://portal.azure.com/)d' Azure,
 - Accédez au serveur.
 - Sélectionnez l’option [Importer base de données](sql-database-import.md) .
 - Sélectionnez une prime tarifs niveau.

2. SSMS permet d’importer l’à DOS :
 - Dans l' **Explorateur d’objets**, cliquez sur le nœud de **bases de données** .
 - Cliquez sur **Importer des données de couche Application**.
 - Utiliser la fenêtre de l’Assistant qui s’affiche.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Étape 2 : Identifier les objets pour migrer vers OLTP en mémoire

SSMS inclut un rapport de **Présentation de l’analyse Transaction performances** que vous pouvez exécuter sur une base de données avec une charge de travail actif. Le rapport identifie les tables et les procédures stockées qui sont des candidats pour la migration vers OLTP en mémoire.

Dans SSMS pour générer le rapport :
- Dans l' **Explorateur d’objets**, cliquez sur le nœud de votre base de données.
- Cliquez sur **rapports** > **rapports Standard** > **Transaction performances présentation de l’analyse**.

Pour plus d’informations, voir [détermination si une Table ou stockées procédure doivent être portés à OLTP en mémoire](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Étape 3 : Créer une base de données de test comparable

Supposons que le rapport indique votre base de données a une table qui peuvent bénéficier d’être converti en tableau mémoire optimisée. Nous vous recommandons de commencer par tester pour confirmer l’indication en testant.

Vous avez besoin d’une copie de test de votre base de données de production. La base de données de test doit être au même niveau de niveau de service en tant que votre base de données de production.

Pour faciliter le test, redéfinir certains votre base de données de test comme suit :

1. Se connecter à la base de données de test à l’aide de SSMS.

2. Pour éviter de l’option avec (instantané) dans les requêtes, définissez l’option de base de données comme indiqué dans l’instruction T-SQL suivante :
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Étape 4 : Migrer des tables

Vous devez créer et remplir une copie mémoire optimisée de la table que vous souhaitez tester. Vous pouvez le créer à l’aide de des options :

- L’Assistant pratique l’optimisation de la mémoire dans SSMS.
- Manuel T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Assistant d’optimisation de mémoire dans SSMS

Pour utiliser cette option de migration :

1. Se connecter à la base de données de test avec SSMS.

2. Dans l' **Explorateur d’objets**, avec le bouton droit sur la table, puis cliquez sur **Gestionnaire d’optimisation mémoire**.
 - L’Assistant **Table mémoire optimiseur Advisor** s’affiche.

3. Dans l’Assistant, cliquez sur **validation de Migration** (ou le bouton **suivant** ) pour voir si la table comporte des fonctionnalités non prises en charge qui sont non prises en charge dans les tables de mémoire optimisée. Pour plus d’informations, voir :
 - *Liste de vérification de l’optimisation de mémoire* dans le [Gestionnaire d’optimisation mémoire](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Constructions transact-SQL non pris en charge par OLTP en mémoire](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migration vers OLTP en mémoire](http://msdn.microsoft.com/library/dn247639.aspx).

4. Si la table ne comporte aucuns fonctionnalités non prises en charge, le conseiller peut effectuer le schéma réel et la migration de données pour vous.


#### <a name="manual-t-sql"></a>Manuel T-SQL

Pour utiliser cette option de migration :

1. Se connecter à votre base de données de test à l’aide de SSMS (ou un utilitaire similaire).

2. Obtenir le script T-SQL complète pour votre tableau et ses index.
 - Dans SSMS, cliquez sur le nœud de votre tableau.
 - Cliquez sur **Table de Script en tant que** > **créer pour** > **nouvelle fenêtre de requête**.

3. Dans la fenêtre de script, ajoutez WITH (MEMORY_OPTIMIZED = ON) à l’instruction CREATE TABLE.

4. S’il existe un index groupées, modifiez-le en NONCLUSTERED.

5. Renommer la table existante à l’aide de SP_RENAME.

6. Créer la nouvelle copie mémoire optimisée de la table en exécutant le script CREATE TABLE modifié.

7. Copiez les données dans votre table mémoire optimisée à l’aide d’insérer... SÉLECTIONNEZ * DANS :
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Étape 5 (facultatif) : migrer les procédures stockées

La fonctionnalité en mémoire pouvez également modifier une procédure stockée pour améliorer les performances.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considérations sur les procédures stockées compilées en mode natif

Une procédure stockée en mode natif compilée doit avoir les options suivantes dans sa clause T-SQL avec :

- NATIVE_COMPILATION

- SCHÉMA : ce qui signifie que les tables de la procédure stockée ne peut pas contenir les définitions de colonne modifiées d’une manière qui affecte la procédure stockée, à moins que vous déposez la procédure stockée.


Un module natif doit utiliser une grands [blocs atomiques](http://msdn.microsoft.com/library/dn452281.aspx) pour la gestion des transactions. Il n’y a aucun rôle pour une TRANSACTION explicite commencer, ou pour TRANSACTION de restauration. Si votre code détecte une violation d’une règle d’entreprise, il peut mettre fin à la bloc atomique avec une instruction [lever](http://msdn.microsoft.com/library/ee677615.aspx) .


### <a name="typical-create-procedure-for-natively-compiled"></a>Classique CREATE PROCEDURE pour compilé en mode natif

Généralement le T-SQL pour créer une procédure stockée en mode natif compilée est semblable au modèle suivant :

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Pour TRANSACTION_ISOLATION_LEVEL, instantané est la valeur la plus courante pour la procédure stockée compilée de manière native. Toutefois, un sous-ensemble des autres valeurs sont également pris en charge :
 - LECTURE RENOUVELABLE
 - SÉRIALISABLE


- La valeur de langue doit être présente dans la vue sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Comment migrer une procédure stockée

Les étapes de migration sont :


1. Obtenir le script CREATE PROCEDURE à la procédure stockée interprété normal.

2. Réécriture son en-tête pour faire correspondre le modèle précédent.

3. Vérifier si la procédure stockée code T-SQL utilise des fonctionnalités qui ne sont pas prises en charge pour les procédures stockées compilées en mode natif. Implémenter des solutions si nécessaire.
 - Pour plus d’informations, voir [Problèmes de Migration pour en mode natif compilé les procédures stockées](http://msdn.microsoft.com/library/dn296678.aspx).

4. Renommez l’ancienne procédure stockée à l’aide de SP_RENAME. Ou simplement le faire glisser.

5. Exécutez le script CREATE PROCEDURE T-SQL modifié.


## <a name="step-6-run-your-workload-in-test"></a>Étape 6 : Exécuter votre charge de travail de test

Exécuter une charge de travail dans votre base de données de test similaire à la charge de travail s’exécute dans votre base de données de production. Cela doit faire apparaître le gain de performances obtenu par l’utilisation de la fonctionnalité de mémoire dans des tables et des procédures stockées.

Les attributs principaux de la charge de travail sont :

- Nombre de connexions simultanées.

- Rapport de lecture/écriture.


Pour personnaliser et exécuter la charge de travail de test, envisagez d’utiliser l’outil de ostress.exe pratique, illustré [ici](sql-database-in-memory.md).


Pour réduire la latence du réseau, exécutez votre test dans la même région géographique Azure où se trouve la base de données.


## <a name="step-7-post-implementation-monitoring"></a>Étape 7 : Après l’implémentation surveillance

Vous pouvez contrôler les effets de performances de vos mises en œuvre en mémoire en production :

- [Stockage en mémoire moniteur](sql-database-in-memory-oltp-monitoring.md).

- [Base de données SQL Azure à l’aide des vues de gestion dynamiques de surveillance](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Liens connexes

- [En mémoire OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Présentation des procédures stockées compilées en mode natif](http://msdn.microsoft.com/library/dn133184.aspx)

- [Gestionnaire d’optimisation mémoire](http://msdn.microsoft.com/library/dn284308.aspx)

