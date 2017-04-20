<properties
   pageTitle="Migrer votre entrepôt de données SQL Azure existant vers le stockage premium | Microsoft Azure"
   description="Instructions pour la migration un SQL Data Warehouse existant vers le stockage premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migration vers des détails sur le stockage Premium
Data Warehouse SQL introduite récemment [Stockage Premium prévisibilité performances supérieur][].  Nous sommes maintenant prêts à migrer Data Warehouse existants actuellement sur le stockage Standard au stockage Premium.  Poursuivez votre lecture pour plus d’informations sur quand et comment les migrations automatiques se produisent et comment effectuer une migration automatique si vous préférez contrôler quand le temps d’arrêt se produit.

Si vous avez plusieurs Data Warehouse, utilisez la [planification de la migration automatique][] ci-dessous pour déterminer quand il sera également migré.

## <a name="determine-storage-type"></a>Déterminer le type de stockage
Si vous avez créé un Data Warehouse avant les dates ci-dessous, vous utilisez actuellement un stockage Standard.  Chaque Data Warehouse sur le stockage Standard qui sont couvertes par la migration automatique a une notification en haut de la cuillère Data Warehouse dans le [Portail Azure][] indiquant «*un à venir mise à niveau vers le stockage premium nécessite une panne.  En savoir plus ->*. »

| **Région**          | **DW créée avant cette Date**   |
| :------------------ | :-------------------------------- |
| Australie est      | Stockage Premium n’est pas encore disponible |
| Australie sud-est. | 5 août 2016                    |
| Brésil sud        | 5 août 2016                    |
| Canada Central      | 25 mai 2016                      |
| Canada Extrême-Orient         | 26 mai 2016                      |
| États-Unis centre          | 26 mai 2016                      |
| Moyen-Orient Chine          | Stockage Premium n’est pas encore disponible |
| Amérique du Nord Chine         | Stockage Premium n’est pas encore disponible |
| Asie de l’est           | 25 mai 2016                      |
| États-Unis Extrême-Orient             | 26 mai 2016                      |
| US2 Extrême-Orient            | 27 mai 2016                      |
| Inde centrale       | 27 mai 2016                      |
| Inde sud         | 26 mai 2016                      |
| Inde ouest          | Stockage Premium n’est pas encore disponible |
| Orient Japon          | 5 août 2016                    |
| Japon ouest          | Stockage Premium n’est pas encore disponible |
| États-Unis centre nord    | Stockage Premium n’est pas encore disponible |
| Europe du Nord        | 5 août 2016                    |
| États-Unis centre sud    | 27 mai 2016                      |
| Pays d’Asie du Sud-est      | 24 mai 2016                      |
| Europe occidentale         | 25 mai 2016                      |
| États-Unis centre ouest     | 26 août 2016                   |
| États-Unis Ouest             | 26 mai 2016                      |
| US2 ouest            | 26 août 2016                   |

## <a name="automatic-migration-details"></a>Détails de la migration automatique
Par défaut, nous allons migrer votre base de données à votre place pendant 6 h et 6 h en heure locale de votre région au cours de la [planification de la migration automatique][] en dessous.  Votre Data Warehouse existant sera inutilisable lors de la migration.  Nous estimons que la migration prendra environ une heure par To de stockage par Data Warehouse.  Nous garantit également que vous ne seront pas imputés au cours de tout ou partie de la migration automatique.

> [AZURE.NOTE] Vous ne serez pas en mesure d’utiliser votre Data Warehouse existante lors de la migration.  Une fois la migration terminée, votre Data Warehouse sera remis en ligne.

Les détails ci-dessous sont étapes que Microsoft prend en votre nom pour effectuer la migration et ne nécessite pas l’intervention de votre part.  Dans cet exemple, supposons que votre DW existant sur le stockage Standard se nomme actuellement « MyDW. »

1.  Microsoft renomme « MyDW » à « MyDW_DO_NOT_USE_ [horodatage] »
2.  Microsoft s’interrompt « MyDW_DO_NOT_USE_ [horodatage] ».  Pendant ce temps, une sauvegarde est considérée.  Vous pouvez constater plusieurs pause/CV si nous rencontrer des problèmes pendant le processus.
3.  Microsoft crée un nouveau DW nommé « MyDW » sur le stockage Premium à partir de la sauvegarde effectuée à l’étape 2.  « MyDW » n’apparaîtront pas jusqu'à une fois la restauration terminée.
4.  Une fois la restauration terminée, « MyDW » renvoie à la mêmes DWUs et l’état en pause ou active qu'elle était définie avant la migration.
5.  Une fois la migration terminée, Microsoft supprime « MyDW_DO_NOT_USE_ [horodatage] »
    
> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  Audit au niveau de la base de données doit être réactivé
>   -  Règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas affectés.

### <a name="automatic-migration-schedule"></a>Planification de la migration automatique
Migrations automatiques se produisent à partir de 6 pm – 6 h (heure locale par région) au cours de la planification d’une panne suivante.

| **Région**          | **Date de début estimée**     | **Date de fin estimée**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Australie est      | Pas encore déterminé           | Pas encore déterminé           |
| Australie sud-est. | 10 août 2016              | 24 août 2016              |
| Brésil sud        | 10 août 2016              | 24 août 2016              |
| Canada Central      | 23 juin 2016                | 1 juillet 2016                 |
| Canada Extrême-Orient         | 23 juin 2016                | 1 juillet 2016                 |
| États-Unis centre          | 23 juin 2016                | 4 juillet 2016                 |
| Moyen-Orient Chine          | Pas encore déterminé           | Pas encore déterminé           |
| Amérique du Nord Chine         | Pas encore déterminé           | Pas encore déterminé           |
| Asie de l’est           | 23 juin 2016                | 1 juillet 2016                 |
| États-Unis Extrême-Orient             | 23 juin 2016                | 11 juillet 2016                |
| US2 Extrême-Orient            | 23 juin 2016                | 8 juillet 2016                 |
| Inde centrale       | 23 juin 2016                | 1 juillet 2016                 |
| Inde sud         | 23 juin 2016                | 1 juillet 2016                 |
| Inde ouest          | Pas encore déterminé           | Pas encore déterminé           |
| Orient Japon          | 10 août 2016              | 24 août 2016              |
| Japon ouest          | Pas encore déterminé           | Pas encore déterminé           |
| États-Unis centre nord    | Pas encore déterminé           | Pas encore déterminé           |
| Europe du Nord        | 10 août 2016              | 31 août 2016              |
| États-Unis centre sud    | 23 juin 2016                | 2 juillet 2016                 |
| Pays d’Asie du Sud-est      | 23 juin 2016                | 1 juillet 2016                 |
| Europe occidentale         | 23 juin 2016                | 8 juillet 2016                 |
| États-Unis centre ouest     | 14 août 2016              | 31 août 2016              |
| États-Unis Ouest             | 23 juin 2016                | 7 juillet 2016                 |
| US2 ouest            | 14 août 2016              | 31 août 2016              |

## <a name="self-migration-to-premium-storage"></a>Migration automatique au stockage Premium
Si vous voulez contrôler quand votre temps d’arrêt se produit, vous pouvez utiliser les étapes suivantes pour migrer un Data Warehouse existant sur le stockage Standard au stockage Premium.  Si vous choisissez de migrer automatique, vous devez effectuer la migration automatique avant le début de la migration automatique dans cette zone pour éviter tout risque de la migration automatique à l’origine d’un conflit (reportez-vous à la [planification de la migration automatique][]).

### <a name="self-migration-instructions"></a>Instructions de migration automatique
Si vous souhaitez contrôler la période d’indisponibilité, vous pouvez migrer automatiquement votre Data Warehouse à l’aide de sauvegarde et de restauration.  La partie restauration de la migration doit prendre environ une heure par To de stockage par DW.  Si vous souhaitez conserver le même nom une fois la migration terminée, suivez les étapes pour les [étapes à suivre pour renommer lors de la migration][]. 

1.  [Pause][] votre DW qui prend une sauvegarde automatique
2.  [Restaurer][] à partir de votre instantané le plus récent
3.  Supprimer votre DW existant sur le stockage Standard. **Si vous ne parvenez pas à effectuer cette étape, les deux DWs vous sera facturée.**

> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  Audit au niveau de la base de données doit être réactivé
>   -  Règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas affectés.

#### <a name="optional-steps-to-rename-during-migration"></a>Facultatif : étapes à suivre pour renommer lors de la migration 
Deux bases de données du même serveur logique ne peuvent pas porter le même nom. Data Warehouse SQL prend désormais en charge la possibilité de renommer un Data Warehouse.

Dans cet exemple, supposons que votre DW existant sur le stockage Standard se nomme actuellement « MyDW. »

1.  Renommer « MyDW » à l’aide de la commande modifier la base de données qui suit pour qu’elle soit comme « MyDW_BeforeMigration ».  Cette commande supprime toutes les transactions existantes et doit être effectuée dans la base de données principale pour réussir.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Pause][] « MyDW_BeforeMigration » qui prend une sauvegarde automatique
3.  [Restaurer][] à partir de votre plus récente une base de données avec le nom que vous avez utilisé pour avoir d'instantané (ex : « MyDW »)
4.  Supprimer « MyDW_BeforeMigration ».  **Si vous ne parvenez pas à effectuer cette étape, les deux DWs vous sera facturée.**

> [AZURE.NOTE] Ces paramètres ne s’appliquent pas dans le cadre de la migration :
> 
>   -  Audit au niveau de la base de données doit être réactivé
>   -  Règles de pare-feu au niveau de la **base de données** doivent être a.  Règles de pare-feu au niveau du **serveur** ne sont pas affectés.

## <a name="next-steps"></a>Étapes suivantes
Avec la modification apportée au stockage de Premium, nous avons également augmenté le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre Data Warehouse.  Pour optimiser les avantages de cette modification, nous vous recommandons de reconstruire votre index Columnstore groupé en utilisant le script suivant.  Le script ci-dessous fonctionne en forcer certaines de vos données existantes pour les objets BLOB supplémentaires.  Si vous ne prenez aucune action, les données seront naturellement redistribuer au fil du temps lors du chargement de davantage de données dans vos tableaux Data Warehouse.

**Conditions préalables :**

1.  Data Warehouse doit s’exécuter avec 1 000 DWUs ou version ultérieure (voir [échelle cluster power][])
2.  Utilisateur exécutant le script doit être dans le [rôle mediumrc][] ou version ultérieure
    1.  Pour ajouter un utilisateur à ce rôle, exécutez la commande suivante : 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Si vous rencontrez des problèmes avec votre Data Warehouse, [créer une demande d’assistance][] et le référence « Stockage de Migration à Premium » comme étant la cause possible.

<!--Image references-->

<!--Article references-->
[planification de la migration automatique]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[créer une demande d’assistance]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restaurer]: sql-data-warehouse-restore-database-portal.md
[étapes à suivre pour renommer lors de la migration]: #optional-steps-to-rename-during-migration
[échelle cluster power]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[rôle mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Stockage Premium supérieure prévisibilité de performances]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portail Azure]: https://portal.azure.com
